# 🔗 JOINs e Relacionamentos em SQL Server

Neste arquivo guardo minhas consultas de junção entre tabelas — operações que combinam dados de duas ou mais tabelas a partir de uma coluna em comum.

`INNER JOIN`, `LEFT JOIN`, `RIGHT JOIN`, `FULL JOIN`

> **Base de dados de referência:** as consultas deste arquivo utilizam tabelas do schema **ContosoRetailDW** (`DimProduct`, `DimProductSubcategory`, `DimProductCategory`...) e, quando indicado, tabelas de exercícios com nomenclatura própria (`produtos`, `subcategoria`).

---

## Tipos de JOIN — visão geral

Antes de entrar nas consultas, é importante entender o que cada tipo de JOIN retorna:

| Tipo | O que retorna |
|---|---|
| `INNER JOIN` | Apenas as linhas que têm correspondência nas **duas** tabelas |
| `LEFT JOIN` | Todas as linhas da tabela da **esquerda**, com ou sem correspondência à direita |
| `RIGHT JOIN` | Todas as linhas da tabela da **direita**, com ou sem correspondência à esquerda |
| `FULL JOIN` | Todas as linhas das **duas** tabelas, com ou sem correspondência |

> 💡 Na prática, `INNER JOIN` e `LEFT JOIN` cobrem a grande maioria dos casos do dia a dia. `RIGHT JOIN` é raro — quase todo `RIGHT JOIN` pode ser reescrito como `LEFT JOIN` apenas trocando a ordem das tabelas, o que costuma deixar a consulta mais fácil de ler.

---

## 1. `RIGHT JOIN` — todas as linhas da tabela da direita

Retorna todas as subcategorias, independentemente de terem produtos associados. Para os produtos que existem, traz também os dados do produto.

```sql
-- Visualizar amostra das tabelas envolvidas
SELECT TOP (100) * FROM produtos
SELECT TOP (100) * FROM subcategoria

-- RIGHT JOIN: todas as subcategorias, com ou sem produto correspondente
SELECT
    id_produto,
    nome_produto,
    produtos.id_subcategoria,
    subcategoria.nome_subcategoria
FROM
    produtos
RIGHT JOIN subcategoria
    ON produtos.id_subcategoria = subcategoria.id_subcategoria
```

> ⚠️ **Atenção na condição do ON:** compare sempre a **chave estrangeira** de uma tabela com a **chave primária** da outra. Neste caso, `produtos.id_subcategoria` (chave estrangeira) com `subcategoria.id_subcategoria` (chave primária). Comparar colunas de naturezas diferentes (como `id_produto` com `id_subcategoria`) faz a query rodar sem erro, mas retorna dados incorretos — um bug silencioso difícil de detectar em produção.

> 💡 **RIGHT JOIN vs. LEFT JOIN:** esta consulta é equivalente a um `LEFT JOIN` com as tabelas trocadas de posição. A versão com `LEFT JOIN` costuma ser preferida por convenção — a maioria dos times de dados adota `LEFT JOIN` como padrão e evita `RIGHT JOIN` para manter a leitura consistente:
> ```sql
> SELECT
>     id_produto,
>     nome_produto,
>     produtos.id_subcategoria,
>     subcategoria.nome_subcategoria
> FROM
>     subcategoria
> LEFT JOIN produtos
>     ON subcategoria.id_subcategoria = produtos.id_subcategoria
> ```

## 2. `INNER JOIN` — apenas linhas com correspondência nas duas tabelas

Retorna somente os produtos que possuem uma subcategoria correspondente — registros sem par em qualquer uma das tabelas são descartados.

```sql
-- Visualizar amostra das tabelas envolvidas
SELECT TOP (100) * FROM produtos
SELECT TOP (100) * FROM subcategoria

-- INNER JOIN: apenas produtos com subcategoria correspondente
SELECT
    produtos.id_produto,
    produtos.nome_produto,
    produtos.id_subcategoria,
    subcategoria.nome_subcategoria
FROM
    produtos
INNER JOIN subcategoria
    ON produtos.id_subcategoria = subcategoria.id_subcategoria
```

> 💡 **Qualifique sempre as colunas com o nome da tabela** (`produtos.id_produto` em vez de só `id_produto`) quando trabalhar com JOINs. Se as duas tabelas tiverem uma coluna com o mesmo nome, o SQL Server retorna erro de ambiguidade — qualificar previne esse problema e deixa a consulta mais legível, deixando claro de onde vem cada coluna.

