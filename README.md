#  Ufology Investigation Unit - Infraestrutura e CI/CD

![Status do Build](https://img.shields.io/github/actions/workflow/status/machadocalebe/At_DevOps/run-monitor.yml?branch=main&label=CI%2FCD%20Pipeline&style=for-the-badge)

Este repositório contém a infraestrutura como código (Kubernetes), a conteinerização e as esteiras avançadas de CI/CD para a operação *Ufology*.

##  O Papel do Git na Integração e Entrega Contínuas (DevOps)
O Git é a espinha dorsal de qualquer operação de DevOps moderna. Ele não serve apenas para guardar código, mas atua como a única fonte de verdade (Single Source of Truth) que aciona as automações.
- **Branches (Ramificações):** Permitem o desenvolvimento isolado de features. No contexto de CI/CD, trabalhar em branches (como `ci/setup`) garante que testes de integridade sejam executados antes de mesclar o código na `main`, protegendo o ambiente de produção contra falhas.
- **Tags e Releases:** O versionamento semântico via Tags (ex: `v1.0.0`) é crucial para a rastreabilidade. Em um pipeline, as tags são usadas para disparar o empacotamento de artefatos fixos e gerar *Releases* oficiais, permitindo rollbacks rápidos e seguros caso o deploy falhe.

##  Arquitetura Kubernetes (Missões 1, 2 e 4)
Provisionado no namespace `ufology`, contendo PostgreSQL, Redis e a aplicação UfoTracker, utilizando `ConfigMap` e `Secret` para injeção de dependências sem hardcode. (Manifestos no arquivo `k8s-ufology.yaml`).

##  Dockerização (Missão 3)
A aplicação `ufoTracker` foi conteinerizada com Dockerfile Multi-stage.
- **Imagem Oficial no Docker Hub:** [https://hub.docker.com/r/klbcode/ufotracker](https://hub.docker.com/r/klbcode/ufotracker)

##  Workflows Avançados de CI/CD (GitHub Actions)
Para garantir as melhores práticas de entrega contínua, o arquivo `run-monitor.yml` implementa:
1. **Artefatos:** Uso do `upload-artifact` para empacotamento do JAR.
2. **Ambientes Protegidos:** O Job de deploy utiliza o environment `production`, exigindo **aprovação manual** antes de executar alterações críticas.
3. **Segurança e Variáveis:** Validação de segurança utilizando `GITHUB_TOKEN`, contextos de `secrets` (ex: `API_KEY`) e variáveis dinâmicas (`vars.PROD_DOMAIN`) em escopos de workflow, job e step.
4. **Monitoramento e Diagnóstico:** Implementação de logs de debug (`ACTIONS_STEP_DEBUG=true`), uso de comandos interpretados `::warning::` e `::error::` para validação condicional, e geração de *Job Summaries* dinâmicos (`$GITHUB_STEP_SUMMARY`) para auditoria visual da execução.