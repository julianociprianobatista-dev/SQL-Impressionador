# 🔰 Comandos Básicos em SQL Server

 Neste aquivo guardo minhas consultas fundamentais de seleção e filtragem de dados

 (SELECT, WHERE, ORDER BY, TOP)
 ---

## 1. Consulta simples de colunas
```sql
 Este código seleciona toda a tabela
SELECT 
	*
FROM
	DimStore

 Este código seleciona 03 colunas da tabela
SELECT
	StoreKey,
	StoreName, 
	StorePhone 
from
	DimStore
```
## 2. Comandos SELECT TOP(N) e TOP(N) PERCENT: retorna as N primeiras linhas
```sql
 1. Crie um código que retona as 10 primeiras linhas da tabela de Produtos

SELECT TOP(10) * FROM DimProduct

 2. Retorna as 10% primeiras linhas da tabela de Clientes

SELECT TOP(10) PERCENT * FROM DimCustomer
```
## 3. Comando SELECT DISTINCT: Retorna os valores distintos de uma tabela
```sql
-- Retorne todas as linhas da tabela DimProduct

SELECT * FROM DimProduct

-- Retorne os valores distintos da coluna ColorNAme da tabela DimProduct

SELECT ColorName FROM DimProduct

-- Retorne os valores distintos da coluna ColorName da tabela DimProduct

SELECT DISTINCT ColorNAme FROM DimProduct

-- Retorne todas as linhas da tabela DimEmployee

SELECT * FROM DimEmployee

-- Retorne os valores distintos da coluna DetartmentName da tabela DimEmployee

SELECT
	DISTINCT DepartmentName
FROM
	DimEmployee
```