> 💡 **INNER JOIN é o padrão:** em queries com múltiplas tabelas, `INNER JOIN` é o tipo mais comum. Ele garante que só aparecem no resultado registros que têm correspondência nos dois lados — nenhum `NULL` "vaza" pro resultado por falta de par.

## 3. `FULL JOIN` — todas as linhas das duas tabelas

Retorna todos os registros de ambas as tabelas, com ou sem correspondência. Onde não houver par, o SQL Server preenche as colunas da tabela sem correspondência com `NULL`.

```sql
-- Visualizar amostra das tabelas envolvidas
SELECT TOP (100) * FROM produtos
SELECT TOP (100) * FROM subcategoria

-- FULL JOIN: todos os produtos e todas as subcategorias, com ou sem par
SELECT
    produtos.id_produto,
    produtos.nome_produto,
    produtos.id_subcategoria,
    subcategoria.nome_subcategoria
FROM
    produtos
FULL JOIN subcategoria
    ON produtos.id_subcategoria = subcategoria.id_subcategoria
```

> 💡 **Quando usar FULL JOIN:** é o tipo mais raro na prática. O caso de uso mais comum é auditoria e diagnóstico de integridade — por exemplo, encontrar produtos sem subcategoria cadastrada **e** subcategorias sem nenhum produto associado, tudo em uma única consulta. Em relatórios do dia a dia, `INNER JOIN` ou `LEFT JOIN` costumam ser suficientes.

> ⚠️ **Cuidado com o volume de dados:** em tabelas grandes, `FULL JOIN` pode retornar um volume muito maior de linhas do que o esperado, especialmente se houver muitos registros sem correspondência. Sempre combine com filtros ou analise o resultado com `COUNT(*)` antes de usar em produção.

## 4. `LEFT JOIN` — todas as linhas da tabela da esquerda

Retorna todos os produtos, independentemente de terem uma subcategoria correspondente. Para os produtos sem par em `subcategoria`, as colunas dessa tabela aparecem como `NULL`.

```sql
-- Visualizar amostra das tabelas envolvidas
SELECT TOP (100) * FROM produtos
SELECT TOP (100) * FROM subcategoria

-- LEFT JOIN: todos os produtos, com ou sem subcategoria correspondente
SELECT
    produtos.id_produto,
    produtos.nome_produto,
    produtos.id_subcategoria,
    subcategoria.nome_subcategoria
FROM
    produtos
LEFT JOIN subcategoria
    ON produtos.id_subcategoria = subcategoria.id_subcategoria
```

> 💡 **LEFT JOIN é o segundo mais usado**, logo após o `INNER JOIN`. Sempre que você precisar de "todos os registros de uma tabela, mais o que existir na outra", o `LEFT JOIN` é a escolha certa.

## 5. ANTI JOINs — encontrando registros sem correspondência

ANTI JOIN não é um comando SQL — é um **padrão** que combina `LEFT`, `RIGHT` ou `FULL JOIN` com `WHERE coluna IS NULL` para retornar apenas os registros que **não têm par** na outra tabela. É muito útil para auditoria e diagnóstico de integridade de dados.

```sql
-- Visualizar amostra das tabelas envolvidas
SELECT TOP (100) * FROM produtos
SELECT TOP (100) * FROM subcategoria
```

**LEFT ANTI JOIN** — produtos que não têm subcategoria correspondente:

```sql
SELECT
    produtos.id_produto,
    produtos.nome_produto,
    produtos.id_subcategoria,
    subcategoria.nome_subcategoria
FROM
    produtos
LEFT JOIN subcategoria
    ON produtos.id_subcategoria = subcategoria.id_subcategoria
WHERE
    subcategoria.nome_subcategoria IS NULL
```

**RIGHT ANTI JOIN** — subcategorias que não têm nenhum produto associado:

```sql
SELECT
    produtos.id_produto,
    produtos.nome_produto,
    produtos.id_subcategoria,
    subcategoria.nome_subcategoria
FROM
    produtos
RIGHT JOIN subcategoria
    ON produtos.id_subcategoria = subcategoria.id_subcategoria
WHERE
    produtos.id_produto IS NULL
```

**FULL ANTI JOIN** — registros sem correspondência em qualquer um dos lados:

```sql
SELECT
    produtos.id_produto,
    produtos.nome_produto,
    produtos.id_subcategoria,
    subcategoria.nome_subcategoria
FROM
    produtos
FULL JOIN subcategoria
    ON produtos.id_subcategoria = subcategoria.id_subcategoria
WHERE
    produtos.id_produto IS NULL OR subcategoria.nome_subcategoria IS NULL
```

