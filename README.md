# console

Console Angular 19 para gestão e teste de políticas do RuleEngineLabs.

## Visão geral

- Listar, criar e versionar políticas (via API admin do engine)
- Rodar preview com trace completo (via `/preview` do engine)

## Requisitos

- Node 24+
- Angular CLI 19
- Docker (para integração)

## Desenvolvimento

```bash
npm install
ng serve
ng test
ng build
```

## CI/CD

| Branch | Trigger | Ação |
|---|---|---|
| `feature/*` | push | lint + build + test → abre PR para `develop` |
| `develop` | push/merge | lint + build + test → cria `release/vX.Y.Z` + PR para `main` |
| `release/*` | push | lint + build + test + Docker build → valida artefato |
| `main` | PR merge | produção (deploy não configurado ainda) |
