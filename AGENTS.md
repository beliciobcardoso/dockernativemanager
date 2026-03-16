# AGENTS.md

Este arquivo orienta agentes de IA e colaboradores ao trabalhar neste repositório.

## Visão geral do projeto

- Nome: **Docker Native Manager**
- Tipo: Desktop app nativa com **Tauri v2**
- Frontend: **React + TypeScript + Vite + Tailwind + shadcn/ui**
- Backend nativo: **Rust** em `src-tauri/`
- Integração Docker: crate Rust `bollard`

## Estrutura principal

- `src/` → aplicação frontend
- `pages/` → telas (Containers, Images, Networks, Volumes, Stacks, etc.)
- `components/layout/` → layout e sidebar
- `components/ui/` → componentes base (shadcn/radix)
- `hooks/` → hooks de app (ex.: eventos Docker)
- `lib/` → integração e utilitários (ex.: `docker.ts`)
- `src-tauri/` → aplicação Rust/Tauri
- `src/main.rs` → entrypoint do backend
- `Cargo.toml` → dependências Rust/Tauri
- `tauri.conf.json` → configuração do app

## Comandos de desenvolvimento

No diretório raiz:

- Instalar deps JS: `pnpm install`
- Rodar frontend (Vite): `pnpm dev`
- Rodar desktop Tauri: `pnpm tauri dev` ou `pnpm tauri:dev`
- Build frontend: `pnpm build`
- Build desktop: `pnpm tauri build`
- Lint: `pnpm lint`

## Pré-requisitos locais (Linux)

- Node.js 20+
- `pnpm`
- Rust (`cargo`, `rustc`) no `PATH`
- Docker Engine/Desktop ativo
- Dependências de sistema para Tauri:
  - `build-essential`
  - `libwebkit2gtk-4.1-dev`
  - `librsvg2-dev`

Comando Ubuntu:

```bash
sudo apt-get update && sudo apt-get install -y build-essential libwebkit2gtk-4.1-dev librsvg2-dev
```

## Troubleshooting conhecido

### `failed to run 'cargo metadata' ... (os error 2)`

Causa comum: `cargo` ausente no ambiente atual.

Correção:

```bash
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh -s -- -y
source "$HOME/.cargo/env"
```

Recomendado persistir no shell:

```bash
echo 'source "$HOME/.cargo/env"' >> ~/.zshrc
source ~/.zshrc
```

### `linker 'cc' not found`

Causa comum: toolchain C não instalado.

Correção: instalar `build-essential` (veja seção de pré-requisitos).

## Convenções para alterações

- Faça mudanças **cirúrgicas** e focadas no escopo solicitado.
- Preserve APIs e estrutura existentes, evitando refatorações amplas sem necessidade.
- Ao mexer em Tauri, mantenha alinhamento entre versões Rust e JS do ecossistema Tauri.
- Atualize documentação (`README.md`) quando adicionar comandos, fluxos ou troubleshooting.

## Fluxo OpenSpec (quando aplicável)

Este repositório possui recursos em `.github/prompts/` e `.github/skills/` para fluxo OpenSpec.

- Propor mudança: usar o fluxo `opsx:propose`.
- Implementar mudança proposta: usar `opsx:apply`.

Ao criar proposta, gere artefatos de mudança (ex.: `proposal.md`, `design.md`, `tasks.md`) antes da implementação.
