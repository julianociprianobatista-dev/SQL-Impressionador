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

## 5. Funções de Arredondamento (`ROUND`, `FLOOR`, `CEILING`)
 
**`ROUND`** — arredonda um número para a quantidade de casas decimais informada:
 
```sql
SELECT ROUND(18.739130, 2) -- resultado: 18.740000 (arredonda para 2 casas)
```
 
**`ROUND` com truncamento** — o terceiro parâmetro `1` substitui o arredondamento por truncamento (simplesmente corta as casas excedentes, sem arredondar):
 
```sql
SELECT ROUND(18.739130, 3, 1) -- resultado: 18.739000 (trunca, não arredonda)
```
 
> 💡 `ROUND(valor, casas)` arredonda normalmente. `ROUND(valor, casas, 1)` trunca — o `1` no terceiro parâmetro é o que muda o comportamento. Qualquer valor diferente de zero no terceiro parâmetro ativa o truncamento.
 
**`FLOOR`** — arredonda sempre para baixo, retornando o maior inteiro menor ou igual ao valor:
 
```sql
SELECT FLOOR(18.739130) -- resultado: 18
```
 
**`CEILING`** — arredonda sempre para cima, retornando o menor inteiro maior ou igual ao valor:
 
```sql
SELECT CEILING(18.739130) -- resultado: 19
```
 
> 💡 **Resumindo:** `ROUND` segue a regra matemática padrão; `FLOOR` sempre vai para baixo (como "andar de baixo"); `CEILING` sempre vai para cima (como "teto"). Em cálculos financeiros, a escolha entre os três impacta diretamente o resultado — use com atenção.
 
 ## 6. Variáveis — Parte 1: aplicando desconto com `DECLARE` e `SET`
 
Variáveis permitem armazenar um valor temporário e reutilizá-lo em uma ou mais consultas. São declaradas com `DECLARE` e recebem valor com `SET`. No SQL Server, toda variável começa com `@`.
 
**Raciocínio do cálculo:**
- Preço original: `100`
- Desconto: `10% = 0.10`
- Preço com desconto: `100 * (1 - 0.10) = 90`
- Generalizado: `UnitPrice * (1 - @varDesconto)`
**Exercício:** aplicar 10% de desconto em todos os preços da tabela `DimProduct`.
 
```sql
-- Visualizar amostra da tabela
SELECT TOP (100) * FROM DimProduct
 
-- Declarar e atribuir a variável de desconto
DECLARE @varDesconto FLOAT
SET @varDesconto = 0.10
 
-- Aplicar o desconto na consulta
SELECT
    DimProduct.ProductKey                        AS [ID],
    DimProduct.ProductName                       AS [NomeDoProduto],
    DimProduct.UnitPrice                         AS [PrecoDoProduto],
    DimProduct.UnitPrice * (1 - @varDesconto)    AS [PrecoComDesconto]
FROM
    DimProduct
```
 
> 💡 **Vantagem da variável:** se o desconto mudar de 10% para 15%, basta alterar o valor no `SET` — a query inteira se atualiza automaticamente, sem precisar editar cada linha do `SELECT`.
 
## 7. Variáveis — Parte 2: filtrando por data com `DECLARE` e `UNION`
 
Variáveis de data são especialmente úteis em queries com `UNION` — evitam repetir o mesmo valor de filtro em cada bloco da consulta.
 
**Exercício:** retornar clientes e funcionários nascidos a partir de 01/01/1980, identificando o tipo de cada pessoa.
 
```sql
-- Declarar e atribuir a variável de data
DECLARE @varData DATETIME
SET @varData = '01/01/1980'
 
-- Clientes nascidos a partir da data
SELECT
    FirstName  AS [Nome],
    LastName   AS [Sobrenome],
    BirthDate  AS [Nascimento],
    'Cliente'  AS [Tipo]
FROM
    DimCustomer
WHERE
    BirthDate >= @varData
 
UNION
 
-- Funcionários nascidos a partir da data
SELECT
    FirstName      AS [Nome],
    LastName       AS [Sobrenome],
    BirthDate      AS [Nascimento],
    'Funcionário'  AS [Tipo]
FROM
    DimEmployee
WHERE
    BirthDate >= @varData
ORDER BY
    Nascimento
```
 
> 💡 Com a variável `@varData`, basta alterar o `SET` para mudar o filtro de data nos dois blocos do `UNION` de uma só vez. Sem a variável, seria necessário editar `'01/01/1980'` em cada `WHERE` separadamente — em queries com muitos blocos, isso aumenta o risco de inconsistência.
 
> ⚠️ O `ORDER BY` ao final de um `UNION` ordena o resultado combinado inteiro — não apenas o último bloco. Por isso ele fica fora e depois do último `SELECT`.
 
 