# 📚 Biblioteca — Projeto final de Desenvolvimento Web

**Vídeo:** COLOCAR_AQUI_O_LINK_DO_VIDEO

Sistema web completo de uma biblioteca: autores, livros, leitores e empréstimos, com cadastro e login de usuário.




| Caio Dantas da Silveira | 33361436 | Todo o projeto: banco (schema.prisma, migrations, seed), API (rotas, controllers, services, repositories, Swagger), schemas Zod no shared, login/cadastro com bcrypt e frontend (telas, modais, selects, busca) |



 Biblioteca

| Tabela | Campos principais | Ligações |
|---|---|---|
| **Autor** | nome, nacionalidade | tem vários livros |
| **Livro** | titulo, **isbn (@unique)**, anoPublicacao, autorId | **Livro → Autor** |
| **Leitor** | nome, **cpf (@unique)**, **email (@unique)**, telefone | tem vários empréstimos |
| **Emprestimo** | dataEmprestimo, dataPrevista, dataDevolucao, livroId, leitorId | **Empréstimo → Livro e Leitor** (ligada a duas) |
| **Usuario** (login) | nome, **email (@unique)**, senha (hash bcrypt) | — |

## Tecnologias

- **Banco:** PostgreSQL (Docker) + Prisma
- **API:** Node, Express e TypeScript, em camadas: `routes → controllers → services → repositories`
- **Validação:** Zod, com os schemas no pacote `shared`, usado pela API e pelo frontend
- **Documentação:** Swagger em `http://localhost:3333/docs`
- **Frontend:** React, Vite, TypeScript, Mantine e Axios
- **Senha:** bcrypt

## Como rodar do zero

Precisa ter instalado: **Node.js 20 ou mais novo**, **npm** e **Docker** (com Docker Compose).

```bash
# 1. Clonar e entrar na pasta
git clone https://github.com/USUARIO/REPOSITORIO.git
cd REPOSITORIO

# 2. Subir o banco PostgreSQL
docker compose up -d

# 3. Criar o .env da API a partir do exemplo
cp api/.env.example api/.env
#    (no Windows/PowerShell: copy api\.env.example api\.env)

# 4. Instalar tudo (shared, api e frontend) — também gera o Prisma Client
npm install

# 5. Rodar as migrations (cria as tabelas) e o seed (dados de exemplo)
npm run migrate -w api
npm run seed -w api

# 6. Subir a API (deixe este terminal aberto)
npm run dev -w api
#    API:     http://localhost:3333
#    Swagger: http://localhost:3333/docs

# 7. Em outro terminal, subir o frontend
npm run dev -w frontend
#    Abrir http://localhost:5173
```

> Sem Docker: dá para usar um PostgreSQL instalado na máquina. Crie um banco `biblioteca` e ajuste a `DATABASE_URL` no `api/.env`.

### Builds

```bash
npm run build -w api
npm run build -w frontend
```

## Usuário para entrar no sistema 

- **E-mail:** `professor@biblioteca.com`
- **Senha:** `123456`

## Rotas da API

| Método | Rota | O que faz |
|---|---|---|
| POST | `/usuarios` | Cadastro de usuário (senha com bcrypt) |
| POST | `/login` | Login |
| GET / POST | `/autores` | Lista / cria |
| GET / PUT / DELETE | `/autores/:id` | Busca / edita / remove |
| GET / POST | `/livros` | Lista (com o nome do autor) / cria |
| GET / PUT / DELETE | `/livros/:id` | Busca / edita / remove |
| GET / POST | `/leitores` | Lista / cria |
| GET / PUT / DELETE | `/leitores/:id` | Busca / edita / remove |
| GET / POST | `/emprestimos` | Lista (com título do livro e nome do leitor) / cria |
| GET / PUT / DELETE | `/emprestimos/:id` | Busca / edita / remove |

Status: `201` ao criar, `204` ao remover, `404` id que não existe, `400` dado inválido (com a mensagem de cada campo), `409` valor repetido em campo único (CPF, e-mail, ISBN) ou remoção de registro que tem outros ligados a ele.

## Estrutura

```
shared/      schemas Zod usados pela API e pelo frontend
api/
  prisma/    schema.prisma, migrations e seed.ts
  src/
    routes/        URLs e validarBody
    controllers/   lê a requisição e escolhe o status
    services/      regras de negócio (bcrypt, 404, livro já emprestado)
    repositories/  única camada que usa o Prisma
    middlewares/   validarSchema.ts (validarBody) e tratarErros.ts
    docs/          swagger.ts
frontend/
  src/
    services/api.ts  todas as chamadas com Axios
    pages/           Login, Cadastro, Início, Autores, Livros, Leitores, Empréstimos
    components/      Layout (menu), ConfirmarRemocao, RotaProtegida
```
