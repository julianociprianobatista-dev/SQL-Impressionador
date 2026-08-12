# 📊 Funções de Agregação em SQL Server

Neste arquivo guardo minhas consultas de agregação — funções que resumem um conjunto de linhas em um único valor.

`SUM`, `COUNT`, `MAX`, `MIN`, `AVG`

> **Base de dados de referência:** as tabelas usadas aqui (`FactSales`, `DimProduct`, `DimCustomer`) pertencem ao schema do **ContosoRetailDW**.

---

## 1. Soma de Valores (`SUM`)

Calcula o volume total de itens vendidos e o total de itens devolvidos em toda a tabela de vendas (`FactSales`).

```sql
-- 1. Visualizar amostra dos dados
SELECT TOP (100)
    *
FROM
    FactSales

-- 2. Calcular os totais agregados
SELECT
    SUM(SalesQuantity)  AS [TOTAL VENDIDO],
    SUM(ReturnQuantity) AS [TOTAL DEVOLVIDO]
FROM
    FactSales
```

## 2. Contagem de Registros (`COUNT`)

Conta o total de registros na tabela de produtos, destacando a diferença entre contar todas as linhas e contar valores de uma coluna específica.

```sql
-- Contagem total da tabela (inclui nulos)
SELECT
    COUNT(*) AS [TotalLinhas]
FROM
    DimProduct

-- Contagem por coluna (ignora valores NULL, se existirem na coluna)
SELECT
    COUNT(ProductName) AS [QtdProdutos]
FROM
    DimProduct
```

> ⚠️ **Cuidado** ao passar uma coluna dentro de `COUNT(nome_coluna)`, pois valores `NULL` são ignorados na contagem. Para obter a quantidade real de linhas da tabela, prefira `COUNT(*)`.

## 3. Contagem de Valores Distintos (`COUNT` + `DISTINCT`)

Conta quantas marcas **únicas** existem na tabela de produtos, desconsiderando as repetições de marca entre os registros.

```sql
SELECT
    COUNT(DISTINCT BrandName) AS [QtdMarcasUnicas]
FROM
    DimProduct
```

> 💡 **Dica:** o uso do `DISTINCT` dentro do `COUNT()` garante que cada marca seja contada apenas uma vez, independentemente de quantos produtos estejam associados a ela na tabela.

## 4. Valores Máximo e Mínimo (`MAX` / `MIN`)

Encontra os valores extremos (máximo e mínimo) da coluna de custo unitário (`UnitCost`) na tabela de produtos.

```sql
SELECT
    MAX(UnitCost) AS [CustoMaximo],
    MIN(UnitCost) AS [CustoMinimo]
FROM
    DimProduct
```

> 💡 **Dica:** as funções `MAX()` e `MIN()` também podem ser aplicadas em colunas de datas, para encontrar o registro mais recente ou o mais antigo do banco de dados.

## 5. Média Salarial Anual (`AVG`)

Calcula a média da renda anual (`YearlyIncome`) dos clientes cadastrados na tabela `DimCustomer`.

```sql
SELECT
    AVG(YearlyIncome) AS [MediaSalarioAnual]
FROM
    DimCustomer
```

> 💡 **Dica:** a função `AVG()` calcula apenas a média dos valores não nulos. Registros com `NULL` são ignorados no cálculo, inclusive no divisor da média.

## 6. Agrupamento com Contagem (`GROUP BY` + `COUNT`)

Conta quantos produtos existem por marca, agrupando os resultados pela coluna `BrandName`.

```sql
-- Visualizar amostra dos dados
SELECT TOP (100)
    *
FROM
    DimProduct

-- Contar produtos por marca
SELECT
    BrandName AS [NomeDaMarca],
    COUNT(*)  AS [QtdTotal]
FROM
    DimProduct
GROUP BY
    BrandName
```

## 7. Agrupamento com Soma (`GROUP BY` + `SUM`)

Soma o total de funcionários por tipo de loja.

```sql
-- Visualizar amostra dos dados
SELECT TOP (100)
    *
FROM
    DimStore

-- Somar funcionários por tipo de loja
SELECT
    StoreType,
    SUM(EmployeeCount) AS [TotalFuncionarios]
FROM
    DimStore
GROUP BY
    StoreType
```

## 8. Agrupamento com Média (`GROUP BY` + `AVG`)

Calcula o custo médio unitário por marca.

```sql
-- Visualizar amostra dos dados
SELECT TOP (100)
    *
FROM
    DimProduct

-- Calcular custo médio por marca
SELECT
    BrandName,
    AVG(UnitCost) AS [CustoMedio]
FROM
    DimProduct
GROUP BY
    BrandName
```

## 9. Agrupamento com Valor Máximo (`GROUP BY` + `MAX`)

Encontra o maior preço unitário por classe de produto (`ClassName`).

```sql
-- Visualizar amostra dos dados
SELECT TOP (100)
    *
FROM
    DimProduct

-- Encontrar o preço máximo por classe
SELECT
    ClassName,
    MAX(UnitPrice) AS [PrecoMaximo]
FROM
    DimProduct
GROUP BY
    ClassName
```

> ⚠️ **Regra do `GROUP BY`:** toda coluna que aparece no `SELECT` e **não** está dentro de uma função de agregação (`COUNT`, `SUM`, `AVG`, `MAX`, `MIN`...) precisa obrigatoriamente estar listada no `GROUP BY`. Se você esquecer, o SQL Server recusa a consulta com erro de sintaxe/validação.

