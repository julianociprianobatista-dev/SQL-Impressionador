# 🗄️ SQL Impressionador

![SQL Server](https://img.shields.io/badge/Microsoft%20SQL%20Server-CC292B?style=for-the-badge&logo=microsoftsqlserver&logoColor=white)
![Git](https://img.shields.io/badge/Git-F05032?style=for-the-badge&logo=git&logoColor=white)
![GitHub](https://img.shields.io/badge/GitHub-181717?style=for-the-badge&logo=github&logoColor=white)
![License MIT](https://img.shields.io/badge/License-MIT-green?style=for-the-badge)

---

## 📌 Sobre o Projeto

O **SQL Impressionador** é um guia prático e biblioteca de consultas SQL estruturada para desenvolvedores, analistas de dados e estudantes que buscam exemplos de queries eficientes e bem documentadas no **Microsoft SQL Server**.

Este repositório foi construído para resolver um problema comum: encontrar rapidamente soluções para problemas do dia a dia em banco de dados, servindo tanto como fonte de consulta rápida quanto como portfólio de estudos.

📋 [Veja o roadmap do projeto](https://github.com/julianociprianobatista-dev/SQL-Impressionador/projects/1)

---

## ⚡ Exemplo Prático de Consulta

Aqui está um aperitivo da estrutura visual e de documentação que você encontrará nas seções:

```sql
-- Selecionando os 100 primeiros clientes cadastrados
SELECT TOP (100)
    [ID_Cliente],
    [Empresa],
    [Nome],
    [Sobrenome]
FROM [dbo].[DimCliente]
ORDER BY [ID_Cliente] ASC;
```

---

## 📌 Sumário por Tecnologia

### 🟦 SQL Server

Consultas e scripts focados em **Microsoft SQL Server**:

* 📄 [01 - Comandos Básicos](./SQL-server/01-comandos-basicos.md) *(SELECT, WHERE, TOP, ORDER BY)*
* 📄 [02 - Funções de Agregação](./SQL-server/02-funcoes-de-agregacao.md) *(GROUP BY, HAVING, agrupamentos, GROUP BY + JOINs)*
* 📄 [03 - JOINs e Relacionamentos](./SQL-server/03-joins-relacionamentos.md) *(em construção — INNER JOIN, LEFT JOIN, RIGHT JOIN)*
* 📄 04 - Variáveis e Manipulação de Dados *(em construção — variáveis, strings, datas, funções condicionais)*
* 📄 05 - Views e CRUD *(em construção — SQL Views, CRUD)*
* 📄 06 - Subqueries e CTEs *(em construção)*
* 📄 07 - Estruturas de Controle *(em construção — loops no SQL)*
* 📄 08 - Funções de Janela *(em construção — ROW_NUMBER, RANK, OVER)*
* 📄 09 - Expressões Regulares *(em construção — Regular Expressions)*
* 📄 10 - Constraints e Sequences *(em construção)*
* 📄 11 - Transações *(em construção — Transactions)*
* 📄 12 - Functions e Procedures *(em construção)*
* 📄 13 - Triggers *(em construção — Triggers DML)*
* 📄 14 - Pivot Table *(em construção)*

---

## 🚀 Como Usar Este Repositório

**1. Clonar o repositório para o seu computador**

```bash
git clone https://github.com/julianociprianobatista-dev/SQL-Impressionador.git
```

**2. Entrar na pasta do projeto**

```bash
cd SQL-Impressionador
```

**3. Navegar pelas consultas**

Você pode abrir os arquivos `.md` diretamente no seu editor de código (como o VS Code) ou visualizar formatado diretamente aqui no GitHub!

---

## 🤝 Contribuições

Contribuições são sempre bem-vindas! Se você tem uma consulta otimizada ou um novo caso de uso no SQL Server para compartilhar:

1. Faça um **Fork** deste repositório.
2. Crie uma branch para a sua funcionalidade (`git checkout -b feature/nova-consulta`).
3. Commit suas alterações (`git commit -m 'docs: adiciona nova consulta de JOIN'`).
4. Envie para o repositório remoto (`git push origin feature/nova-consulta`).
5. Abra um **Pull Request**.

---

## 🛠️ Ferramentas Utilizadas

* **SGBD:** Microsoft SQL Server / SSMS
* **Versionamento:** Git & GitHub
* **Editor:** Visual Studio Code

---

## 🌐 Banco de Dados Utilizado

As consultas deste repositório foram desenvolvidas e testadas utilizando o banco de dados fictício **Contoso** (disponibilizado gratuitamente pela **Microsoft** para cenários de treino em Business Intelligence e SQL Server).

* **Database de Treino:** Contoso (ContosoRetailDW / ContosoUniversity)

---

## 📜 Licença

Este projeto está sob a licença MIT - veja o arquivo [LICENSE](./LICENSE) para mais detalhes.

---

## 👨‍💻 Desenvolvido por **Juliano Cipriano Batista**

⭐ Se este repositório ajudou nos seus estudos, não se esqueça de deixar uma estrela!
