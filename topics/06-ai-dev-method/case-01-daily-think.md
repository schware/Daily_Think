# 사례 1: Daily_Think를 Claude Code로 운영한 흐름

- 기간: 2026-10-12 ~ 10-15
- repository: `schware/Daily_Think` (public)
- 환경: iPhone의 Claude 앱 → Claude Code cloud session (permission mode: auto)
- 무엇을 했나: 출퇴근 중 iPhone 메모를 Claude Code가 기록·정리하고, 질문에 답한 주제 1~6 노트를 만들었다.

## 한 줄 요약
iPhone 메모 → Claude가 초안 → 새 context의 검증 agent가 확인 → branch에 push → 사람이 "pr and merge go" → merge → 사람이 읽고 다음 메모로 피드백.
7원칙이 대부분 실제로 돌았다. 다만 승인은 chat 한 줄이고, 피드백으로 생긴 규칙과 검증 도구는 아직 repository 밖(대화와 session 안)에 있다.

## 숫자로 보면 (PR #8 merge 시점)

| 항목 | 값 |
|---|---|
| main의 commit | 62개 (Claude 41, 사람 21) |
| PR | 8개 (모두 사람의 요청 뒤 merge) |
| workflow | 19번, agent 168개 |
| 실패한 agent | 34개 (session 사용 한도) |
| 노트 | 주제 1~6과 추가 질문들 |

- 사람의 commit 21개: PR merge 8개, `CLAUDE.md` 작성·수정 3개, README 1개, `references/` 폴더와 PDF 올리기 8개, Initial commit 1개
- workflow 19번 중 2번은 조사(PDF 읽기, 공식 문서 조사)였고, 나머지 17번은 검증이나 검증을 겸한 재작성이었다.

## 전체 흐름 (실제로 돈 순서)

```
1. 메모 (사람, iPhone)
   → index/memo.md에 원문 그대로 commit
2. 초안 (Claude)
   → daily/YYYY-MM-DD.md, "(draft)" commit
3. 검증 (새 context의 agent들)
   → fact-check, clarity, refute
4. 수정 → branch에 push
   → "Revise ... after fact-check" commit
5. 승인 (사람): "pr and merge go"
   → PR 생성 → merge → branch를 main에 맞춤
6. 운영 (사람이 iPhone으로 읽는다)
   → 고칠 점을 다음 메모로 → 1로
```

## 단계별로 무엇이 있었나

**1. 메모 (사람)**
- iPhone에서 쓴 글을 Claude가 `index/memo.md`에 원문 그대로, 날짜 heading 아래 commit했다. 분류하지 않는다 (`CLAUDE.md` 규칙).
- 무엇을 물을지, 다음 주제가 무엇인지는 늘 사람이 정했다. 예: "주제 6은 제가 천천히 생각해 볼께요."

**2. 초안 (Claude)**
- 공식 문서와 원문 PDF를 조사해 `daily/YYYY-MM-DD.md`에 초안을 쓰고 "(draft)"로 commit했다.
- 문서만 믿지 않고 직접 실험한 것도 있다. 예를 들어 worktree 동작은 scratch repository에서 git과 `claude` CLI로 확인했다.

**3. 검증 (새 context의 agent)**
- 초안을 쓴 Claude가 아니라, 새 context에서 시작한 agent들이 공식 문서와 원문에 대조했다.
- 처음에는 fact-check와 clarity를 봤다. 4-1~4-3부터는 찾은 문제마다 "정말 문제인지" 반박하는 refute agent를 더 붙였다.

**4. 수정과 push (Claude)**
- 검증 결과를 반영해 "Revise ... after fact-check" 같은 commit을 따로 남겼다. 초안 commit과 수정 commit이 짝을 이룬다.
- push는 branch(`daily/2026-10-12`)에만 했다. 첫날 "올려 달라고 할 때 올려 달라"는 요청을 받은 뒤로 main에는 직접 push하지 않았다.

**5. 승인과 merge (사람의 요청 → Claude)**
- 사람이 "pr and merge go"라고 하면 PR을 만들고 merge했다.
- merge할 때 `expectedHeadSha`로 "확인한 그 commit"만 merge되게 했다.
- merge 뒤에는 branch를 main에 맞춰 다음 작업을 이어 갔다.

