# 📦 API Inventory Manager

Uma API REST para gerenciamento de estoque, desenvolvida em Node.js com TypeScript, Express e Drizzle ORM, focada em performance e escalabilidade.

## 📋 Índice

- [Funcionalidades](#-funcionalidades)
- [Tecnologias](#-tecnologias)
- [Estrutura do Projeto](#-estrutura-do-projeto)
- [Instalação](#-instalação)
- [Uso](#-uso)
- [Endpoints](#-endpoints)
- [Banco de Dados](#-banco-de-dados)
- [Desenvolvimento](#-desenvolvimento)

## ⚡ Funcionalidades

- ✅ **Gestão de Usuários**: Criação, atualização, listagem e exclusão de usuários.
- ✅ **Autenticação JWT-like por Token**: Segurança nas rotas com token de sessão no header `Authorization`.
- ✅ **Autorização por Nível de Acesso**: Middleware para controle de permissões (`admin` ou usuário comum).
- ✅ **Gestão de Categorias**: CRUD completo com contagem de produtos por categoria.
- ✅ **Gestão de Produtos**: CRUD com filtro por nome, paginação e controle de estoque mínimo/máximo.
- ✅ **Movimentações de Estoque**: Registro de entradas (`in`) e saídas (`out`) com histórico completo.
- ✅ **Dashboard Analítico**: Valor total do estoque, resumo de movimentações, gráfico diário, produtos em baixa e estagnados.
- ✅ **Upload de Avatares**: Upload e processamento de imagens com `Multer` + `Sharp`.
- ✅ **Validação de Dados**: Validação robusta de entrada com Zod.
- ✅ **Tratamento de Erros**: Sistema centralizado para tratamento de erros da aplicação.
- ✅ **Banco via Docker**: Setup pronto com PostgreSQL em contêiner.

## 🛠️ Tecnologias

- **Node.js** - Runtime JavaScript
- **TypeScript** - Tipagem estática
- **Express 5** - Framework web
- **Drizzle ORM** - ORM moderno e type-safe para Node.js e TypeScript
- **PostgreSQL 17** - Banco de dados relacional (gerenciado com Docker)
- **Zod** - Validação de schemas
- **Bcrypt** - Hashing de senhas
- **Multer** - Middleware de upload de arquivos
- **Sharp** - Processamento de imagens
- **CORS** - Controle de acesso cross-origin
- **Docker** - Gerenciamento de contêineres para o banco de dados e aplicação
- **tsx** - Execução de TypeScript em desenvolvimento com hot reload

## 📁 Estrutura do Projeto

```
inventory-manager-backend/
├── drizzle/                # Migrações geradas pelo Drizzle Kit
│   ├── meta/
│   └── 0000_optimal_carnage.sql
├── src/
│   ├── controllers/        # Controladores das rotas
│   │   ├── auth.controller.ts
│   │   ├── category.controller.ts
│   │   ├── dashboard.controller.ts
│   │   ├── move.controller.ts
│   │   ├── product.controller.ts
│   │   └── user.controller.ts
│   ├── db/                 # Conexão, schemas e seed do banco
│   │   ├── schema/
│   │   ├── connection.ts
│   │   └── seed.ts
│   ├── middlewares/        # Middlewares (autenticação, erros, upload)
│   │   ├── auth.middleware.ts
│   │   ├── error.middleware.ts
│   │   └── upload.middleware.ts
│   ├── routes/             # Definição das rotas da API
│   │   ├── auth.routes.ts
│   │   ├── categories.routes.ts
│   │   ├── dashboard.routes.ts
│   │   ├── moves.routes.ts
│   │   ├── products.routes.ts
│   │   ├── user.routes.ts
│   │   └── index.ts
│   ├── services/           # Regras de negócio
│   │   ├── category.service.ts
│   │   ├── dashboard.service.ts
│   │   ├── file.service.ts
│   │   ├── move.service.ts
│   │   ├── product.service.ts
│   │   └── user.service.ts
│   ├── validators/         # Schemas de validação com Zod
│   │   ├── auth.validator.ts
│   │   ├── category.validator.ts
│   │   ├── dashboard.validator.ts
│   │   ├── move.validator.ts
│   │   ├── product.validator.ts
│   │   └── user.validator.ts
│   ├── types/              # Tipagens customizadas (ex: Express)
│   ├── utils/              # Utilitários (ex: AppError)
│   └── server.ts           # Ponto de entrada da aplicação
├── docker-compose.yml      # Configuração do Docker (banco + app)
├── Dockerfile              # Imagem da aplicação
├── drizzle.config.ts       # Configuração do Drizzle Kit
├── .env.example
├── package.json
├── tsconfig.json
└── README.md
```

## 🚀 Instalação

1.  **Clone o repositório**

    ```bash
    git clone <url-do-repositorio>
    cd inventory-manager/fontes/backend
    ```

2.  **Instale as dependências**

    ```bash
    npm install
    ```

3.  **Crie o arquivo de ambiente**

    Crie um arquivo `.env` na raiz do backend baseado no `.env.example`:

    ```env
    PORT=3001
    NODE_ENV=development
    DATABASE_URL=postgresql://postgres:postgres@localhost:5432/inventory-manager-db
    BASE_URL=http://localhost:3001
    ```

4.  **Inicie o contêiner do banco de dados com Docker**

    ```bash
    docker-compose up -d db
    ```

    > 💡 Para subir aplicação + banco juntos, use `docker-compose up -d` (a aplicação executa migrações e seed automaticamente).

5.  **Execute as migrações do Drizzle**

    ```bash
    npm run db:migrate
    ```

6.  **(Opcional) Popule o banco com dados iniciais**

    ```bash
    npm run db:seed
    ```

7.  **Inicie o servidor de desenvolvimento**

    ```bash
    npm run dev
    ```

A API estará disponível em `http://localhost:3001`.

## 🎯 Uso

### Fluxo Básico

1.  **Criar um usuário** na rota `POST /api/users`.
2.  **Autenticar** para obter um token na rota `POST /api/auth/login`.
3.  **Anexar o token** no header `Authorization` das requisições protegidas (`Bearer SEU_TOKEN`).
4.  **Criar categorias** para organizar os produtos.
5.  **Cadastrar produtos** associando a uma categoria.
6.  **Registrar movimentações** de entrada e saída de estoque.
7.  **Consultar o dashboard** para acompanhar valor do estoque, produtos em baixa e movimentações.

## 📡 Endpoints

Base URL: `http://localhost:3001/api`

Todas as respostas seguem o formato:

```json
{
  "error": "string ou null",
  "data": "objeto ou null"
}
```

### 🔐 Autenticação

| Método | Endpoint           | Descrição                          | Autenticação |
| :----- | :----------------- | :--------------------------------- | :----------- |
| `POST` | `/auth/login`      | Autentica e retorna um token       | Não          |
| `POST` | `/auth/logout`     | Encerra a sessão do usuário        | Sim          |
| `GET`  | `/auth/me`         | Retorna o usuário autenticado      | Sim          |

### 👤 Usuários

| Método   | Endpoint      | Descrição                          | Autenticação | Permissão |
| :------- | :------------ | :--------------------------------- | :----------- | :-------- |
| `GET`    | `/users`      | Lista usuários (paginado)          | Sim          | `admin`   |
| `GET`    | `/users/:id`  | Busca um usuário por ID            | Sim          | `admin`   |
| `POST`   | `/users`      | Cria um novo usuário               | Sim          | `admin`   |
| `PUT`    | `/users/:id`  | Atualiza um usuário (com avatar)   | Sim          | `admin`   |
| `DELETE` | `/users/:id`  | Remove um usuário                  | Sim          | `admin`   |

### 🏷️ Categorias

| Método   | Endpoint           | Descrição                                       | Autenticação |
| :------- | :----------------- | :---------------------------------------------- | :----------- |
| `GET`    | `/categories`      | Lista categorias (com `includeProductCount`)    | Sim          |
| `GET`    | `/categories/:id`  | Busca uma categoria por ID                      | Sim          |
| `POST`   | `/categories`      | Cria uma nova categoria                         | Sim          |
| `PUT`    | `/categories/:id`  | Atualiza uma categoria                          | Sim          |
| `DELETE` | `/categories/:id`  | Remove uma categoria                            | Sim          |

### 📦 Produtos

| Método   | Endpoint          | Descrição                                  | Autenticação |
| :------- | :---------------- | :----------------------------------------- | :----------- |
| `GET`    | `/products`       | Lista produtos (filtro por nome + paginado) | Sim          |
| `GET`    | `/products/:id`   | Busca um produto por ID                    | Sim          |
| `POST`   | `/products`       | Cria um novo produto                       | Sim          |
| `PUT`    | `/products/:id`   | Atualiza um produto                        | Sim          |
| `DELETE` | `/products/:id`   | Remove um produto                          | Sim          |

**Exemplo - Criar produto:**

```json
POST /api/products
{
  "name": "Arroz 5kg",
  "categoryId": "uuid-da-categoria",
  "unitPrice": 2500,
  "unitType": "kg",
  "quantity": 50,
  "minimumQuantity": 10,
  "maximumQuantity": 200
}
```

### 🔄 Movimentações (Moves)

| Método | Endpoint   | Descrição                                | Autenticação |
| :----- | :--------- | :--------------------------------------- | :----------- |
| `GET`  | `/moves`   | Lista movimentações (filtro por produto) | Sim          |
| `POST` | `/moves`   | Registra entrada ou saída de estoque     | Sim          |

**Exemplo - Registrar movimentação:**

```json
POST /api/moves
{
  "productId": "uuid-do-produto",
  "type": "in",
  "quantity": 10
}
```

### 📊 Dashboard

| Método | Endpoint                        | Descrição                                         | Autenticação |
| :----- | :------------------------------ | :------------------------------------------------ | :----------- |
| `GET`  | `/dashboard/inventory-value`    | Valor monetário total do estoque                  | Sim          |
| `GET`  | `/dashboard/moves-summary`      | Resumo de entradas e saídas por período           | Sim          |
| `GET`  | `/dashboard/moves-graph`        | Total diário de saídas (para gráficos)            | Sim          |
| `GET`  | `/dashboard/low-stock`          | Produtos abaixo ou próximos do estoque mínimo     | Sim          |
| `GET`  | `/dashboard/stagnant-products`  | Produtos sem saídas em um período                 | Sim          |

## 🗄️ Banco de Dados

O banco de dados é gerenciado pelo **Drizzle ORM** com **PostgreSQL**. Os schemas ficam em `src/db/schema/`.

### Tabelas principais

- **users**: usuários do sistema (com permissão `isAdmin` e avatar).
- **categories**: categorias de produtos.
- **products**: produtos com preço unitário (em centavos), tipo de unidade (`kg`, `g`, `l`, `ml`, `un`) e limites de estoque.
- **moves**: movimentações de estoque (entradas/saídas) vinculadas a produto e usuário.

### Comandos Úteis do Drizzle Kit

```bash
# Gerar uma nova migração a partir do schema
npm run db:generate

# Aplicar migrações pendentes no banco
npm run db:migrate

# Sincronizar o schema com o banco (sem migração)
npm run db:push

# Abrir o Drizzle Studio para visualizar os dados
npm run db:studio

# Popular o banco com dados iniciais
npm run db:seed
```

## 🔧 Desenvolvimento

### Scripts Disponíveis

```bash
# Desenvolvimento com hot reload (tsx watch)
npm run dev

# Build para produção (TypeScript -> dist/)
npm run build

# Executar em produção
npm start
```

### Estrutura de Erros

A API utiliza um sistema centralizado de tratamento de erros:

- **AppError**: Erros de negócio controlados (400, 401, 403, 404, etc.).
- **ZodError**: Erros de validação de entrada (400).
- **Erros Inesperados**: Erros internos do servidor (500).

Todos são tratados pelo middleware global `globalErrorHandler` em `src/middlewares/error.middleware.ts`.

### Health Check

- `GET /ping` → retorna `{ "pong": true }` para verificar se a API está ativa.

---

⭐ Se este projeto foi útil para você, considere dar uma estrela no repositório!
