## Context

Hoje o Docker Native Manager opera somente sobre o daemon local. Em ambientes de operação real, times gerenciam múltiplos hosts (bare metal, VMs e nodes de cluster), o que exige alternância segura de contexto e visibilidade de conectividade por servidor.

A arquitetura atual já possui backend Rust com Bollard e frontend React/Tauri, o que permite introduzir uma camada de contexto remoto sem trocar stack. Restrições principais: evitar vazamento de credenciais, minimizar regressão no fluxo local existente e garantir que ações sejam executadas no contexto correto.

## Goals / Non-Goals

**Goals:**
- Permitir cadastro e gerenciamento de múltiplos servidores Docker remotos.
- Suportar seleção de contexto ativo para todas as operações de gerenciamento.
- Exibir estado de conexão por servidor e erros de autenticação/conectividade.
- Manter compatibilidade com uso local como contexto padrão.
- Definir base extensível para adicionar políticas de segurança e health checks futuros.

**Non-Goals:**
- Implementar orquestração Kubernetes ou Swarm avançada nesta etapa.
- Adicionar RBAC multiusuário no aplicativo.
- Sincronização em nuvem de credenciais/contextos.
- Telemetria avançada de performance por host remoto.

## Decisions

1. Introduzir entidade de domínio `DockerContext` (id, nome, endpoint, tipo, authRef, tlsConfigRef, flags) persistida localmente.
   - **Rationale:** separa configuração de conexão da lógica de operações Docker e facilita expansão futura.
   - **Alternatives considered:**
     - Usar apenas variáveis globais de ambiente (`DOCKER_HOST`, `DOCKER_CERT_PATH`): descartado por baixa segurança e baixa flexibilidade no app desktop.
     - Salvar apenas endpoint sem metadados: descartado por impedir UX robusta de status e seleção.

2. Resolver contexto ativo no backend Tauri (não no frontend), e exigir `contextId` opcional nos comandos Docker.
   - **Rationale:** reduz risco de inconsistência e centraliza validação/autorização de operação por contexto.
   - **Alternatives considered:**
     - Resolver contexto no frontend e enviar endpoint bruto em cada chamada: descartado por ampliar superfície de erro e exposição de dados sensíveis.

3. Implementar pool/cache de clientes Bollard por `contextId` com invalidação em alteração de configuração.
   - **Rationale:** reduz overhead de reconexão e melhora responsividade sem perder consistência.
   - **Alternatives considered:**
     - Recriar cliente em toda chamada: simples, porém custo maior e UX pior sob latência.

4. Credenciais não devem ser persistidas em texto puro no estado de UI.
   - **Rationale:** requisito de segurança mínimo para ambientes corporativos.
   - **Alternatives considered:**
     - Persistência em arquivo plano sem proteção: descartado por risco alto.

5. Compatibilidade retroativa: inicializar `local-default` como contexto padrão em instalações existentes.
   - **Rationale:** evita breaking change para usuários atuais.

## Risks / Trade-offs

- [Configuração incorreta de endpoint remoto] → Mitigação: validação sintática + teste de conexão antes de salvar.
- [Operação executada no host errado] → Mitigação: indicador visível de contexto ativo e confirmação em ações destrutivas com nome do contexto.
- [Latência e timeouts em redes remotas] → Mitigação: timeout configurável por contexto e feedback de estado no UI.
- [Gestão de segredos insuficiente no desktop] → Mitigação: encapsular armazenamento sensível no backend e evitar exposição em payloads de listagem.

## Migration Plan

1. Introduzir modelo `DockerContext` e persistência local.
2. Criar comandos Tauri para CRUD de contextos e seleção do contexto ativo.
3. Adaptar camada Docker para resolver cliente por contexto.
4. Migrar comandos existentes para aceitar `contextId` opcional mantendo fallback no contexto ativo.
5. Adicionar UI de gestão de servidores e seletor global de contexto.
6. Criar migração de dados para gerar `local-default` em bases antigas.
7. Validar regressão no modo local e smoke tests com endpoint remoto.

Rollback:
- Manter feature flag `remote_contexts` no backend para desligar fluxo remoto e voltar ao modo local em caso de falha crítica.

## Open Questions

- Qual backend de armazenamento seguro será adotado para segredos (chaveiro do SO vs criptografia local)?
- O suporte SSH será nativo nesta fase ou ficará limitado a TCP/TLS inicialmente?
- Quais campos mínimos devem aparecer no status de saúde de cada contexto (ping, versão daemon, último erro)?
