## Why

Atualmente o aplicativo gerencia apenas o Docker local, o que limita o uso em ambientes com múltiplos hosts e clusters distribuídos. Precisamos permitir conexões remotas para que operadores possam administrar vários servidores Docker em um único painel.

## What Changes

- Adicionar cadastro de múltiplos endpoints Docker remotos (TCP/TLS e SSH) com nome amigável.
- Permitir alternar o contexto ativo de servidor sem reiniciar o aplicativo.
- Isolar operações por contexto (containers, imagens, volumes, redes e stacks) para evitar ações no host errado.
- Persistir lista de servidores e metadados de conexão no app (sem expor segredos em texto puro no frontend).
- Exibir estado da conexão e erro por servidor (conectado, desconectado, falha de autenticação, timeout).

## Capabilities

### New Capabilities
- `remote-docker-contexts`: Gerencia cadastro, validação, seleção e uso de múltiplos contextos Docker remotos no aplicativo.

### Modified Capabilities
- Nenhuma.

## Impact

- Frontend: novas telas/fluxos para lista de servidores, formulário de conexão e seleção de contexto ativo.
- Backend Tauri (Rust): camada para criar clientes Bollard por contexto remoto e manter estado de conexão.
- Segurança: tratamento de credenciais/segredos com armazenamento seguro local (evitando exposição direta no estado do UI).
- APIs internas: comandos Tauri precisarão receber ou resolver contexto ativo para operações Docker.
- Observabilidade UX: mensagens de erro e status de conectividade por servidor.