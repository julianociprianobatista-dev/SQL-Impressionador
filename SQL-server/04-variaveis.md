# 🧮 Variáveis e Manipulação de Dados em SQL Server

Neste arquivo guardo minhas consultas de manipulação de dados — variáveis, operações com números, textos e datas, funções de string, funções de data e funções condicionais.

`DECLARE`, `SET`, `CAST`, `CONVERT`, `DATEADD`, `DATEDIFF`, `IIF`, `CASE`

> **Base de dados de referência:** as tabelas usadas aqui pertencem ao schema do **ContosoRetailDW**.

---

## 1. Operações Básicas no `SELECT`

Antes de trabalhar com variáveis, é possível realizar operações diretamente no `SELECT` — com números, textos e datas.

**Retornando valores literais:**

```sql
SELECT 10         AS [Numero]
SELECT 'Juliano'  AS [Nome]
SELECT '30/12/1987' AS [Data]
```

**Operações com números:**

```sql
SELECT 10 + 20    AS [Soma]
SELECT 20 - 5     AS [Subtracao]
SELECT 31 * 40    AS [Multiplicacao]
SELECT 431.0 / 23 AS [Divisao]
```

> 💡 Use `431.0` (com `.0`) em vez de `431` na divisão — se os dois valores forem inteiros, o SQL Server retorna um inteiro truncado. Com `.0`, o resultado é decimal.

**Concatenação de texto:**

```sql
SELECT 'Juliano' + ' ' + 'Cipriano' AS [Nome]
```

> ⚠️ O separador entre os nomes deve ser `' '` (espaço entre aspas). Usar `''` (sem espaço) resulta em `JulianoCipriano` sem separação.

**Operações com datas:**

```sql
-- ❌ Isso gera erro: SQL Server não soma número a uma string de data diretamente
SELECT '20/06/2021' + 1 AS [Data]

-- ✅ Use DATEADD para somar dias a uma data
SELECT DATEADD(DAY, 1, '2021-06-20') AS [Data]
```

> ⚠️ Diferente do Excel, o SQL Server não converte automaticamente uma string como `'20/06/2021'` em data para operações aritméticas. Use sempre `DATEADD(intervalo, quantidade, data)` para manipular datas — veremos mais funções de data nas próximas seções.

## 2. Identificando o tipo de dado (`SQL_VARIANT_PROPERTY`)
 
A função `SQL_VARIANT_PROPERTY` permite inspecionar o tipo base de um valor. Útil para entender como o SQL Server interpreta cada dado antes de aplicar conversões.
 
```sql
-- Retornando valores para inspecionar
SELECT 10           AS [Numero]
SELECT 49.50        AS [Decimal]
SELECT 'Juliano'    AS [Nome]
SELECT '30/12/1987' AS [Data]
 
-- Verificando o tipo base de cada valor
SELECT SQL_VARIANT_PROPERTY(10,           'BaseType') -- int
SELECT SQL_VARIANT_PROPERTY(49.50,        'BaseType') -- numeric
SELECT SQL_VARIANT_PROPERTY('Marcos',     'BaseType') -- varchar
SELECT SQL_VARIANT_PROPERTY('30/12/1987', 'BaseType') -- varchar
```
 
> ⚠️ Repare que `'30/12/1987'` retorna `varchar`, não `datetime` — porque qualquer valor entre aspas simples é tratado como texto pelo SQL Server. Para que seja reconhecido como data, é preciso usar `CAST` ou `CONVERT` explicitamente (ver seção seguinte).
 
## 3. Convertendo tipos de dados (`CAST`)
 
`CAST` converte um valor de um tipo para outro. É essencial para operações que misturam tipos diferentes (como concatenar texto com número ou somar dias a uma data).
 
Tipos mais comuns: `INT` (inteiro), `FLOAT` (decimal), `VARCHAR` (texto), `DATETIME` (data e hora).
 
```sql
-- Verificando o tipo após conversão explícita
SELECT SQL_VARIANT_PROPERTY(CAST('20/06/2021' AS DATETIME), 'BaseType') -- datetime
 
-- Conversões numéricas
SELECT CAST(21.45 AS INT)   -- trunca para 21 (sem arredondamento)
SELECT CAST(21.45 AS FLOAT) -- mantém o decimal
SELECT CAST(18.7  AS FLOAT)
SELECT CAST('15.6' AS FLOAT) -- converte string numérica para decimal
 
-- Conversão de string para data
SELECT CAST('20/06/2021' AS DATETIME)
```
 
**Exercício 1:** juntar o texto `'O preço do pastel é:'` com o valor `30.99`.
 
```sql
-- CAST converte o número para VARCHAR antes de concatenar
SELECT 'O preço do pastel é: ' + CAST(30.99 AS VARCHAR(50))
```
 
**Exercício 2:** adicionar 1 dia à data `'20/06/2021'`.
 
```sql
-- CAST converte a string para DATETIME, permitindo a soma de dias
SELECT CAST('20/06/2021' AS DATETIME) + 1
```
 
> 💡 Após o `CAST` para `DATETIME`, o SQL Server permite somar inteiros diretamente — cada unidade equivale a 1 dia. Para intervalos maiores ou mais precisos (horas, minutos), use `DATEADD`.
 
## 4. Formatando valores para exibição (`FORMAT`)
 
`FORMAT` aplica uma máscara de formatação sobre números e datas, retornando sempre uma string formatada. Útil para exibição em relatórios.
 
**Números — formatos predefinidos:**
 
```sql
SELECT FORMAT(1000, 'N') -- separador de milhar + 2 casas decimais: 1.000,00
SELECT FORMAT(1000, 'G') -- formato geral: retorna o número sem alteração
```
 
**Números — máscara personalizada:**
 
```sql
SELECT FORMAT(123456789, '###-##-###') -- aplica máscara: 123-45-6789
```
 
**Datas:**
 
```sql
-- Formata como dia/mês por extenso/ano: 21/março/2021
SELECT FORMAT(CAST('21/03/2021' AS DATETIME), 'dd/MMMM/yyyy')
 
-- Retorna o dia da semana por extenso: domingo
SELECT FORMAT(CAST('21/03/2021' AS DATETIME), 'dddd')
```
 
**Exercício 3:** juntar o texto `'A data de validade é:'` com a data `17/abr/2022`.
 
```sql
SELECT 'A data de validade é: ' + FORMAT(CAST('17/04/2022' AS DATETIME), 'dd/MMM/yyyy')
```
 
> ⚠️ `FORMAT` sempre retorna `VARCHAR` — por isso é possível concatenar diretamente com texto. O `CAST` antes da data garante que a string seja interpretada como `DATETIME` antes de aplicar a formatação.
 