# Ufology Investigation Unit - Infraestrutura e CI/CD

Este repositório contém a entrega das missões de Infraestrutura como Código (Kubernetes), Conteinerização e Esteiras de CI/CD desenvolvidas para a operação *Ufology*.

## Arquitetura Kubernetes (Missões 1, 2 e 4)
Todos os recursos foram provisionados no namespace isolado `ufology`. A arquitetura declarada no arquivo `k8s-ufology.yaml` é composta por:
- **PostgreSQL:** Banco de dados relacional (Imagem `leogloriainfnet/ufodb`).
- **Redis (Alpine):** Camada de cache em memória.
- **UfoTracker App:** Aplicação principal executando com 2 réplicas.
- **Segurança e Configuração:** Uso de `ConfigMap` (`app-config`) e `Secret` (`db-secret`) para injeção de variáveis de ambiente no container da aplicação, evitando hardcode de credenciais.

## Dockerização (Missão 3)
A aplicação `ufoTracker` foi conteinerizada utilizando um Dockerfile Multi-stage build (Maven + Alpine JRE) e disponibilizada publicamente.

- **Link da imagem no Docker Hub:** https://hub.docker.com/r/klbcode/ufotracker

**Comandos utilizados para Build e Push:**
\`\`\` bash
### Build da imagem
docker build -t klbcode/ufotracker:v1 .

### Envio para o repositório público
docker push klbcode/ufotracker:v1
\`\`\`

## GitHub Actions (Partes 2 e 3)
Foram implementados 5 workflows na pasta `.github/workflows` para automação de CI/CD:

1. **Hello CI/CD (`hello.yml`):** Disparado em push. Exibe mensagem de log.
2. **Testes (`tests.yml`):** Disparado apenas em Pull Requests.
3. **Build Java (`gradle-ci.yml`):** Disparado em push na branch `main`. Executa a simulação do build via Maven.
4. **Env Variables (`env-demo.yml`):** Demonstração do uso de variáveis de escopo local (`DEPLOY_ENV=staging`).
5. **Segurança (`secret-demo.yml`):** Validação de secret de repositório (`API_KEY`) mascarado nos logs de execução.

---

##  Fundamentação Teórica: Runners do GitHub

**Diferença entre runners hospedados pelo GitHub e auto-hospedados (Self-hosted):**

* **GitHub-hosted runners:** São máquinas virtuais gerenciadas e mantidas inteiramente pelo próprio GitHub.
    * *Vantagens:* Zero esforço de manutenção de infraestrutura, ambiente efêmero (uma VM limpa por job garante consistência) e fácil configuração inicial.
    * *Desvantagens:* Limite de minutos gratuitos mensais, hardware padrão (dificuldade de escalar para builds ultra-pesados) e IP dinâmico (exige configurações extras para acessar bancos de dados em redes privadas).
* **Self-hosted runners:** São servidores próprios (físicos, VMs na nuvem ou máquina local) configurados pela própria equipe para escutar e executar as Actions do GitHub.
    * *Vantagens:* Controle total sobre o hardware, sem limite de minutos tarifados pelo GitHub, acesso nativo a redes internas/VPNs da empresa e possibilidade de cache local persistente para acelerar builds.
    * *Desvantagens:* A equipe de DevOps precisa aplicar patches de segurança, manter o SO atualizado e gerenciar a limpeza do ambiente entre execuções para evitar que "lixo" de um build afete o próximo.