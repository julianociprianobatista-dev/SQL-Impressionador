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
## 9. WHERE mais AND
```sql
SELECT * FROM DimProduct
WHERE BrandName = 'FABRIKAM' AND ColorName = 'BLACK'
```
## 10. WHERE mais OR
```sql
SELECT * FROM DimProduct
WHERE BrandName = 'CONTOSO' OR ColorName = 'WHITE'

-- OBS: NÃO USAMOS OR PARA COLUNAS DIFERENTES
-- EXERCICIO FILTRAR NA TABELA AS MARCAS CONTOSO OU FABRIKAM

SELECT * FROM DimProduct
WHERE BrandName = 'CONTOSO' OR BrandName = 'FABRIKAM'
```
## 11. WHERE mais NOT
```sql
SELECT * FROM DimEmployee
WHERE NOT DepartmentName = 'Marketing'
```
## 12. Exercicios de fixação: AND, OR e NOT
```sql
--1. SELECIONE TODAS AS LINHAS DA TABELA DIMEMPLOYEE DE FUNCIONÁRIOS DO SEXO FEMININO E DO DEPARTAMENTO DE FINANÇAS

SELECT * FROM DimEmployee
WHERE Gender = 'F' AND DepartmentName = 'Finance'

--2. SELECIONE TODAS AS LINHAS DA TABELA DIMPRODUCT DE PRODUTOS DA MARCA CONTOSO E DA COR VERMELHA E QUE TENHAM UN UNITPRICE MAIOR OU IGUAL A $100

SELECT * FROM DimProduct
WHERE BrandName = 'Contoso' AND ColorName = 'Red' AND UnitPrice >= 100

-- 3. SELECIONE TODAS AS LINHAS DA TABELA DIMPRODUCT COM PRODUTOS DA MARCA LITWARE OU DA MARCA FABRIKAM OU DA COR PRETA
SELECT * FROM DimProduct
WHERE BrandName = 'Litware' OR BrandName = 'Fabrikam' OR ColorName = 'Black'

--4. SELECIONE TODAS AS LINHAS DA TABELA DIMSALESTERRITORY ONDE O CONTINENTE É A EUROPA MAS O PAÍS NÃO É IGUAL A ITALIA
SELECT * FROM DimSalesTerritory
WHERE NOT SalesTerritoryCountry = 'ItalY' AND SalesTerritoryGroup = 'Europe'
```
## 13. Cuidados ao utilizar AND em conjunto com o OR
```sql
-- SELECIONE TODAS AS LINHAS DA TABELA DIMPRODUCT ONDE A COR DO PRODUTO PODE SER IGUAL A PRETO OU VERMELHO, MAS A MARCA DEVE SER OBRIGATORIAMENTE A FABRIKAM

SELECT * FROM DimProduct
WHERE ColorName = 'Black' OR ColorName = 'Red' AND BrandName = 'Fabrikam'

-- SQL NÃO CONSEGUE ENTENDER QUAIS FILTROS FAZER PRIMEIRO, TEMOS QUE GARANTIR COMO QUEREMOS QUE OS CRITERIOS SEJAM AVALIADOS.
--TEMOS QUE POR () ONDE QUEREMOS QUE O SQL FILTRE PRIMEIRO

SELECT * FROM DimProduct
WHERE (ColorName = 'Black' OR ColorName = 'Red') AND BrandName = 'Fabrikam'
```






