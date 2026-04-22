# 📦 Inventory Manager Front-End

Uma aplicação web para gerenciamento de estoque, desenvolvida em Next.js com React, TypeScript e Tailwind CSS.

## 📋 Índice

- [Funcionalidades](#-funcionalidades)
- [Tecnologias](#️-tecnologias)
- [Pré-requisitos](#-pré-requisitos)
- [Instalação](#-instalação)
- [Uso](#-uso)
- [Estrutura do Projeto](#-estrutura-do-projeto)

## ⚡ Funcionalidades

- ✅ **Autenticação**: Login de usuários com sessão baseada em token
- ✅ **Dashboard**: Visão geral do estoque com valor total, movimentações e gráficos
- ✅ **Gestão de Produtos**: Cadastro, edição, listagem e exclusão de produtos
- ✅ **Gestão de Categorias**: Organize produtos em categorias personalizadas
- ✅ **Gestão de Usuários**: Cadastro e administração de usuários (com permissão de admin)
- ✅ **Movimentações de Estoque**: Registre entradas e saídas de produtos
- ✅ **Produtos em Baixa**: Visualize produtos próximos do estoque mínimo
- ✅ **Produtos Estagnados**: Identifique produtos sem saídas em um período
- ✅ **Tema Claro/Escuro**: Alternância de tema com `next-themes`
- ✅ **Responsividade**: Interface adaptada para dispositivos móveis e desktops

## 🛠️ Tecnologias

- [Next.js 16] - Framework React com App Router e Server Actions
- [React 19] - Biblioteca JavaScript para construção de interfaces
- [TypeScript] - Tipagem estática
- [Tailwind CSS 4] - Estilização utilitária
- [Radix UI] - Componentes acessíveis e sem estilização
- [React Hook Form] - Gerenciamento de formulários
- [Zod] - Validação de schemas e dados
- [Axios] - Cliente HTTP para consumo da API
- [Recharts] - Gráficos e visualização de dados
- [Lucide React] - Ícones
- [next-themes] - Suporte a tema claro/escuro

## 📦 Pré-requisitos

- Node.js (versão 18+)
- npm (ou pnpm/yarn)
- Git para clonagem do repositório
- **Backend da API em execução** (veja abaixo)

### 🔗 Backend Necessário

Este projeto requer o backend do Inventory Manager para funcionar. Certifique-se de clonar e configurar o backend antes de iniciar o frontend.

O backend deve estar disponível em `http://localhost:3001`. Consulte o `endpoints.md` na raiz do frontend para conhecer todos os endpoints consumidos.

## 🚀 Instalação

1. **Clone o repositório**

```bash
git clone <url-do-repositorio>
cd inventory-manager/fontes/frontend
```

2. **Instale as dependências**

```bash
npm install
```

3. **Configure as variáveis de ambiente**

Crie um arquivo `.env` na raiz do frontend com a URL da API:

```env
NEXT_PUBLIC_API_BASE_URL=http://localhost:3001
```

4. **Certifique-se de que o backend está rodando**

O backend deve estar disponível em `http://localhost:3001`

5. **Execute o servidor de desenvolvimento**

```bash
npm run dev
```

6. **Acesse a aplicação**

Abra [http://localhost:3000](http://localhost:3000) no seu navegador.

## 🎯 Uso

### Comandos Disponíveis

| Comando         | Descrição                            |
| --------------- | ------------------------------------ |
| `npm run dev`   | Inicia o servidor de desenvolvimento |
| `npm run build` | Gera build para produção             |
| `npm run start` | Executa a aplicação em produção      |
| `npm run lint`  | Executa o linter                     |

### Fluxo Básico

1. **Fazer login** com um usuário cadastrado
2. **Cadastrar categorias** para organizar os produtos
3. **Cadastrar produtos** informando categoria, preço e quantidades mínima/máxima
4. **Registrar movimentações** de entrada e saída no estoque
5. **Acompanhar o dashboard** com valor do estoque, gráficos e alertas
6. **Gerenciar usuários** (apenas administradores)

## 📁 Estrutura do Projeto

```
frontend/
├── actions/              # Server Actions do Next.js
│   ├── auth.ts
│   ├── category.ts
│   ├── move.ts
│   ├── product.ts
│   └── user.ts
├── app/                  # App Router do Next.js
│   ├── (painel)/         # Rotas protegidas (layout do painel)
│   │   ├── categories/
│   │   ├── dashboard/
│   │   ├── moves/
│   │   ├── products/
│   │   ├── users/
│   │   └── layout.tsx
│   ├── login/            # Página de login
│   ├── globals.css
│   ├── layout.tsx
│   └── page.tsx
├── components/           # Componentes React reutilizáveis
│   ├── ui/               # Componentes de UI (Radix + Tailwind)
│   ├── categories/
│   ├── dashboard/
│   ├── moves/
│   ├── products/
│   ├── users/
│   ├── sidebar.tsx
│   ├── theme-provider.tsx
│   └── theme-toggle.tsx
├── hooks/                # Custom hooks
│   └── use-mobile.ts
├── lib/                  # Utilitários e clientes compartilhados
│   ├── server-api.ts     # Instância Axios para Server Actions
│   └── utils.ts
├── schemas/              # Schemas de validação (Zod)
│   ├── category.ts
│   ├── move.ts
│   ├── product.ts
│   └── user.ts
├── services/             # Funções de consumo da API
│   ├── auth.ts
│   ├── category.ts
│   ├── dashboard.ts
│   ├── move.ts
│   ├── product.ts
│   └── user.ts
├── types/                # Tipagens TypeScript
│   ├── api.ts
│   ├── category.ts
│   ├── dashboard.ts
│   ├── move.ts
│   ├── product.ts
│   └── user.ts
├── public/               # Arquivos estáticos públicos
├── endpoints.md          # Documentação dos endpoints da API
├── next.config.ts        # Configuração do Next.js
├── tsconfig.json         # Configuração do TypeScript
├── package.json          # Informações do projeto e dependências
└── package-lock.json     # Lock de dependências do npm
```

---

⭐ Se este projeto foi útil para você, considere dar uma estrela no repositório!
