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