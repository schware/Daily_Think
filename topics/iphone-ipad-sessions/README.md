# iPhone·iPad에서 한 Claude Code session 정리

iPhone·iPad 앱에서 시작한 cloud session(origin: `ios`)을 모두 모았다. 기준일은 2026-10-18이다.
- iPhone과 iPad는 기록상 구분되지 않는다. 둘 다 `ios`로 남는다.
- 시각은 KST다.
- 이 repository는 public이라, private repository는 이름과 업무 세부를 빼고 방식과 결과만 적었다 ([ai-java-method README](../ai-java-method/README.md)의 관리 규칙).

## 한눈에

| # | 기간 | session 제목 | repository | 한 일 | 상태 |
|---|---|---|---|---|---|
| 1 | 09-07 | alignment/docs 한글 문서 검토 | alignment (public, 지금 목록에는 reflect-alignment로 보임) | 한글 문서 검토, 영어판 동기화, conflict 해결, PR 개념, CI/CD 개념 | branch에 push. PR은 직접 하기로 함 |
| 2 | 09-08 ~ 09-11 | 모두의 창업 공개 설정 및 문서 시스템 | private A | PARA 문서 구조, 창업 아이템 기획·설계 문서, 신청서 초안 (PR 2건 merge) | branch 1개 PR 미생성. PC로 넘기기로 함 |
| 3 | 09-17 ~ 10-08 | 저장소 연결 확인 | sun-moon-iphone-repository (public) | 연결 확인, 공부 추천, 뉴스 질문, iPhone에서 git 쓰는 법 | 질문·답만 있음. repository는 비어 있음 |
| 4 | 10-12 | 생각 정리 공간 | private A | repository 점검 1회 | 점검 결과만 남음 |
| 5 | 10-12 ~ 10-17 | AI 학습 방법 고민 | Daily_Think | 메모 정리, 주제 1~6, `topics/ai-java-method/` | merge 8번. memo commit 3개는 main에 아직 없음 |
| 6 | 10-18 ~ | iPhone Claude code 정리 | Daily_Think | 이 정리 | 진행 중 |

## 1. alignment/docs 한글 문서 검토 (09-07)
- **repository:** 커리어 결정을 ADR로 기록하는 문서 repository. 한글판(`_kr`)과 영어판이 짝으로 있다.
- **한 일**
  - `_kr` 문서를 고친 대로 영어판을 맞췄다. README의 "기술 저장소와의 관계"는 앞으로 repository가 여러 개가 될 것을 생각해 복수 표현으로 바꿨다.
  - master에 직접 올린 commit과 겹쳐 conflict가 났고, 양쪽을 합쳐 해결했다.
  - "영어 문서가 안 바뀌었다" → 작업이 branch에만 있고 master에 merge되지 않아서였다. PR이 무엇인지 물었다.
  - 배포 자동화와 DevOps를 물었다. CI/CD 개념(CI = push마다 build·test, CD = 배포 자동화)을 들었다.
- **결정**
  - PR과 merge는 직접 한다. git·GitHub가 SVN과 무엇이 다른지는 나중에 묻는다.
  - 질문은 ADR에 넣지 않는다. 질문이 정리되면 그때 넣는다.
  - CI/CD는 실제 source가 있는 "연결된 곳"(PC session)에서 이어 간다.

## 2. 모두의 창업 공개 설정 및 문서 시스템 (09-08 ~ 09-11)
- **repository:** private A (창업 아이템 문서)
- **한 일**
  - PARA 방식 문서 구조(`0-index`, `1-projects`, `2-areas`, `3-resources`)를 만들었다.
  - 창업 아이템 하나를 기획서·설계서로 쓰고, 조사 근거 목록을 붙였다. 하루(09-08)에 commit 25개.
  - 지원 사업 신청서 초안을 쓰고, 외부에서 받은 정리본과 대조했다.
  - PR 2건 merge (09-08, 09-11).
- **막힌 것 (09-11)**
  - 새 repository를 만들려다 GitHub App 권한(`Resource not accessible by integration`)에 막혔다.
  - Claude가 안 되는 이유 설명과 같은 요청을 되풀이했다. "핸드폰이라는 환경 때문인가"라는 질문에 아니라고 단정했지만, 실제로는 기기마다 session 환경과 권한이 다른 것이 핵심이었다.
  - "작은 것은 넘어가자"는 식의 강요를 지적했다. "AI 11일차고 이렇게 넘어가면 원인을 못 찾는다."
  - 이 session을 PC로 가져와 이어 가기로 했다.
- **남은 것:** `docs/ux-dx-framing` branch (UX → data → AI 순서로 문서 틀을 바꾼 것)가 PR 없이 남아 있다 (10-18 확인).