> 💡 **Resumindo os ANTI JOINs:** `LEFT ANTI JOIN` = "o que existe em A mas não em B"; `RIGHT ANTI JOIN` = "o que existe em B mas não em A"; `FULL ANTI JOIN` = "o que não tem par em nenhum dos lados". São ferramentas poderosas para encontrar inconsistências e dados órfãos no banco.

## 6. Comparativo prático: `INNER JOIN`, `RIGHT JOIN` e `LEFT JOIN`

As três consultas abaixo usam as mesmas tabelas, as mesmas colunas e a mesma condição no `ON` — só o tipo de JOIN muda. O objetivo é visualizar na prática como cada um afeta o resultado.

```sql
-- Visualizar amostra das tabelas envolvidas
SELECT TOP (100) ProductKey, ProductName, ProductSubcategoryKey FROM DimProduct
SELECT TOP (100) ProductSubcategoryKey, ProductSubcategoryName FROM DimProductSubcategory
```

**INNER JOIN** — apenas produtos que possuem subcategoria correspondente:

```sql
SELECT
    ProductKey,
    ProductName,
    DimProduct.ProductSubcategoryKey,
    ProductSubcategoryName
FROM
    DimProduct
INNER JOIN DimProductSubcategory
    ON DimProduct.ProductSubcategoryKey = DimProductSubcategory.ProductSubcategoryKey
```

**RIGHT JOIN** — todas as subcategorias, com ou sem produto correspondente:

```sql
SELECT
    ProductKey,
    ProductName,
    DimProduct.ProductSubcategoryKey,
    ProductSubcategoryName
FROM
    DimProduct
RIGHT JOIN DimProductSubcategory
    ON DimProduct.ProductSubcategoryKey = DimProductSubcategory.ProductSubcategoryKey
```

**LEFT JOIN** — todos os produtos, com ou sem subcategoria correspondente:

```sql
SELECT
    ProductKey,
    ProductName,
    DimProduct.ProductSubcategoryKey,
    ProductSubcategoryName
FROM
    DimProduct
LEFT JOIN DimProductSubcategory
    ON DimProduct.ProductSubcategoryKey = DimProductSubcategory.ProductSubcategoryKey
```

> 💡 **Compare os resultados:** rode as três queries e observe a diferença na quantidade de linhas retornadas e onde aparecem os `NULL`. Esse exercício é a forma mais rápida de fixar intuitivamente o comportamento de cada tipo de JOIN.

## 7. Como escolher a tabela da esquerda e da direita?

Uma dúvida comum: qual tabela vai antes e qual vai depois do JOIN? A regra prática é simples — **a tabela que você quer preservar completamente vai do lado do `LEFT`**. As duas consultas abaixo retornam o mesmo resultado, demonstrando que `LEFT JOIN` e `RIGHT JOIN` são intercambiáveis dependendo da ordem das tabelas.

**Caso 1 — `LEFT JOIN`:** `produtos` é a tabela principal (esquerda), complementada por `subcategoria`:

```sql
SELECT
    id_produto,
    produtos.id_subcategoria,
    nome_subcategoria
FROM
    produtos
LEFT JOIN subcategoria
    ON produtos.id_subcategoria = subcategoria.id_subcategoria
```

**Caso 2 — `RIGHT JOIN` equivalente:** mesma lógica, tabelas invertidas de lado:

```sql
SELECT
    id_produto,
    produtos.id_subcategoria,
    nome_subcategoria
FROM
    subcategoria
RIGHT JOIN produtos
    ON subcategoria.id_subcategoria = produtos.id_subcategoria
```

> 💡 **Conclusão:** `LEFT JOIN` com `produtos` à esquerda e `RIGHT JOIN` com `produtos` à direita produzem exatamente o mesmo resultado. Por convenção, prefira sempre o `LEFT JOIN` — é mais legível e evita que o leitor precise "virar" mentalmente a ordem das tabelas pra entender a query.

## 8. `CROSS JOIN` — produto cartesiano entre duas tabelas
 
O `CROSS JOIN` combina **cada linha** da primeira tabela com **todas as linhas** da segunda, sem nenhuma condição de correspondência. É o único tipo de JOIN que não usa `ON`.
 
```sql
-- Visualizar amostra das tabelas envolvidas
SELECT TOP (100) * FROM marcas
SELECT TOP (100) * FROM subcategoria
 
-- CROSS JOIN: cada marca combinada com cada subcategoria
SELECT
    marca,
    nome_subcategoria
FROM
    marcas
CROSS JOIN subcategoria
```
 
