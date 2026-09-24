# RuleEngineLabs/console — contexto do projeto

## O que é
- Frontend Angular 19 para gestão e teste de políticas do RuleEngineLabs.
- Projeto novo e separado — **não é** o "visual rule engine" antigo, que continua mirando o motor MVEL.
- Escopo confirmado: listar/gerenciar políticas e rodar preview com trace completo.

## Repo irmão
- `RuleEngineLabs/engine` (Go) tem o contrato completo — schema de política, envelope de resposta, formato de trace — no `CLAUDE.md` dele. Esse arquivo aqui traz só um resumo focado no que a UI precisa consumir; o de lá é a fonte de verdade pra regra de validação.
- Forma de sincronizar os dois sem duplicar shape à mão: publicar o schema do `policy` como JSON Schema a partir do `engine`. Isso ainda não tem pipeline — ficou em aberto no CI/CD do `engine`.

## APIs que o console consome
- **admin** (`cmd/admin` do engine) — CRUD de política, versionamento, dispara invalidação. Rotas e payloads exatos **ainda não foram especificados** — é o primeiro contrato a fechar antes de construir as telas de gestão/listagem.
- **engine, rota `/preview`** (`cmd/engine`) — recebe a política inteira no payload + entrada de teste, roda sem cache. Resposta:
  - Sucesso: `{ state, data }`
  - Erro geral (não veio de nenhum state, ex.: campo obrigatório ausente): `{ error, message }`
  - Sempre inclui `trace`: array de passos por state, com `attempts` (status + duration) quando o state tem retry configurado, e falhas de `parallel` agrupadas por assinatura de `result.status`

## Shape de política (resumo pra renderizar/editar)
- Grafo de states: `id`, `kind`, `transitions` (`[{when, to}]`, avaliadas em ordem), `fallback` (obrigatório em todo state que não for `response`)
- `kind`: `execution`, `dbQuery` (fase 3), `apiCall` (rest na fase 2; grpc/soap depois), `parallel`, `response` (terminal — sem `transitions`/`fallback`/`contextKey`, tem `status` + `data`)
- Todo campo em camelCase (`contextKey`, `timeoutMs`, `maxConcurrency` etc.) — exceção: nome de operação nativa do provider (`getItem`, `hget`) mantém o nome real
- `parallel`: `over` (path pro array), `each` (id do state por item), `maxConcurrency` (obrigatório, 1–100)

## Em aberto
- Contrato exato da API admin (rotas, payloads de CRUD/versionamento)
- Pipeline de publicação do JSON Schema do engine pro console consumir sem duplicar à mão
- Estrutura interna do Angular — componentes, roteamento, state management: nada disso foi discutido ainda
- Licença MIT gerada como proposta pro repo — pergunta se fica público ou proprietário nunca foi respondida

## Repo
- `RuleEngineLabs/console`, Angular 19
- `.gitignore` próprio (build Angular, node_modules, cache do CLI) já gerado