**6. 운영과 피드백 (사람)**
- 여기서 "운영"은 사람이 iPhone으로 노트를 읽는 것이다.
- 피드백이 문서를 바꿨다.
  - "머리·작업대 같은 표현은 어렵다" → 비유를 빼고 용어로 설명
  - "영어 단어는 영어 그대로" → 주제 1~6 전체의 기술 용어를 영어로 바꿈 (PR #7)
  - "PC(local)에서 할 때도" → 4-3에 PC(local) section 추가 (PR #8)

## 7원칙에 대어 보기

| 원칙 | 실제로 한 것 | 부족한 것 |
|---|---|---|
| 1. 사람이 Why·What | 메모, `CLAUDE.md`, 폴더 구조, 참고 PDF는 사람이 정했다 | 요청마다 "완료 조건"이 따로 없다 |
| 2. Claude가 How | 조사, 초안, git·CLI 실험 | — |
| 3. 명세가 기준 | `CLAUDE.md`가 사실상의 명세다 | 피드백으로 생긴 규칙이 `CLAUDE.md`에 없다 |
| 4. 생성·검증 분리 | 초안과 검증을 다른 agent가 했다 | 검증 agent를 작성자가 불렀다. CI가 없다 |
| 5. 자율성 ∝ 위험 | branch push는 자유, merge는 요청 뒤에만 | 승인이 chat 한 줄이다. branch protection이 없다 |
| 6. 지식은 repository에 | 메모·노트·PDF·`CLAUDE.md`가 repository에 있다 | 검증 script와 용어 규칙은 밖에 있다 |
| 7. 운영 결과가 피드백 | 읽은 뒤의 피드백이 문서와 규칙을 바꿨다 | 피드백을 세는 지표가 없다 |

**표를 풀어 쓰면**
- **1번:** `CLAUDE.md`는 사람이 직접 쓰고 세 번 고쳤다. 참고 PDF도 사람이 올렸다.
- **3번에서 실제로 있었던 일:** 처음 `CLAUDE.md`는 메모 위치를 목록에서는 `index/memo.md`, 폴더 그림에서는 `inbox/`로 다르게 적고 있었다. Claude가 `inbox/`로 맞춰 branch에서 `CLAUDE.md`까지 고쳤지만, 사람이 main의 `CLAUDE.md`에서 `index/`로 정했다. Claude는 그에 맞춰 되돌렸다 (commit `7780d87`). 명세의 주인이 사람이라는 것이 실제로 확인된 일이다.
- **4번의 한계:** 검증 agent를 초안을 쓴 Claude가 직접 불렀다. 그래서 위임 prompt에 작성자의 시각이 섞일 수 있다. daily 노트 4-3의 기준으로는 "반쯤 독립"이다. 마지막 관문은 사람의 확인이었다.
- **5번의 한계:** 승인은 chat의 "pr and merge go" 한 줄이다. GitHub 기록에는 merge한 사람이 사용자 계정(Sun&Moon)으로 남지만, 실제로 merge를 실행한 것은 그 요청을 받은 Claude다.
- **6번의 효과:** 대화가 길어져 중간에 요약(compaction)됐다. 그래도 메모, 노트, commit이 repository에 있어서 그대로 이어 갔다.

## 검증이 실제로 잡은 것 (예)
- **주제 1~4:**
  - "Node.js는 server가 아니다"를 "Tomcat은 켜면 기다리고, Node는 `listen`을 불러야 기다린다"로 바로잡았다.
  - React·Vue가 Node 위에서 돈다는 오해를 바로잡았다.
  - CDN이 Nginx보다 먼저였다.
  - Apache event MPM은 2005년에 실험, 2012년에 안정이었다.
- **주제 5:** 보고서 page 번호 3곳이 틀렸다. S&P의 -5는 실측이고 -13·+3은 2026년 전망이다. 비율에 분모가 빠져 있었다.
- **주제 6:**
  - `--settings` 파일 안의 `/specs/**`는 그 파일이 있는 `.claude/` 기준으로 풀려서 `**/specs/**`로 고쳤다.
  - `REVIEW.md`는 managed Code Review만 읽는다.
  - PostToolUse hook의 피드백은 `exit 2`일 때만 Claude에게 간다.
- **4-1~4-3:**
  - `claude --worktree`로 시작하면 `$CLAUDE_PROJECT_DIR`가 worktree를 가리킨다. 처음에 쓴 설명이 틀렸다.
  - git으로 만든 worktree에서 그냥 `claude`를 실행하면 isolation check가 없다.
  - cloud session은 tag를 push하지 못한다.
- **용어 정리:** "Google cloud"를 Google Cloud로 고쳤다. 사람 이름 E. F. Codd가 "코드"로 잘못 옮겨져 있었다.
- **PC(local):** `/clear`와 resume은 `--settings` lock을 되살리지 않는다.

## 운영 중에 실제로 겪은 것
- **Stop hook이 실제로 막았다.** 끝내려는데 commit하지 않은 변경이 있자, 이 cloud 환경의 Stop hook(`~/.claude/stop-hook-git-check.sh`)이 막고 "commit하고 push하라"고 돌려보냈다. daily 노트 4번·5번에서 말한 "hook으로 강제하기"가 실제로 돈 예다.
- **session 사용 한도:** 검증 agent 34개가 한도에 걸려 실패했다. refute 단계를 다 못 돌린 PR #7은 그 사실을 PR 설명에 적고, 남은 항목은 Claude가 직접 판단했다.
- **Claude가 먼저 정한 구조를 되돌린 일:** 처음 메모를 `2026/2026-10-12.md`처럼 임의의 위치에 만들었다가 `CLAUDE.md`의 구조에 맞춰 지웠다.

## 다음에 바꿀 것 (이 repository에 바로 적용할 수 있는 것)
1. **피드백 규칙을 `CLAUDE.md`에 적는다 (사람이 결정).**
   - 기술 용어는 영어 그대로 쓴다. 비유보다 용어로 설명한다.
   - 주제별 폴더는 `topics/`에 둔다.
   - 지금은 이 규칙들이 대화에만 있다 (원칙 3, 6).
2. **검증을 repository에 둔다.**
   - 예: 읽기 도구만 가진 fact-check agent를 `.claude/agents/fact-checker.md`로 commit한다.
   - 그러면 다른 session이나 PC에서도 같은 검증을 쓴다 (원칙 4, 6).
3. **승인을 GitHub에 남긴다.**
   - main에 branch protection을 걸어 PR 없이는 바뀌지 않게 한다.
   - merge 버튼은 사람이 GitHub 앱에서 직접 누른다 (원칙 5).
4. **요청마다 완료 조건을 한 줄 둔다.**
   - 예: "참조자료는 끝에", "code block은 45칸 안", "기술 용어는 영어".
   - PR 설명에 checklist로 붙인다 (원칙 1, 3).
5. **피드백을 센다.**
   - PR마다 검증이 잡은 오류 수와 사람이 고치라고 한 횟수를 이 파일에 적는다.
   - 줄어드는지 본다 (원칙 7).

## 근거 (commit과 PR)

| PR | 내용 | 검증 뒤 수정 commit |
|---|---|---|
| #1 | 주제 1~2 (web 구조, C/S 역사) | 여러 개 ("Revise ... after review/fact-check") |
| #2 | 주제 3 (BO 기술 조합) | `4079c05` |
| #3 | 주제 4 (연대별 표) | `6e131b6` |
| #4 | 주제 5 (2026 AI 보고서) | `8b6977a` |
| #5 | 주제 6 (방법론) | `f9fbcbe` |
| #6 | 주제 6 4-1~4-3 | `9127a4a`, `488433e` |
| #7 | 기술 용어 영어로 | `03b5eb6` |
| #8 | 주제 6 PC(local) | `8f38e2c` |

## 참조자료
- 주제 6 원문: `daily/2026-10-15.md`
- 메모 원문: `index/memo.md`
- PR 목록: [github.com/schware/Daily_Think/pulls](https://github.com/schware/Daily_Think/pulls?q=is%3Apr)
- Claude Code hook (Stop hook): [hooks](https://code.claude.com/docs/en/hooks)
- permission mode (auto): [permission-modes](https://code.claude.com/docs/en/permission-modes)
- cloud session: [claude-code-on-the-web](https://code.claude.com/docs/en/claude-code-on-the-web)
- subagent (`.claude/agents/`): [sub-agents](https://code.claude.com/docs/en/sub-agents)
- GitHub branch protection: [About protected branches](https://docs.github.com/en/repositories/configuring-branches-and-merges-in-your-repository/managing-protected-branches/about-protected-branches)
