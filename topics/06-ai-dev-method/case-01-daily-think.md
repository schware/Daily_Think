# 사례 1: Daily_Think를 Claude Code로 운영한 흐름

- 기간: 2026-10-12 ~ 10-15
- repository: `schware/Daily_Think` (public)
- 환경: iPhone의 Claude 앱 → Claude Code cloud session (permission mode: 첫 메시지만 default, 그 뒤로 auto)
- 무엇을 했나: 출퇴근 중 iPhone 메모를 Claude Code가 기록·정리하고, 질문에 답한 주제 1~6 노트를 만들었다.
- 왜 이 사례인가: 다른 session에서 한 Claude Code 작업도 주제 6에 맞지만, private repository의 작업이라 여기에는 넣지 않았다. 넣는다면 README의 관리 규칙대로 세부를 빼고, 넣을지는 사람이 정한다. 이 repository는 메모부터 merge까지 전 과정이 commit과 PR로 남아 있다.
- 한계: code가 아니라 문서를 만든 작업이라 test, CI, 배포 단계는 없다.

## 한 줄 요약
iPhone 메모 → Claude가 초안 → 새 context의 검증 agent가 확인 → branch에 push → 사람이 PR·merge 요청 → merge → 사람이 읽고 다음 메모로 피드백.
7원칙이 대부분 실제로 돌았다. 다만 승인은 chat 한 줄이고, merge 전의 마지막 관문은 사실상 검증 agent였다. 피드백으로 생긴 규칙과 검증 도구는 아직 repository 밖(대화와 session 안)에 있다.

## 숫자로 보면 (PR #8 merge 시점)

| 항목 | 값 |
|---|---|
| main의 commit | 62개 |
| 그중 Claude 이름 | 41개 |
| 그중 사용자 계정 이름 | 21개 (PR merge 8개 포함) |
| PR | 8개 (모두 사람의 요청 뒤 merge) |
| workflow | 19번, agent 168개 |
| 실패한 agent | 34개 (session 사용 한도) |

- 사용자 계정 이름의 commit 21개
  - PR merge 8개: 사람의 요청을 받아 Claude가 실행했다.
  - 사람이 GitHub에서 직접 한 commit 13개: `CLAUDE.md` 작성 1개·수정 2개, README 1개, `references/` 폴더와 PDF 올리기 8개, Initial commit 1개
- workflow 19번 중 2번은 조사(PDF 읽기, 공식 문서 조사)였고, 나머지 17번은 검증이나 검증을 겸한 재작성이었다.

## 전체 흐름 (실제로 진행된 순서)

```
1. 메모 (사람, iPhone)
   → index/memo.md에 원문 그대로 commit
2. 초안 (Claude)
   → daily/YYYY-MM-DD.md, "(draft)" commit
3. 검증 (새 context의 agent들)
   → accuracy, clarity, gaps, refute
4. 수정 → branch에 push
   → "Revise ... after fact-check" commit
5. 승인 (사람): PR·merge 요청
   → PR 생성 → merge → branch를 main에 맞춤
6. 운영 (사람이 iPhone으로 읽는다)
   → 고칠 점을 다음 메모로 → 1로
```

실제로는 이 순서가 두 번 어긋났다. Stop hook 때문에 검증 전에 초안이 push된 때가 있었고 (아래 "운영 중에 겪은 것"), PR #6에서는 승인이 검증보다 먼저 왔다 (5단계).

## 단계별로 무엇이 있었나

**1. 메모 (사람)**
- iPhone에서 쓴 글을 Claude가 `index/memo.md`에 원문 그대로, 날짜 heading 아래 commit했다. 분류하지 않는다 (`CLAUDE.md` 규칙).
- 무엇을 물을지, 다음 주제가 무엇인지는 늘 사람이 정했다.
  - 예: Claude가 주제 6 후보 세 개를 내고 하나를 추천했지만, 사람은 "6에 대한 주제는 제가 천천히 생각해 볼께요."라고 하고 나중에 직접 7원칙을 보냈다.

**2. 초안 (Claude)**
- 공식 문서와 원문 PDF를 조사해 `daily/YYYY-MM-DD.md`에 초안을 쓰고 "(draft)"로 commit했다.
- 문서만 믿지 않고 직접 실험한 것도 있다. 예를 들어 worktree 동작은 scratch repository에서 git과 `claude` CLI로 확인했다.

