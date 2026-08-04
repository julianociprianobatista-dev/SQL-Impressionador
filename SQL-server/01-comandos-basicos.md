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
## 9. `WHERE` com `AND`

Retorna produtos da marca Fabrikam **e** da cor preta (as duas condições precisam ser verdadeiras).

```sql
SELECT
    *
FROM
    DimProduct
WHERE
    BrandName = 'Fabrikam' AND ColorName = 'Black'
```

## 10. `WHERE` com `OR`

Retorna produtos da marca Contoso **ou** da cor branca (basta uma das condições ser verdadeira).

```sql
SELECT
    *
FROM
    DimProduct
WHERE
    BrandName = 'Contoso' OR ColorName = 'White'
```

> ⚠️ É possível usar `OR` entre colunas diferentes, mas o resultado tende a ficar mais amplo do que o esperado — combine com cuidado. O uso mais comum de `OR` é dentro da **mesma coluna**, para checar múltiplos valores possíveis (esse caso evolui naturalmente para o operador `IN`, que veremos mais adiante).

**Exercício:** filtrar produtos das marcas Contoso ou Fabrikam.

```sql
SELECT
    *
FROM
    DimProduct
WHERE
    BrandName = 'Contoso' OR BrandName = 'Fabrikam'
```

## 11. `WHERE` com `NOT`

Retorna todos os funcionários que **não** pertencem ao departamento de Marketing.

```sql
SELECT
    *
FROM
    DimEmployee
WHERE
    NOT DepartmentName = 'Marketing'
```

> 💡 O equivalente mais usado no dia a dia é o operador `<>` (diferente de):
> ```sql
> SELECT * FROM DimEmployee WHERE DepartmentName <> 'Marketing'
> ```
> Os dois retornam o mesmo resultado. `NOT` costuma ser mais útil quando a condição a ser negada é composta (veja o exercício 4 abaixo).

## 12. Exercícios de fixação: `AND`, `OR` e `NOT`

**1.** Selecione todas as linhas da tabela `DimEmployee` de funcionárias do sexo feminino e do departamento de Finanças.

```sql
SELECT
    *
FROM
    DimEmployee
WHERE
    Gender = 'F' AND DepartmentName = 'Finance'
```

**2.** Selecione todas as linhas da tabela `DimProduct` de produtos da marca Contoso, da cor vermelha e com `UnitPrice` maior ou igual a $100.

```sql
SELECT
    *
FROM
    DimProduct
WHERE
    BrandName = 'Contoso' AND ColorName = 'Red' AND UnitPrice >= 100
```

**3.** Selecione todas as linhas da tabela `DimProduct` com produtos da marca Litware ou da marca Fabrikam ou da cor preta.

```sql
SELECT
    *
FROM
    DimProduct
WHERE
    BrandName = 'Litware' OR BrandName = 'Fabrikam' OR ColorName = 'Black'
```

**4.** Selecione todas as linhas da tabela `DimSalesTerritory` onde o continente é a Europa, mas o país não é igual a Itália.

```sql
SELECT
    *
FROM
    DimSalesTerritory
WHERE
    SalesTerritoryGroup = 'Europe' AND NOT (SalesTerritoryCountry = 'Italy')
```

## 13. Cuidados ao utilizar `AND` em conjunto com o `OR`

Selecione todas as linhas da tabela `DimProduct` onde a cor do produto pode ser preta ou vermelha, mas a marca deve ser obrigatoriamente Fabrikam.

```sql
-- ❌ Sem parênteses: o resultado não é o esperado
SELECT
    *
FROM
    DimProduct
WHERE
    ColorName = 'Black' OR ColorName = 'Red' AND BrandName = 'Fabrikam'
```

> ⚠️ Sem parênteses, o SQL aplica a ordem de precedência padrão — `AND` é avaliado antes de `OR` — o que pode não ser a lógica pretendida. Use `()` para deixar explícito o que deve ser avaliado primeiro, do jeito que você quer.

```sql
-- ✅ Com parênteses: agora sim, cor (preta OU vermelha) E marca Fabrikam
SELECT
    *
FROM
    DimProduct
WHERE
    (ColorName = 'Black' OR ColorName = 'Red') AND BrandName = 'Fabrikam'
```
## 14. `WHERE` com `IN`: alternativa ao `OR` múltiplo
 
Como vimos na seção 10, usar `OR` várias vezes na mesma coluna funciona, mas fica repetitivo e menos legível. O operador `IN` resolve isso, permitindo checar uma lista de valores possíveis em uma única condição.
 
Retorna produtos cuja cor esteja entre Prata, Azul, Vermelho ou Preto.
 
```sql
SELECT
    *
FROM
    DimProduct
WHERE
    ColorName IN ('Silver', 'Blue', 'Red', 'Black')
```
 
Retorna funcionários dos departamentos de Produção, Marketing ou Engenharia.
 
```sql
SELECT
    *
FROM
    DimEmployee
WHERE
    DepartmentName IN ('Production', 'Marketing', 'Engineering')
```
 
> 💡 `ColorName IN ('Silver', 'Blue', 'Red', 'Black')` é equivalente a escrever `ColorName = 'Silver' OR ColorName = 'Blue' OR ColorName = 'Red' OR ColorName = 'Black'` — mesmo resultado, forma mais limpa e mais fácil de manter conforme a lista cresce.





