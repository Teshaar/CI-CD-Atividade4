# Projeto: Workflow de Deploy Contínuo (CD)

Este repositório contém a implementação de um workflow de Deploy Contínuo utilizando o GitHub Actions, conforme os requisitos da atividade proposta.

## Como o Pipeline Funciona
O pipeline é acionado automaticamente a cada *push* na branch `main` (ou manualmente via `workflow_dispatch`). Ele é composto por três *jobs* executados de forma sequencial, garantindo que um código só avance para o próximo estágio se o anterior for bem-sucedido:
1. **ci**: Realiza o checkout do código e simula a execução de testes e rotinas de integração contínua.
2. **deploy-staging**: Depende do job `ci` (usando a tag `needs: ci`). Executa o deploy no ambiente de testes.
3. **deploy-production**: Depende do job `deploy-staging`. Executa o deploy final, mas requer aprovação manual antes de rodar.

## O Papel de Cada Ambiente
* **Staging (`environment: staging`)**: É um ambiente de homologação, uma réplica do ambiente de produção. Serve para testar as alterações em uma infraestrutura real antes que elas cheguem aos usuários finais.
* **Produção (`environment: production`)**: É o ambiente real ("ao vivo") acessado pelos usuários finais. Alterações só chegam aqui após serem validadas no Staging e aprovadas.

## Onde Ocorre a Aprovação Manual
A aprovação manual ocorre na transição entre o job de `deploy-staging` e `deploy-production`. 
**Como foi configurada:**
1. Nas configurações do repositório (`Settings` > `Environments`), foi criado um ambiente chamado `production`.
2. Nas regras de proteção desse ambiente, a opção **"Required reviewers"** (Revisores obrigatórios) foi ativada.
3. Isso faz com que o workflow pause a execução do job `deploy-production` até que um revisor autorizado clique em "Review deployments" na aba Actions e aprove o deploy.

## Como os Secrets Seriam Utilizados
Os *secrets* (como senhas de banco de dados ou chaves de API) não ficam expostos no código. Eles são cadastrados diretamente no GitHub (em `Settings` > `Secrets and variables` > `Actions` > `Environment secrets`). 
No arquivo YAML, eles são chamados de forma segura utilizando a sintaxe `${{ secrets.NOME_DO_SECRET }}`. O GitHub Actions encobre automaticamente os valores reais nos logs (mostrando `***`).

## Como Visualizar a Execução do Workflow
Para acompanhar as execuções, verificar os logs ou realizar as aprovações manuais:
1. Acesse o repositório no GitHub.
2. Clique na aba **"Actions"** no menu superior.
3. Selecione o workflow "Continuous Deployment Workflow" na barra lateral esquerda para ver o histórico de execuções.

---

## Evidências da Execução
<img width="877" height="790" alt="ATV 4 - aprovar" src="https://github.com/user-attachments/assets/cb3e92ad-14c7-4683-a106-8921d8efb02a" />
<img width="874" height="651" alt="Aprovado" src="https://github.com/user-attachments/assets/112c1b58-1680-492d-a2cf-19f9156f369a" />
