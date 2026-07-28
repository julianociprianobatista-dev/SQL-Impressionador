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