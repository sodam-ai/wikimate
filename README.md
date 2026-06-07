# Wikimate (위키메이트)

> AI 에이전트에게 "이거 정리해줘"라고 하면, 흩어진 자료를 **옵시디언(원본)** 에 노트로 정리하고 **노션(색인)** 에 표로 색인해주는 도구. **Claude Code 플러그인 + 이식 가능한 MCP 코어**.

**🌐 [English README](./README.en.md)**

---

## Wikimate가 뭐예요? (한 줄 비유)
브라우저 탭·다운로드 폴더·AI 대화창에 흩어진 자료를, AI가 대신 **"다시 찾고 다시 읽을 수 있는" 내 지식 노트**로 정리해주는 비서예요.

## 지금 상태 (개발 단계)
- **Phase 1a (현재)**: MCP 코어 + 수집 도구(`wikimate_collect`). 자료를 옵시디언 노트(.md)로 생성. **안전 게이트·중복 방지·인젝션 방어** 포함. 서버는 **무의존(zero-dependency)** 이라 받으면 바로 작동.
- 다음: 자동 분류·노션 색인(1b) → 자동 트리거 스킬(2) → 마켓플레이스 공개(3, 검증 후).

## 구조 한눈에
```
옵시디언 = 원본·장기기억 (.md 파일)
노션     = 색인·운영판      (Phase 1b)
MCP 코어 = 정리 로직 1개   (Claude Code·Codex 공용 = 모델 비종속)
안전     = 분석 → 보고 → 사람 승인 → 실행
```

---

## 설치 (Claude Code) — 왕초보 단계별

### 일반 사용자 — GitHub에서 설치 (권장)
Claude Code 대화칸에 입력:
```
/plugin marketplace add sodam-ai/wikimate
/plugin install wikimate@wikimate-marketplace
```
→ Claude Code **재시작** → `/mcp` 입력 → `wikimate_collect` 도구가 보이면 성공.

> ⚠️ **다른 사람이 받으려면 저장소가 "공개(public)"여야 해요.** 현재는 비공개라, **공개 배포는 검증 후(Phase 3)** 입니다. (서버가 무의존이라 별도 `npm install`은 필요 없어요.)

### 개발자·본인 테스트 — 로컬 폴더에서
```
/plugin marketplace add <이 폴더의 경로>
/plugin install wikimate@wikimate-marketplace
```
→ 재시작 → `/mcp` 확인.
> 로컬 경로는 **내 PC에만** 있으니 남에게 공유용이 아니에요(개발/테스트용).

---

## 사용법
대화로 시키면 됩니다. 예시:
```
wikimate_collect 도구로 이 내용을 노트로 만들어줘.
title="MCP란?", text="...본문...", vault_path="<내 볼트 경로>", dry_run=true
```
- **`dry_run=true`(기본)**: 계획만 보고 → 파일 안 만듦. 확인 후 `false`로 실제 생성.
- 같은 자료를 또 넣으면 `source_hash`로 **중복 차단**.
- 만들어지는 노트: 제목·출처·날짜·요약·태그·중요도(머리말) + 본문.

## 환경변수
| 변수 | 설명 |
|---|---|
| `OBSIDIAN_VAULT_PATH` | 내 옵시디언 볼트 폴더의 절대경로. 미설정 시 호출마다 `vault_path` 전달 |

> 노션 관련 변수는 Phase 1b부터. 실제 값은 `.env`에 두고 **절대 커밋 금지**. 예시는 `.env.example` 참고.

## 개발자용 (로컬 검증)
```bash
npm install        # 검증 도구용 의존성 (플러그인 동작 자체엔 불필요)
npm run verify     # 수집 로직 검증
npm start          # MCP 서버(stdio) 실행 — 무의존, node만 있으면 됨
```
> 플러그인의 MCP 서버는 **외부 의존성이 없어요.** `npm install`은 `smoke-server` 검증(공식 SDK 클라이언트)에만 필요합니다.

## 오류 대처 (Troubleshooting)
| 증상 | 원인 | 해결 |
|---|---|---|
| `/mcp`에 도구가 안 보임 | 설치 후 재시작 안 함 | Claude Code 재시작 |
| "vault_path 필요" 오류 | 볼트 경로 미설정 | 호출 시 `vault_path` 넣기 또는 `OBSIDIAN_VAULT_PATH` 설정 |
| GitHub 설치가 안 됨 | 저장소가 비공개 | 저장소 공개 전환(검증 후) 또는 로컬 방식 사용 |

---

## 안전·보안
- 쓰기/삭제는 **사람 승인 게이트**(`dry_run`이 기본값).
- 외부 자료 속 지시문은 **명령이 아니라 데이터**로만 취급 — **프롬프트 인젝션 방어**.
- API 키·토큰·개인정보는 노트·저장소에 저장하지 않음. `.env`는 git 제외.

## 참고 도구 (References)
| 도구 | 역할 |
|---|---|
| [Yakitrak/notesmd-cli](https://github.com/Yakitrak/notesmd-cli) | 옵시디언 CLI (헤드리스) |
| [MarkusPfundstein/mcp-obsidian](https://github.com/MarkusPfundstein/mcp-obsidian) | 옵시디언 MCP |
| [makenotion/notion-mcp-server](https://github.com/makenotion/notion-mcp-server) | 노션 MCP (공식) |
| [Notion `ntn` CLI](https://developers.notion.com/cli/get-started/overview) | 노션 CLI (공식) |

> 영감: 지윤쌤 "AI 작업실 공식 세팅북" (Notion × Obsidian).

## 알려진 한계
- 노션 색인·자동 트리거 스킬은 다음 Phase.
- Codex 어댑터 동작은 미검증.
- 공개 배포(많은 사용자)는 저장소 공개 전환 + 검증 후(Phase 3).

## 라이선스
Apache-2.0 (잠정 — 공개 배포 전 확정).
