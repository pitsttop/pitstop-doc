# Arquitetura do Ecossistema PitStop

## ⚠️ Status do Projeto: Em Desenvolvimento

Este documento descreve a arquitetura atual do ecossistema PitStop. O projeto está em evolução constante; portanto, fluxos, componentes e integrações seguem em revisão à medida que novos requisitos surgem.

---

## Visão Geral

O PitStop adota uma arquitetura multi-repositório formada por três serviços principais:
- **Frontend**: SPA responsável pela experiência do usuário.
- **Backend**: API REST que centraliza regras de negócio, integrações e persistência.
- **Autenticação**: serviço dedicado a identidade, autorização e emissão de tokens.

Os serviços comunicam-se via HTTP, compartilhando contratos REST e políticas de segurança unificadas. O banco de dados PostgreSQL sustenta tanto o backend quanto o módulo de autenticação, cada qual com seu schema e migrações versionadas via Prisma.

## Repositórios e Responsabilidades

- `pitstop-frontend`: interface web desenvolvida em React + TypeScript, com rotas protegidas por token JWT.
- `pitstop-backend`: API em Node.js + Express que orquestra operações de clientes, veículos, ordens e integrações internas.
- `pitstop-auth`: serviço independente de autenticação/autorização, responsável por cadastro, login, emissão e validação de JWTs.
- `pitstop-doc`: documentação oficial (este repositório), construída com MkDocs.

Cada repositório possui ciclo próprio de build/test/deploy, permitindo evoluir funcionalidades de forma isolada, mas com contratos bem definidos para integração.

## Serviço de Autenticação (`pitstop-auth`)

### Visão Geral

- Serviço Node.js + TypeScript baseado em Express 5.
- Persistência via Prisma ORM conectado a PostgreSQL.
- Fluxo completo de cadastro, login, geração de JWT e rota protegida.
- Hash seguro com `bcryptjs` e seeds para criar administrador padrão.

### Estrutura do Repositório

- `src/app.ts`: montagem do Express, middlewares globais e registro de rotas.
- `src/server.ts`: ponto de entrada que sobe o servidor HTTP.
- `src/auth.routes.ts`: rotas públicas (`signup`, `login`) com validações e respostas padronizadas.
- `src/services/user.services.ts`: criação/autenticação de usuários via Prisma e `bcrypt`.
- `src/middlewares/auth.middlewares.ts`: middleware `authorize` que valida JWT e aplica controle de acesso por roles.
- `prisma/schema.prisma`: modelo de dados (tabela `User`) e `migrations/` versionadas.
- `prisma/seed.ts`: script opcional que insere o usuário administrador.
- `test/`: suíte com Jest + Supertest cobrindo fluxo E2E, middleware e serviços.

### Stack e Dependências

- Runtime: Node.js 18+ (TypeScript compilado para CommonJS).
- Framework: Express 5.
- ORM/DB: Prisma + PostgreSQL.
- Segurança: `bcryptjs` para hash e `jsonwebtoken` para tokens.
- Testes: Jest + `ts-jest`, Supertest para cenários E2E.

## Backend API (`pitstop-backend`)

### Arquitetura e Tecnologias

- Linguagem TypeScript sobre Node.js.
- Express com middlewares para CORS, logging (`morgan`) e parsing de JSON.
- Autorização via JWT com `UserRole.ADMIN` e `UserRole.CLIENT`.
- Prisma ORM conectado a PostgreSQL.
- Testes com Jest (`ts-jest`) divididos em unitários, integração e E2E.
- Ferramentas auxiliares: ESLint, Prettier, `ts-node-dev` e `dotenv`.

### Estrutura de Pastas

- `src/app.ts`: configuração principal do Express.
- `src/index.ts`: bootstrap do servidor HTTP.
- `src/middlewares/`: middlewares de autorização e utilidades.
- `src/routes/`: rotas REST (clientes, veículos, ordens etc.).
- `src/services/`: camada de regras de negócio e acesso ao Prisma.
- `tests/`: cobertura unitária, integração e E2E.

### Infraestrutura e Suporte

- `prisma/schema.prisma`: modelagem das entidades (clientes, veículos, ordens etc.).
- `prisma/migrations/`: histórico versionado das alterações de schema.
- `prisma/seed.ts`: script para popular dados iniciais.
- `generated/`: cliente Prisma gerado automaticamente.
- `docker-compose.yml`: orquestra banco e serviços auxiliares.
- `Dockerfile`: build da imagem Node/Prisma.
- `jest.config.js` e `jest-e2e.config.js`: configuração das suítes de testes.
- `package.json`: scripts de build, testa e lint.

## Frontend Web (`pitstop-frontend`)

### Arquitetura

- SPA construída com React + TypeScript e bundling via Vite.
- `App.tsx`: roteamento condicional baseado em token/role fornecido pelo `AuthProvider`.
- `components/`: biblioteca de componentes com `data-testid` para facilitar testes.
- `hooks/useAuth.tsx`: abstração de login, cadastro, refresh e persistência de sessão.
- `hooks/useApi.tsx`: encapsula chamadas REST para clientes, veículos, ordens etc. com axios.
- `services/`: configuração do cliente HTTP e endpoints de autenticação.
- `main.tsx`: bootstrap padrão da aplicação React/Vite.

### Experiência do Usuário

- Integração com o serviço de autenticação para obter e renovar JWT.
- Proteção de rotas e controle de visibilidade conforme o papel (`ADMIN` ou `CLIENT`).
- Feedbacks consistentes via componentes reutilizáveis.

## Integração entre Serviços

- O frontend consome o backend via REST, enviando o JWT persistido no `AuthProvider`.
- O backend delega operações de identidade ao `pitstop-auth`, validando tokens recebidos e aplicando regras de autorização.
- Tanto backend quanto autenticação compartilham práticas de versionamento do Prisma e pipelines de testes automatizados para garantir contratos compatíveis.

## Considerações Finais

A separação em repositórios independentes favorece isolamento de domínio, escalabilidade horizontal e ciclos de deploy mais curtos. A documentação acompanha as evoluções garantindo canal único de comunicação arquitetural para o time.