## 3. 저장소 연결 확인 (09-17 ~ 10-08)
- **repository:** sun-moon-iphone-repository. 파일이 없는 빈 repository다.
- **질문과 답**
  - 09-17: 연결 확인. "여기서 같이 진행할 것을 찾겠다."
  - 10-01: 공부할 것 추천 → Swift 기초. 관심 분야(AI, TypeScript, 수학, 물리, 화학)를 알려 주자 다섯 분야를 경사하강법 하나로 묶은 TypeScript 실습을 받았다.
  - 10-02: 은행권 해킹 뉴스가 이어지는 이유 추론. 보도된 사실과 추론을 나눠 정리했다.
  - 10-03: 다른 PC의 code에 접속할 수 있나 → cloud session은 PC에 닿지 못한다. PC에서 `claude remote-control`을 실행해야 한다.
  - 10-08: iPhone에서 git은 어떻게 관리하나 → Claude Code 앱, Working Copy, GitHub 앱, a-Shell·iSH.
- **남은 것:** 공부 기록을 파일로 남기자는 제안만 있었고 만든 것은 없다.

## 4. 생각 정리 공간 (10-12)
- **repository:** private A (2번과 같은 곳)
- **한 일:** "생각 정리하는 공간" 한 줄로 시작한 점검 1회. 마지막 기록(09-10) 뒤 한 달 동안 정리되지 않은 것을 찾았다.
  - 진행 중인 아이템은 1개
  - 정리 필요 4건: 신청서 마감일 미확인, 09-11에 하기로 한 결정이 기록되지 않음, 큰 문서가 `0-index`에 있음, 구조 변경 제안서 미결정
- **남은 것:** 점검 결과에 대한 후속 작업은 없다. 같은 날 Daily_Think를 시작했다 (5번).

## 5. AI 학습 방법 고민 (10-12 ~ 10-17)
- **repository:** Daily_Think
- **한 일:** 메모와 정리는 이미 repository에 있다.
  - [daily 2026-10-12](../../daily/2026-10-12.md): 벽
  - [daily 2026-10-14](../../daily/2026-10-14.md): 주제 1~5
  - [daily 2026-10-15](../../daily/2026-10-15.md): 주제 6, cloud에서 local로
  - [topics/ai-java-method](../ai-java-method/README.md): 사례 1
- **repository에 아직 없는 대화**
  - 10-16 새벽: `~/.claude/CLAUDE.md`를 만드는 script를 iPhone·iPad에 등록할 수 있나 → cloud environment의 **Setup script**에 넣는다. 그 environment로 여는 모든 cloud session에 적용된다. 앱에서 Edit 메뉴를 못 찾으면 PC 브라우저나 Desktop app에서 한다. (지금 이 session에 그 `CLAUDE.md`가 적용돼 있다.)
  - 10-17: iPhone Claude Code는 폴더 정리를 어떻게 하나 → iPhone에는 "내 폴더"가 없다. 폴더는 GitHub repository 안에 있고, 정리는 말로 시킨다. session 정리는 Code 탭의 이름 바꾸기·archive와 Projects.
- **남은 것:** 원격 `daily/2026-10-12` branch의 memo commit 3개가 main에 없다 (이 session에서 working tree에만 반영).

## 6. iPhone Claude code 정리 (10-18 ~)
- 이 session. 메모를 daily로 옮기고, 이 파일을 만들었다.

## Claude: 보이는 것
- **끝까지 닫힌 것은 Daily_Think뿐이다.** 요청이 작았고, 결과가 파일로 남았고, 끝이 merge로 정해져 있었다 (주제 6의 생각과 같은 이야기).
- **PC로 넘어가며 멈춘 것이 둘이다.** 1번은 CI/CD를 PC에서, 2번은 session 전체를 PC로. 넘어간 뒤 이어졌는지는 iPhone 쪽 기록에 없다.
- **같은 질문이 되풀이된다.** "PC와 iPhone이 어떻게 이어지나"가 2번(09-11), 3번(10-03), 5번(10-15)에 나온다. 답은 매번 같다. 이어 주는 것은 git repository이고, session 자체는 따로다.

## 참조자료
- Claude Code 앱: [mobile](https://code.claude.com/docs/en/mobile)
- PC session을 iPhone에서 조종: [remote-control](https://code.claude.com/docs/en/remote-control)
- cloud session과 teleport: [claude-code-on-the-web](https://code.claude.com/docs/en/claude-code-on-the-web)
- Setup script 등 cloud environment 설정: [cloud-environments](https://code.claude.com/docs/en/cloud-environments)
- Projects: [claude-projects](https://code.claude.com/docs/en/claude-projects)
