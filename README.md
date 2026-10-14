# kanban-automation

Automação do Kanban (GitHub Projects V2) **sem nenhum arquivo nos repositórios do produto** — a "opção B" do [laboratório](https://github.com/Gabs-X-Lab/github-projects-lab).

Uma reconciliação agendada (`.github/workflows/kanban-reconcile.yml`) lê pela API o estado dos PRs e Issues dos repositórios listados em `TARGET_REPOSITORIES` e move os cards do Project:

| Estado real | Coluna |
|---|---|
| Issue com branch vinculada (*Create a branch* / `gh issue develop`) | In Progress |
| PR rascunho | Draft PR |
| PR pronto para revisão | In Review |
| PR aprovado | Approved |
| Alterações pedidas | In Progress |
| PR fechado sem merge | To Do |

Issue nova → Backlog, merge → Done e Issue reaberta → To Do ficam com os **workflows nativos** do Project.

**Diferença para a automação dentro do repositório (opção C):** atraso de até um intervalo do cron (mínimo de 5 minutos), em troca de não alterar o código do produto.

## Configuração

1. GitHub App com *Issues: read*, *Pull requests: read*, *Organization projects: read and write*, **instalado apenas nos repositórios do produto** (não precisa estar instalado aqui).
2. Neste repositório, *Settings → Secrets and variables → Actions*:

   | Nome | Tipo | Exemplo |
   |---|---|---|
   | `PROJECT_APP_PRIVATE_KEY` | Secret | conteúdo do `.pem` |
   | `PROJECT_APP_CLIENT_ID` | Variable | `Iv23...` |
   | `PROJECT_OWNER` | Variable | `Gabs-X-Lab` |
   | `PROJECT_NUMBER` | Variable | `1` |
   | `OWNER_TYPE` | Variable | `organization` |
   | `TARGET_REPOSITORIES` | Variable | `github-projects-lab` (vários: separados por vírgula) |

3. *Actions → Kanban reconcile → Run workflow* para testar na hora.

`scripts/project-status.sh` é uma cópia do script do laboratório (variável `TARGET_REPOSITORY` para operar sobre outro repositório).

## Observação sobre o agendamento

O GitHub registra o `schedule` a partir de um push na branch padrão. Depois de desligar e religar o workflow (ou logo após criar o repositório), o cron pode não disparar até o próximo push — no laboratório, ficou 28 minutos sem nenhuma execução agendada. Para testar na hora, use *Run workflow*.
