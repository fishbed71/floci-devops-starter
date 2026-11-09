# Floci DevOps Lab — 스타터

Windows 11 노트북 한 대에서 **AWS 계정 없이** 웹서버·DB 인프라를 코드로 만들고, 스케일을 조정하고,
GitHub Actions self-hosted runner 로 배포까지 하는 실습의 **시작 저장소**입니다.
[독학 교재](https://claude.ai/artifact/MJP8Ha1PmNmQUFw7YxGXkL)를 따라 이 저장소를 채워 갑니다.
코드는 직접 쓰지 않고, AI 코딩 에이전트(Claude Code 또는 Google Antigravity)에게 `prompts/` 의 프롬프트로 시킵니다.

## 들어 있는 것

| 경로 | 내용 |
| --- | --- |
| [AGENTS.md](AGENTS.md), [CLAUDE.md](CLAUDE.md), `.agent/rules/` | AI 가 항상 지키는 규칙 (Floci 전용, apply·destroy 금지, 비밀값 금지, 이름 규칙) |
| [prompts/](prompts) | 프롬프트 18개 (생성 9 · 관리 9). 사용법은 [prompts/README.md](prompts/README.md) |
| `.claude/commands/`, `.agent/workflows/` | 위 프롬프트의 슬래시 커맨드 (`/tf-network` 등) |
| `.claude/settings.json` | Claude Code 가 `terraform apply`·`destroy` 를 실행하지 못하게 막는 설정 |
| [app/app.py](app/app.py) | 방명록 웹앱 (완성본. 이 과정의 주제가 아님) |
| [scripts/](scripts) | 보조 스크립트: `floci-env.ps1`, `smoke-test.ps1`, `scale.ps1`, `new-ssh-key.ps1`, `sync-agent-prompts.ps1` |
| [docs/](docs) | runner 설치, 트러블슈팅 |

## 여러분이 만들 것

| 장 | 커맨드 | 만들어지는 파일 |
| --- | --- | --- |
| 4 | `/floci-compose` | `compose.yaml`, `.gitignore` 에 `data/` 추가 |
| 5 | `/app-docker`, `/docker-review` | `app/Dockerfile`, `app/requirements.txt`, `app/compose.dev.yaml`, `app/.dockerignore` |
| 6 | `/tf-foundation` | `infra/envs/dev/{versions,variables,providers,backend}.tf`, `scripts/bootstrap-state.ps1` |
| 7 | `/tf-network`, `/tf-web-single` | `infra/modules/network/`, `infra/envs/dev/main.tf`, `web_single.tf` |
| 8 | `/tf-db` | `infra/modules/db/` |
| 9 | `/provisioning` | `infra/modules/web/templates/user-data.sh.tftpl`, `provision/ssm-run.ps1` |
| 10 | `/tf-web-asg`, `/scale-change` | `infra/modules/web/`, `terraform.tfvars` |
| 12 | `/gha-workflows`, `/security-review` | `.github/workflows/` 4개 |
| 13 | `/real-aws-diff` | `REAL_AWS.md` |

진행하면서 AI 가 틀린 곳과 고친 이유를 `AI_LOG.md` 에 기록합니다 (3장).

## 시작하기

사전 설치(교재 2장): WSL2, Docker Desktop, Git, GitHub CLI, Terraform ≥ 1.10, AWS CLI v2, Claude Code 또는 Antigravity.

```powershell
New-Item -ItemType Directory -Force C:\labs | Out-Null
cd C:\labs
git clone https://github.com/fishbed71/floci-devops-starter.git floci-devops-lab
cd floci-devops-lab
claude          # 또는 Antigravity 에서 이 폴더 열기
```

에이전트 입력창에 `/` 를 입력해 `/floci-compose` 같은 커맨드가 보이면 준비가 끝났습니다. 교재 3장부터 따라갑니다.

11장에서 내 계정의 **private** 저장소로 옮깁니다. clone 한 주소(origin)는 그때 `starter` 로 이름을 바꿉니다.

## 주의

- 저장소를 public 으로 만들고 self-hosted runner 를 붙이지 마세요. 누구나 PR 로 내 PC 에서 코드를 실행할 수 있게 됩니다.
- `terraform apply`·`destroy` 는 AI 가 아니라 사람이 직접 실행합니다.
- 실제 AWS 자격 증명은 필요 없고, 넣어서도 안 됩니다. 모든 요청은 `http://localhost:4566`(Floci)로 갑니다.
