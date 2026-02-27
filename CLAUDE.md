# CLAUDE.md — AutoMarket

## Projeto
Plataforma full stack de compra e venda de carros de segunda mão.
Monorepo com `/client` (Next.js 14) e `/server` (Fastify + Node.js).

## Stack
- **Frontend:** Next.js 14 App Router + TypeScript + Tailwind CSS + shadcn/ui
- **Backend:** Node.js + Fastify + TypeScript + MongoDB (Mongoose)
- **Auth:** JWT + Google OAuth 2.0
- **Upload:** Cloudinary
- **Validação:** Zod em TODAS as rotas e formulários
- **CI/CD:** GitHub Actions + Pull Requests

---

## Workflow Orchestration

### 1. Plan Mode Default
- Entrar em modo de planejamento para QUALQUER tarefa não trivial (3+ passos ou decisões arquiteturais)
- Se algo der errado, PARE e replaneje imediatamente — não continue forçando
- Usar plan mode para etapas de verificação, não só para construção
- Escrever specs detalhadas upfront para reduzir ambiguidade

### 2. Subagent Strategy
- Usar subagentes liberalmente para manter o contexto principal limpo
- Delegar pesquisa, exploração e análises paralelas para subagentes
- Para problemas complexos, usar mais compute via subagentes
- Uma tarefa por subagente para execução focada

### 3. Self-Improvement Loop
- Após QUALQUER correção do usuário: atualizar `tasks/lessons.md` com o padrão aprendido
- Escrever regras que previnam o mesmo erro
- Iterar implacavelmente sobre as lições até a taxa de erro cair
- Revisar lessons.md no início de cada sessão

### 4. Verification Before Done
- NUNCA marcar uma tarefa como completa sem provar que funciona
- Verificar diff de comportamento entre main e suas alterações quando relevante
- Perguntar: "Um engenheiro sênior aprovaria isso?"
- Rodar testes, checar logs, demonstrar corretude

### 5. Demand Elegance (Balanced)
- Para mudanças não triviais: pausar e perguntar "existe uma forma mais elegante?"
- Se uma correção parecer gambiarra: "Sabendo tudo que sei agora, implemente a solução elegante"
- Pular isso para correções simples e óbvias — não over-engineer
- Questionar o próprio trabalho antes de apresentar

### 6. Autonomous Bug Fixing
- Quando receber um bug report: apenas corrija. Não peça orientação
- Apontar para logs, erros, testes falhando — depois resolver
- Zero context switching necessário do usuário
- Corrigir testes de CI falhando sem precisar ser instruído

---

## Task Management

1. **Plan First:** Escrever plano em `tasks/todo.md` com itens checkáveis
2. **Verify Plan:** Confirmar antes de iniciar a implementação
3. **Track Progress:** Marcar itens como completos conforme avança
4. **Explain Changes:** Resumo de alto nível em cada etapa
5. **Document Results:** Adicionar seção de review ao `tasks/todo.md`
6. **Capture Lessons:** Atualizar `tasks/lessons.md` após correções

---

## Git & Pull Requests

### Mandatory flow
```
main (protected)
  └── develop
        └── feature/feature-name   ← development happens here
```

- **Never commit directly to `main` or `develop`**
- Every feature starts on a branch from `develop`
- When done: open Pull Request from `feature/*` → `develop`
- Merge to `main` only via PR with CI passing

### Creating a feature
```bash
git checkout develop
git pull origin develop
git checkout -b feature/descriptive-name
# ... implement ...
git push origin feature/descriptive-name
gh pr create --base develop --title "feat(scope): description" --body "## What changes\n..."
```

---

## Conventional Commits

> ⚠️ **MANDATORY: ALL commit messages MUST be written in English. No exceptions.**
> ❌ Wrong: `feat(server): adiciona rota de listagem`
> ✅ Correct: `feat(server): add car listing route`

Required format:
```
<type>(<scope>): <description in English, imperative mood, lowercase>

[optional body — what and why, not how]

[optional footer — BREAKING CHANGE or closes #issue]
```

### Types
| Type | When to use |
|------|-------------|
| `feat` | New feature |
| `fix` | Bug fix |
| `refactor` | Refactor without behavior change |
| `test` | Add or fix tests |
| `docs` | Documentation |
| `chore` | Build, deps, configs |
| `ci` | CI/CD changes |
| `style` | Formatting, no logic change |
| `perf` | Performance improvement |

### Scopes
`client` | `server` | `auth` | `cars` | `upload` | `db` | `ci` | `deps`

### Correct examples
```
feat(server): add car listing route with filters and pagination
fix(client): fix null price display in CarCard
refactor(auth): extract JWT validation into reusable plugin
test(server): add integration tests for auth routes
ci: add lint and build workflow on pull request
chore(deps): upgrade fastify to v5
```

---

## CI/CD — GitHub Actions

### Available workflows

#### `ci.yml` — runs on every PR
- Lint (ESLint + Prettier)
- Type check (tsc --noEmit)
- Build (client and server)
- Unit tests

#### `cd.yml` — runs on merge to main
- Production build
- Deploy client → Vercel
- Deploy server → Railway

### Branch protection rules
- `main` and `develop` require approved PR + green CI to merge
- Squash merge required to keep history clean
- Branch deleted automatically after merge

---

## Code Rules

1. Always commit after a complete feature with a Conventional Commit message
2. Run `npm run build` in both before any push
3. Never commit with TypeScript errors
4. Always validate input with Zod in server routes
5. Photos always via Cloudinary — never store locally
6. Never commit `.env` or `.env.local` files
7. Reusable components in `/components`, pages in `/app`
8. API response always in the format: `{ data, error, message }`
9. Tests for every new server route
10. PR description must explain: what, why and how to test

---

## Task Files Structure

```
tasks/
├── todo.md       ← current plan with checkboxes
└── lessons.md    ← lessons learned (update after corrections)
```

### `tasks/todo.md` format
```markdown
# Task: Feature Name

## Plan
- [ ] Step 1
- [x] Step 2 (done)

## Review
What was done, what was left out, next steps.
```

### `tasks/lessons.md` format
```markdown
# Lessons Learned

## [date] — Error description
**Context:** what was being done
**Error:** what happened
**Root cause:** why it happened
**Rule:** never do X, always do Y
```