> ⚠️ **Cuidado com o volume:** o resultado é o **produto cartesiano** das duas tabelas — se `marcas` tem 10 linhas e `subcategoria` tem 20, o resultado terá 200 linhas. Em tabelas com milhares de registros isso pode gerar milhões de linhas e travar o servidor. Sempre teste com `TOP` ou `COUNT(*)` antes de rodar em produção.
 
> 💡 **Quando usar CROSS JOIN:** é raro no dia a dia, mas tem casos de uso legítimos — gerar todas as combinações possíveis entre dois conjuntos (ex: todas as combinações de tamanho e cor de um produto, calendários, matrizes de preço). Fora desses cenários específicos, um `CROSS JOIN` acidental (esquecendo o `ON` num JOIN normal) é quase sempre um bug.

## 9. Múltiplos JOINs encadeados
 
É possível encadear vários JOINs em uma única consulta, conectando três ou mais tabelas em sequência. Cada novo JOIN se apoia no resultado do anterior — a ordem importa.
 
Neste exemplo, partimos de `DimProduct`, subimos para `DimProductSubcategory` e depois para `DimProductCategory`, trazendo o nome do produto, da subcategoria e da categoria em uma única consulta.
 
```sql
-- Visualizar amostra das três tabelas envolvidas
SELECT TOP (100) ProductKey, ProductName, ProductSubcategoryKey FROM DimProduct
SELECT TOP (100) ProductCategoryKey, ProductSubcategoryName, ProductSubcategoryKey FROM DimProductSubcategory
SELECT TOP (100) ProductCategoryKey, ProductCategoryName FROM DimProductCategory
 
-- Três tabelas conectadas em sequência via INNER JOIN
SELECT
    ProductKey,
    ProductName,
    DimProduct.ProductSubcategoryKey,
    ProductSubcategoryName,
    ProductCategoryName
FROM
    DimProduct
INNER JOIN DimProductSubcategory
    ON DimProduct.ProductSubcategoryKey = DimProductSubcategory.ProductSubcategoryKey
INNER JOIN DimProductCategory
    ON DimProductSubcategory.ProductCategoryKey = DimProductCategory.ProductCategoryKey
```
 
> 💡 **Lendo JOINs encadeados:** leia de cima pra baixo — cada `ON` conecta a tabela recém-adicionada com alguma tabela já presente na consulta. Aqui: `DimProduct` conecta com `DimProductSubcategory` pela chave de subcategoria, e `DimProductSubcategory` conecta com `DimProductCategory` pela chave de categoria.
 
> ⚠️ **Cuidado com colunas de mesmo nome em múltiplos JOINs:** quanto mais tabelas, maior a chance de colisão de nomes. Qualifique sempre as colunas ambíguas com o nome da tabela (`DimProduct.ProductSubcategoryKey`), como feito nessa consulta.

## 10. `UNION` e `UNION ALL` — combinando resultados verticalmente
 
Enquanto os JOINs combinam tabelas **horizontalmente** (adicionando colunas), o `UNION` combina resultados **verticalmente** (empilhando linhas). As queries combinadas precisam ter o mesmo número de colunas e tipos compatíveis.
 
**`UNION`** — empilha os resultados e **remove duplicatas** automaticamente (como um `DISTINCT` implícito):
 
```sql
SELECT
    *
FROM
    DimCustomer
WHERE
    Gender = 'F'
UNION
SELECT
    *
FROM
    DimCustomer
WHERE
    Gender = 'M'
```
 
**`UNION ALL`** — empilha os resultados e **mantém todas as linhas**, incluindo duplicatas:
 
```sql
SELECT
    FirstName,
    BirthDate
FROM
    DimCustomer
WHERE
    Gender = 'F'
UNION ALL
SELECT
    FirstName,
    BirthDate
FROM
    DimCustomer
WHERE
    Gender = 'M'
```
 
> ⚠️ **`SELECT *` com `UNION`** funciona quando as duas queries vêm da mesma tabela (como no exemplo acima). Em tabelas diferentes, as colunas precisam ser compatíveis em número e tipo na mesma ordem — usar `SELECT *` em tabelas distintas quase sempre gera erro. Prefira listar as colunas explicitamente.
 
> 💡 **`UNION` vs. `UNION ALL`:** prefira sempre `UNION ALL` quando souber que não há duplicatas ou quando não precisar removê-las — ele é significativamente mais rápido porque não precisa fazer a verificação de duplicidade. Use `UNION` apenas quando a remoção de duplicatas for realmente necessária.
 