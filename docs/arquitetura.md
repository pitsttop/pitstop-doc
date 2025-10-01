# Arquitetura do Sistema PitStop

## ⚠️ Status do Projeto: Em Desenvolvimento

Este documento descreve a arquitetura planejada e o estado atual do projeto PitStop. É importante notar que o projeto está em desenvolvimento ativo. Portanto, todas as suas partes — Frontend, Backend, Banco de Dados e a própria documentação — estão sujeitas a alterações, refatorações e melhorias contínuas à medida que novas funcionalidades são implementadas e os requisitos são refinados.

---

## Visão Geral

A arquitetura do projeto PitStop foi projetada seguindo um modelo moderno de 3 camadas, com componentes desacoplados para garantir manutenibilidade e escalabilidade. As três camadas principais são: Frontend (Cliente), Backend (Servidor da API) e Banco de Dados.

## Componentes Principais

### Frontend (Cliente)

Uma Single-Page Application (SPA) construída com **React** e **TypeScript**, utilizando **Vite** como ferramenta de build. É responsável por toda a interface do usuário e pela experiência de interação.

* **Tecnologias e Padrões:**
    * **React:** Biblioteca principal para a construção da interface.
    * **TypeScript:** Garante a segurança de tipos e melhora a manutenibilidade.
    * **Estilização:** Utiliza **Tailwind CSS** e a biblioteca de componentes **Shadcn/UI** para criar uma interface moderna e consistente de forma rápida.
    

### Backend (Servidor)

Uma **REST API** construída com **Node.js** e **TypeScript**, utilizando o micro-framework **Express.js** para o gerenciamento de rotas e requisições. É o cérebro do sistema, responsável por toda a lógica de negócio, validações e segurança.

### Banco de Dados

Um banco de dados relacional **PostgreSQL**, gerenciado e executado em um contêiner **Docker** para garantir um ambiente de desenvolvimento e produção consistente e isolado.

* **ORM (Object-Relational Mapper):** A comunicação entre o backend e o banco de dados é totalmente gerenciada pelo **Prisma**. O Prisma serve como a camada de acesso a dados, provendo segurança de tipos e simplificando as consultas.
* **Evolução do Schema:** A evolução da estrutura do banco de dados (criação de tabelas, adição de colunas, etc.) é gerenciada de forma versionada através do **Prisma Migrate**.
* **Dados Iniciais:** Dados essenciais, como o usuário administrador padrão, são populados no banco através de um script de **seed** do Prisma.

## Estrutura de Repositórios

O projeto adota uma abordagem multi-repo para separar as responsabilidades:
* **`pitstop-frontend`**: Contém todo o código da aplicação do cliente.
* **`pitstop-backend`**: Contém todo o código do servidor da API.
* **`pitstop-doc`**: Contém a documentação do projeto (este site).

## Arquitetura do Backend (Camadas)

O backend é estruturado em camadas para separar as preocupações:
1.  **Camada de Rotas (Routes):** Define os endpoints da API (`/clientes`, `/ordens`, etc.) e direciona as requisições, validando os dados de entrada.
2.  **Camada de Serviços (Services):** Contém a lógica de negócio principal do sistema. É aqui que as regras são aplicadas (ex: "apenas um admin pode criar outro admin"). Esta camada é a única que se comunica com a camada de dados.
3.  **Camada de Acesso a Dados (Prisma):** É a camada mais baixa, composta pelo Prisma Client, responsável exclusivamente por executar as operações de leitura e escrita no banco de dados.

## Autenticação e Autorização

O sistema utiliza um modelo de **Controle de Acesso Baseado em Papéis (RBAC)**.
* A entidade `User` possui um campo `role` (ex: `ADMIN`, `CLIENT`) que define o nível de permissão.
* A autenticação será feita via **JSON Web Tokens (JWT)**. Um usuário enviará seu email e senha para um endpoint `POST /login`. Se as credenciais forem válidas, o servidor retornará um token JWT.
* Para acessar rotas protegidas, o frontend deverá enviar este token no cabeçalho `Authorization` de cada requisição. Um middleware no backend será responsável por validar o token e o `role` do usuário antes de permitir que a requisição prossiga.