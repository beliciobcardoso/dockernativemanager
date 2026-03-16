## Branch de implementação

- `feature/add-remote-docker-cluster-management`

## Pré-requisito obrigatório (antes de iniciar)

- [ ] Criar e trocar para a branch de desenvolvimento da feature antes de executar qualquer tarefa:
	- `git checkout develop`
	- `git pull`
	- `git checkout -b feature/add-remote-docker-cluster-management`

## 1. Modelagem e persistência de contextos

- [ ] 1.1 Definir struct `DockerContext` no backend (id, nome, tipo, endpoint, metadados de auth)
- [ ] 1.2 Implementar persistência local de contextos com migração para criar `local-default`
- [ ] 1.3 Implementar validações de domínio (nome único, endpoint válido, campos obrigatórios por tipo)

## 2. Comandos Tauri para gestão de contextos

- [ ] 2.1 Criar comandos para listar, criar, atualizar e remover contextos
- [ ] 2.2 Criar comando para testar conectividade e retornar erro estruturado
- [ ] 2.3 Criar comando para definir e consultar contexto ativo

## 3. Integração Docker por contexto

- [ ] 3.1 Implementar resolvedor de cliente Bollard por `contextId` com cache e invalidação
- [ ] 3.2 Adaptar operações Docker existentes para usar contexto ativo por padrão
- [ ] 3.3 Permitir override explícito de `contextId` em comandos críticos quando aplicável

## 4. Segurança e proteção de dados sensíveis

- [ ] 4.1 Encapsular armazenamento de segredos no backend sem expor material bruto ao frontend
- [ ] 4.2 Sanitizar respostas de listagem para remover senha/chave/certificado privado
- [ ] 4.3 Garantir ausência de segredos em logs de erro e telemetria

## 5. Interface de usuário para multi-servidor

- [ ] 5.1 Criar tela/área de configuração de servidores com CRUD de contextos
- [ ] 5.2 Adicionar seletor global de contexto ativo no layout
- [ ] 5.3 Exibir status por contexto (conectado, desconectado, último erro)

## 6. Qualidade e validação funcional

- [ ] 6.1 Validar regressão do fluxo local com `local-default`
- [ ] 6.2 Adicionar testes de integração para troca de contexto e isolamento de operações
- [ ] 6.3 Executar checklist manual de cenários da spec (cadastro, teste de conexão, switch e ações destrutivas)
