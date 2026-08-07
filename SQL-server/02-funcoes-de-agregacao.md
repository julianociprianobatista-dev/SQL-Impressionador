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