**3. 검증 (새 context의 agent)**
- 초안을 쓴 Claude가 아니라, 새 context에서 시작한 agent들이 공식 문서와 원문에 대조했다.
- 처음에는 accuracy(fact-check)·clarity·gaps(빠진 것) 세 관점으로 봤다. 4-1~4-3부터는 찾은 문제마다 "정말 문제인지" 반박하는 refute agent를 더 붙였다.

**4. 수정과 push (Claude)**
- 검증 결과를 반영해 "Revise ... after fact-check" 같은 commit을 따로 남겼다. 초안 commit과 수정 commit이 짝을 이룬다.
- push는 처음부터 branch(`daily/2026-10-12`)에만 했고, main에 직접 push한 적은 없다.
  - 첫날 Claude가 main에 바로 올릴지 묻자, 사람이 "제가 올려 달라고 할께요 그때 올려주세요"라고 답했다. 그 뒤로 main은 사람이 요청한 PR merge로만 바뀌었다.
- 다만 branch에는 사람에게 묻지 않고 force push를 5번 했다.
  - 2번은 사람이 main의 `CLAUDE.md`를 만들거나 고친 뒤, branch를 main 위에 다시 만들 때였다 (`--force-with-lease`). 아직 merge되지 않은 commit(`2026/` 메모, `inbox/` 구조)을 내용을 옮긴 새 commit으로 바꿨다.
  - 3번은 PR #6~#8 merge 직후 branch를 main에 맞출 때였다 (`-f`). 이때는 잃은 commit이 없어서 force가 필요 없었다.

**5. 승인과 merge (사람의 요청 → Claude)**
- 사람이 요청하면 Claude가 PR을 만들고 merge했다.
- 요청하는 방식은 바뀌었다.
  - PR #1·#2는 둘을 나눠 요청했다. PR #1은 "pr 해 주시면 문서 확인 해 볼께요" 뒤에 "merge 및 pr 부탁해요", PR #2는 "PR 부탁해요" 뒤에 "merge 해주세여"였다.
  - PR #3은 한 메시지로 둘 다 요청했고, PR #4부터는 "pr and merge go" 한 줄이 됐다.
  - 처음에는 PR과 merge 사이에 사람이 볼 틈이 있었는데, 뒤로 갈수록 한 줄로 합쳐졌다.
