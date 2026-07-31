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
 Retorne todas as linhas da tabela DimProduct

SELECT * FROM DimProduct

 Retorne os valores distintos da coluna ColorNAme da tabela DimProduct

SELECT ColorName FROM DimProduct

 Retorne os valores distintos da coluna ColorName da tabela DimProduct

SELECT DISTINCT ColorNAme FROM DimProduct

 Retorne todas as linhas da tabela DimEmployee

SELECT * FROM DimEmployee

 Retorne os valores distintos da coluna DetartmentName da tabela DimEmployee

SELECT
	DISTINCT DepartmentName
FROM
	DimEmployee
```
## 4. Comando AS: Renomeando colunas (aliasing)
```sql
Selecione as 03 colunas da tabela DimProduct: ProductName, BrandName e ColorName e renomeie para Produto, Marca e Cor.

SELECT
	ProductName AS "Produto",
	BrandName AS "Marca",
	ColorName AS "Cor"
FROM
	DimProduct
```
## 5. Seleção com Limite (TOP), Aliases e Ordenação Múltipla

Consulta que retorna os 10 produtos de maior custo da tabela `DimProduct`, renomeando os nomes das colunas e aplicando ordenação secundária por peso.

```sql
SELECT TOP (10) 
    ProductName AS 'PRODUTO',
    UnitCost AS 'PREÇO DE CUSTO',
    Weight AS 'PESO'
FROM 
    DimProduct
ORDER BY 
    UnitCost DESC, 
    Weight DESC;
```
## 6. Filtro de Datas com a Cláusula WHERE
Consulta para identificar todos os clientes que nasceram a partir de 31 de dezembro de 1970, ordenando do mais recente para o mais antigo.
```sql
SELECT * FROM 
    DimCustomer
WHERE 
    BirthDate >= '1970-12-31'
ORDER BY 
    BirthDate DESC;
```





