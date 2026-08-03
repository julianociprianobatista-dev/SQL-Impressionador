# 🔰 Comandos Básicos em SQL Server

Neste arquivo guardo minhas consultas fundamentais de seleção e filtragem de dados.

`SELECT`, `WHERE`, `ORDER BY`, `TOP`

> **Base de dados de referência:** as tabelas usadas aqui (`DimStore`, `DimProduct`, `DimCustomer`, `DimEmployee`) pertencem ao schema do **ContosoRetailDW**.

---

## 1. Consulta simples de colunas

```sql
-- Seleciona todas as colunas da tabela
SELECT
    *
FROM
    DimStore

-- Seleciona 3 colunas específicas da tabela
SELECT
    StoreKey,
    StoreName,
    StorePhone
FROM
    DimStore
```

## 2. Comandos `TOP(N)` e `TOP(N) PERCENT`: retorna as N primeiras linhas

```sql
-- 1. Retorna as 10 primeiras linhas da tabela de Produtos
SELECT TOP (10) * FROM DimProduct

-- 2. Retorna as 10% primeiras linhas da tabela de Clientes
SELECT TOP (10) PERCENT * FROM DimCustomer
```

## 3. Comando `SELECT DISTINCT`: retorna os valores distintos de uma coluna

```sql
-- Retorna todas as linhas da tabela DimProduct
SELECT * FROM DimProduct

-- ❌ Sem DISTINCT: retorna a coluna ColorName com valores repetidos
SELECT ColorName FROM DimProduct

-- ✅ Com DISTINCT: retorna apenas os valores únicos da coluna ColorName
SELECT DISTINCT ColorName FROM DimProduct

-- Retorna todas as linhas da tabela DimEmployee
SELECT * FROM DimEmployee

-- Retorna os valores distintos da coluna DepartmentName da tabela DimEmployee
SELECT
    DISTINCT DepartmentName
FROM
    DimEmployee
```

## 4. Comando `AS`: renomeando colunas (aliasing)

Seleciona 3 colunas da tabela `DimProduct` (`ProductName`, `BrandName`, `ColorName`) e renomeia para `Produto`, `Marca` e `Cor`.

```sql
SELECT
    ProductName AS [Produto],
    BrandName   AS [Marca],
    ColorName   AS [Cor]
FROM
    DimProduct
```

## 5. Seleção com Limite (TOP), Aliases e Ordenação Múltipla

Consulta que retorna os 10 produtos de maior custo da tabela `DimProduct`, renomeando as colunas e aplicando ordenação secundária por peso.

```sql
SELECT TOP (10)
    ProductName AS [PRODUTO],
    UnitCost    AS [PREÇO DE CUSTO],
    Weight      AS [PESO]
FROM
    DimProduct
ORDER BY
    UnitCost DESC,
    Weight DESC
```

## 6. Filtro de Datas com a Cláusula `WHERE`

Consulta para identificar todos os clientes que nasceram a partir de 31/12/1970, ordenando do mais recente para o mais antigo.

```sql
SELECT
    *
FROM
    DimCustomer
WHERE
    BirthDate >= '1970-12-31'
ORDER BY
    BirthDate DESC
```

## 7. Filtros por Texto Exato (`WHERE`)

Exemplos de filtragem em colunas de texto para buscar marcas e cores específicas na tabela `DimProduct`.

```sql
-- Produtos da marca Fabrikam
SELECT
    *
FROM
    DimProduct
WHERE
    BrandName = 'Fabrikam'

-- Produtos na cor preta (Black)
SELECT
    *
FROM
    DimProduct
WHERE
    ColorName = 'Black'
```

## 8. Ordenação por Quantidade de Funcionários

Consulta que traz as 100 primeiras lojas, ordenadas de forma decrescente pelo número total de funcionários.

```sql
SELECT TOP (100)
    *
FROM
    DimStore
ORDER BY
    EmployeeCount DESC
```