- PR #6은 승인이 검증보다 먼저 왔다. 초안을 push하고 검증 agent가 아직 돌 때 "pr and merge go"를 받았다. Claude는 검증 결과를 반영하고(`9127a4a`), refute까지 포함한 마지막 검증 결과를 반영한 뒤(`488433e`) PR을 만들고 merge했다.
- merge할 때 `expectedHeadSha`를 붙였다. Claude가 검증을 반영해 push한 그 commit이 PR head일 때만 merge되고, 그 사이 branch가 바뀌면 merge가 실패한다. 사람이 그 commit을 미리 본 것은 아니다 (PR #6).
- merge 뒤에는 branch를 main에 맞춰 다음 작업을 이어 갔다.

**6. 운영과 피드백 (사람)**
- 여기서 "운영"은 사람이 iPhone으로 노트를 읽는 것이다.
- 피드백이 문서를 바꿨다.
  - "서버는 아니다 인가여?" → 첫 검증이 제안해 수정 commit(`05650d7`)에 들어간 문장 "Node.js 자체는 WAS 같은 서버가 아니다"가 헷갈린다는 것을 사람이 읽고 잡았다. 검증이 넣은 오해를 사람의 질문이 먼저 드러낸 예다.
  - "머리·작업대 같은 표현은 어렵다" → 비유를 빼고 용어로 설명 (PR #7)
  - "영어 단어는 영어 그대로" → 주제 1~6 전체의 기술 용어를 영어로 바꿈 (PR #7)
  - "PC(local)에서 할 때도" → 4-3에 PC(local) section 추가 (PR #8)

## 7원칙과 맞춰 보기

| 원칙 | 한 것 | 부족한 것 |
|---|---|---|
| 1. Why·What | 메모, `CLAUDE.md`, PDF | 완료 조건 없음 |
| 2. How | 조사, 초안, 실험 | — |
| 3. 명세 | `CLAUDE.md` | 새 규칙이 밖에 |
| 4. 생성·검증 | 다른 agent가 검증 | 작성자가 호출, CI 없음 |
| 5. 자율성 | push 자유, merge는 요청 뒤 | chat 승인 |
| 6. 저장소 | 메모·노트·PDF | script·용어 규칙은 밖 |
| 7. 피드백 | 피드백이 문서를 바꿈 | 지표 없음 |

**표를 풀어 쓰면**
- **1번:** `CLAUDE.md`는 사람이 직접 만들고 두 번 고쳤다. 참고 PDF도 사람이 올렸다.
- **3번에서 실제로 있었던 일**
  - 처음 `CLAUDE.md`는 메모 위치를 목록에서는 `index/memo.md`, 폴더 그림에서는 `inbox/`로 다르게 적고 있었다. Claude가 `inbox/`로 맞춰 branch에서 `CLAUDE.md`까지 고쳤지만, 사람이 main의 `CLAUDE.md`에서 `index/`로 정했다. Claude는 그에 맞춰 되돌렸다 (commit `7780d87`). 명세의 주인이 사람이라는 것이 실제로 확인된 일이다.
  - 10/14에는 사람이 `CLAUDE.md`에 "참조자료가 있으면 마지막에 정리" 한 줄을 직접 더했다 (commit `5fdda8b`). Claude는 바로 다음 PR #3의 주제 4부터 끝에 참조자료 section을 두었고, 주제 1~3에도 붙였다 (commit `6e131b6`). 명세를 고치자 Claude의 행동이 바뀐 예다.
  - 반대로 그 뒤 대화로 받은 피드백(영어 용어, 비유 대신 용어)은 아직 `CLAUDE.md`에 없다.
- **4번의 한계**
  - 검증 agent를 초안을 쓴 Claude가 직접 불렀다. 그래서 위임 prompt에 작성자의 시각이 섞일 수 있다. daily 노트 4-3의 기준으로는 "반쯤 독립"이다.
  - merge 전의 마지막 관문은 사람의 PR·merge 요청이었지만, 내용을 확인했다기보다 merge를 허락한 것에 가까웠다. 요청은 대개 Claude의 보고 뒤 몇 분 안에 왔다. PR #6에서는 검증이 끝나기 전에 와서, 그 뒤의 수정 commit 두 개(`9127a4a`, `488433e`)는 사람이 다시 보지 않은 채 merge됐다.
  - 사람이 노트를 읽고 고칠 점을 말한 것(비유, 영어 용어, PC(local))은 모두 merge 뒤였다 (6단계).
- **5번의 한계**
  - 승인은 chat 메시지 한 줄이다.
  - GitHub 기록에는 PR을 연 사람도 merge한 사람도 사용자 계정(`schware`)으로 남는다. 실제로 PR을 만들고 merge를 실행한 것은 그 요청을 받은 Claude다 (사용자 계정 권한으로 도는 GitHub MCP tool).
- **6번의 효과:** 대화가 길어져 중간에 요약(compaction)됐다. 그래도 메모, 노트, commit이 repository에 있어서 그대로 이어 갔다.

## 검증이 실제로 잡은 것 (예)
- **주제 1~4**
  - Node.js 절의 첫 검증이 제안한 "Node.js 자체는 server가 아니다"를 그대로 넣었더니, 사람이 "음 ... 서버는 아니다 인가여?"라고 되물었다. 다음 검증이 설명을 바로잡았다: Tomcat은 켜기만 해도 요청을 기다리지만, Node.js는 내 코드가 `listen`을 불러야 기다린다 (`fbc6381`). 검증이 넣은 문장의 문제를 사람의 질문이 먼저 드러낸 예다 (원칙 7).
  - React·Vue가 Node 위에서 돈다는 오해를 바로잡았다.
  - CDN이 Nginx보다 먼저였다.
  - Apache event MPM은 2005년에 실험, 2012년에 안정이었다.
- **주제 5:** 숫자 6개의 보고서 page 번호가 틀렸다 (8곳을 고쳤다. 예: 일자리 노출 60%는 p. 13이 아니라 p. 85). S&P의 -5는 실측이고 -13·+3은 2026년 전망이다. 비율에 분모가 빠져 있었다.
- **주제 6**
  - `--settings` 파일 안의 `/specs/**`는 그 파일이 있는 `.claude/` 기준으로 풀려서 `**/specs/**`로 고쳤다.
  - `REVIEW.md`는 managed Code Review만 읽는다.
  - PostToolUse hook이 `exit 0`으로 끝나면 stderr는 Claude에게 가지 않는다. 실패를 알리려면 `exit 2`로 끝내거나, `exit 0`으로 끝내면서 JSON으로 `decision: "block"`과 `reason`을 출력한다.
- **4-1~4-3**
  - `claude --worktree`로 시작하면 `$CLAUDE_PROJECT_DIR`가 worktree를 가리킨다. 처음에 쓴 설명이 틀렸다.
  - git으로 만든 worktree에서 그냥 `claude`를 실행하면 isolation check가 없다.
  - cloud session은 tag를 push하지 못한다.
- **용어 정리:** "Google cloud"를 Google Cloud로 고쳤다. 사람 이름 E. F. Codd를 "코드"로 적어 code와 헷갈리던 것을 영어 이름으로 바꿨다.
- **PC(local):** `/clear`는 Claude Code를 다시 실행하지 않아 처음 실행할 때의 option을 그대로 쓰고, `--continue`·`--resume`은 `--settings`를 되살리지 않는다. 그래서 lock이 필요한 session은 `--settings`를 붙여 새로 실행한다.

## 운영 중에 실제로 겪은 것
- **Stop hook이 실제로 막았다 (2번)**
  - 끝내려는데 commit하지 않은 변경이 있자, 이 cloud 환경의 Stop hook(`~/.claude/stop-hook-git-check.sh`)이 `exit 2`로 막고 "commit하고 push하라"고 돌려보냈다.
  - 두 번 모두 검증 workflow를 background로 띄워 두고 turn을 끝내려던 때였다. 그래서 Claude는 검증 결과가 오기 전에 초안을 작업 branch에 commit·push했다 (`7780d87`, `164312e`). 두 번째는 사용자에게 "아직 push하지 않았다"고 말한 직후였다. 위 흐름의 "검증 → push" 순서가 hook 때문에 뒤집힌 것이다.
  - 다만 이 script는 한 번 막혀서 이어서 일하는 중(`stop_hook_active`가 true)이면 검사 없이 `exit 0`으로 통과시킨다. commit될 때까지 끝까지 막는 관문이 아니라 한 번 돌려보내는 장치다. 반드시 지켜야 하는 확인은 CI에 둔다 (daily 노트 4번의 Stop hook 주의와 같은 이야기).
- **session 사용 한도**
  - 검증 agent 34개가 한도에 걸려 실패했다.
    - PR #7: 33개. 영어 용어 변경 검토의 refute agent 31개와, 비유를 뺀 commit(`164312e`)을 확인하는 workflow의 agent 2개 전부다.
    - PR #1: 1개. C/S 역사의 clarity 검토다.
  - refute가 끊긴 것은 PR #7 설명에 적고, 남은 항목은 Claude가 직접 판단했다.
  - 나머지 두 검증은 다시 돌리지 않았고 PR 설명에도 적지 않았다. 그래서 `164312e`는 따로 확인을 받지 못했다. Claude는 사람에게 "확인 중"이라고 말한 뒤 그 결과를 알리지 않았다.
- **Claude가 먼저 정한 구조를 되돌린 일**
  - 10/12 첫 메모 때 repository에는 README만 있었고 `CLAUDE.md`는 아직 없었다. Claude는 메모를 `2026/2026-10-12.md`에 임의로 만들었다.
  - 그 뒤 사람이 `CLAUDE.md`를 올리고 "github의 구조는 올려 놓았어요"라고 했다. Claude는 branch를 main에 다시 맞춰 그 파일을 버렸다 (main에는 들어가지 않았다).
  - 명세가 없을 때 Claude가 구조를 정했다가, 명세가 생기자 따른 예다.

## 다음에 바꿀 것 (이 repository에 바로 적용할 수 있는 것)
1. **피드백 규칙을 `CLAUDE.md`에 적는다 (사람이 결정).**
   - 기술 용어는 영어 그대로 쓴다. 비유보다 용어로 설명한다.
   - 주제별 폴더는 `topics/`에 둔다.
   - 지금은 이 규칙들이 대화에만 있다 (원칙 3, 6).
2. **검증을 repository에 둔다.**
   - 예: 읽기 도구만 가진 fact-check agent를 `.claude/agents/fact-checker.md`로 commit한다.
   - 그러면 다른 session이나 PC에서도 같은 검증을 쓴다 (원칙 4, 6).
3. **merge는 사람이 직접 한다.**
   - main에 branch protection을 걸어 PR 없이는 바뀌지 않게 하고, admin도 막히도록 "Do not allow bypassing the above settings"를 켠다.
   - 다만 Claude는 사용자 계정(`schware`, admin)으로 PR을 열고 merge한다. GitHub 기록도 사람이 한 merge와 똑같다. 그래서 branch protection만으로는 Claude의 merge를 막지 못한다. approve 필수도 쓸 수 없다 (PR 작성자가 같은 계정이라 approve할 사람이 없다).
   - 그래서 merge 버튼은 사람이 GitHub 앱에서 직접 누르고, Claude에게는 PR까지만 맡긴다.
   - Claude 쪽은 `.claude/settings.json`의 deny rule로 `mcp__github__merge_pull_request`를 막는다. `gh api`로도 merge할 수 있어서 완전하지는 않다 (원칙 5).
4. **요청마다 완료 조건을 한 줄 둔다.**
   - 예: "참조자료는 끝에", "code block은 45칸 안", "기술 용어는 영어".
   - PR 설명에 checklist로 붙인다 (원칙 1, 3).
5. **검증이 끝나기 전에는 승인하지 않는다.**
   - Claude는 검증 결과를 보고한 뒤에 PR을 만들고, 사람은 그 보고를 보고 merge한다.
   - Stop hook 때문에 검증 전에 push한 초안은 PR 설명에 "검증 전 push"로 적는다 (원칙 4, 5).
6. **피드백을 센다.**
   - PR마다 검증이 잡은 오류 수와 사람이 고치라고 한 횟수를 이 파일에 적는다.
   - 줄어드는지 본다 (원칙 7).

## 근거 (commit과 PR)

| PR | 내용 | 검증 뒤 수정 commit |
|---|---|---|
| #1 | 주제 1~2 (web 구조, C/S 역사) | 여러 개 ("Revise ...") |
| #2 | 주제 3 (BO 기술 조합) | `4079c05` |
| #3 | 주제 4 (연대별 표) | `6e131b6` |
| #4 | 주제 5 (2026 AI 보고서) | `8b6977a` |
| #5 | 주제 6 (방법론) | `f9fbcbe` |
| #6 | 주제 6 4-1~4-3 | `9127a4a`, `488433e` |
| #7 | 비유 빼기, 기술 용어 영어로 | `03b5eb6` |
| #8 | 주제 6 PC(local) | `8f38e2c` |

## 참조자료
- 주제 6 원문: [`daily/2026-10-15.md`](../../daily/2026-10-15.md)
- 메모 원문: [`index/memo.md`](../../index/memo.md)
- PR 목록: [github.com/schware/Daily_Think/pulls](https://github.com/schware/Daily_Think/pulls?q=is%3Apr)
- Claude Code hook (Stop hook, `stop_hook_active`): [hooks](https://code.claude.com/docs/en/hooks)
- permission mode (auto): [permission-modes](https://code.claude.com/docs/en/permission-modes)
- permission rule (MCP tool deny): [permissions](https://code.claude.com/docs/en/permissions)
- cloud session: [claude-code-on-the-web](https://code.claude.com/docs/en/claude-code-on-the-web)
- subagent (`.claude/agents/`): [sub-agents](https://code.claude.com/docs/en/sub-agents)
- GitHub branch protection: [About protected branches](https://docs.github.com/en/repositories/configuring-branches-and-merges-in-your-repository/managing-protected-branches/about-protected-branches)
