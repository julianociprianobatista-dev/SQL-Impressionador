## 1. Função SUM

Consulta que calcula o volume total de itens vendidos e o total de itens devolvidos em toda a tabela de vendas (`FactSales`).

```sql
-- 1. Visualizar amostra dos dados
SELECT TOP (100) * 
FROM FactSales;

-- 2. Calcular os totais agregados
SELECT 
    SUM(SalesQuantity) AS 'TOTAL VENDIDO',
    SUM(ReturnQuantity) AS 'TOTAL DEVOLVIDO'
FROM FactSales;
```
## 2. Contagem de Registros (`COUNT`)

### Consulta utilizada para contar o total de registros na tabela de produtos, destacando a diferença entre contar todas as linhas e contar valores de uma coluna específica.

```sql
-- Contagem total da tabela (inclui nulos)
SELECT 
    COUNT(*) AS TotalLinhas
FROM DimProduct;

-- Contagem por coluna (ignora valores NULL se existirem na coluna)
SELECT 
    COUNT(ProductName) AS QtdProdutos
FROM DimProduct;

⚠️ Nota: Cuidado ao passar uma coluna dentro do COUNT(nome_coluna), pois valores nulos (NULL) são ignorados na contagem. Para obter a quantidade real de linhas da tabela, prefira utilizar COUNT(*)
```
## 3. Contagem de Valores Distintos (`COUNT` + `DISTINCT`)

### Consulta utilizada para contar quantas marcas **únicas** existem na tabela de produtos, desconsiderando as repetições de marca entre os registros.

```sql
SELECT 
    COUNT(DISTINCT BrandName) AS 'QtdMarcasUnicas'
FROM
     DimProduct

💡 Dica: O uso do DISTINCT dentro do COUNT() garante que cada marca seja contada apenas uma vez, independentemente de quantos produtos estejam associados a ela na tabela.
```
