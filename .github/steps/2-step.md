## 2단계: AI 워크플로우 구성하기

잘하셨습니다! 첫 번째 AI 워크플로우가 이제 작동합니다. 다음으로 `ai-inference` 액션을 다른 액션과 결합하여 프로젝트에 의미 있는 AI 워크플로우를 만드는 방법을 알아봅시다.

### 📖 이론: AI 워크플로우 구성

AI는 세 가지 순차적 프로세스를 연결하여 지능적인 자동화를 만들 때 Actions에서 가장 큰 가치를 더합니다:

```mermaid
graph LR
    A[🔍 컨텍스트 수집] --> B[🤖 AI 처리]
    B --> C[🚀 결과 적용]

    style A fill:#4fc3f7,stroke:#333,stroke-width:2px,color:#000
    style B fill:#ffb74d,stroke:#333,stroke-width:2px,color:#000
    style C fill:#ba68c8,stroke:#333,stroke-width:2px,color:#000
```

이 워크플로우 패턴의 작동 방식:

1. **🔍 컨텍스트 수집**: GitHub Actions를 사용하여 [이벤트 컨텍스트](https://docs.github.com/en/actions/learn-github-actions/contexts#github-context), 파일 내용, API 결과 또는 이전 워크플로우 단계의 출력에서 데이터를 수집합니다
1. **🤖 AI 처리**: 수집된 컨텍스트를 AI의 역할과 예상 출력 형식을 정의하는 집중된 [시스템 프롬프트](https://github.com/actions/ai-inference#system-prompts)와 함께 `actions/ai-inference`에 공급합니다
1. **🚀 결과 적용**: AI의 응답을 댓글 게시, 파일 업데이트 또는 릴리스 노트 생성과 같은 의미 있는 변경을 만드는 다른 액션의 입력으로 사용합니다

이 3단계 패턴은 전통적인 로직으로 스크립트하기 어려운 판단이 많이 필요한 작업을 자동화하면서 워크플로우를 유지보수하기 쉽게 유지합니다.

### ⌨️ 활동: 자동화된 AI 이슈 분석 워크플로우 만들기

새로 생성된 GitHub 이슈를 자동으로 분석하여 누락된 정보에 대한 즉각적인 피드백을 제공하고, 개선 사항을 제안하며, 명확한 질문을 하는 워크플로우를 만들어 봅시다.

3단계 패턴을 따릅니다: 이슈 이벤트에서 컨텍스트 수집, AI로 처리, 결과를 댓글로 게시.

1. 다음 이름의 새 워크플로우 파일을 만듭니다:

   ```text
   issue-completeness.yml
   ```

1. 워크플로우 메타데이터와 권한을 추가합니다

   ```yaml
   name: Issue Completeness

   on:
     issues:
       types: [opened]

   permissions:
     models: read
     issues: write
   ```

   이 워크플로우는 새 이슈가 열릴 때마다 GitHub Models에 접근하고 새 이슈 댓글을 작성할 수 있는 권한으로 실행됩니다.

   > ❗ **주의:** 내용을 그대로 복사하세요. 이 정확한 워크플로우 이름(`Issue Completeness`)이 이 실습의 다음 단계로 진행하는 데 필요합니다.

1. 이제 AI 추론 액션을 사용하는 작업을 만듭니다.

   이 시나리오에서는 이슈 내용을 분석하여 지능적인 피드백과 권장 사항을 제공하려고 합니다:

   GitHub Actions는 이슈가 열릴 때마다 `github.event` 객체를 통해 [풍부한 컨텍스트](https://docs.github.com/en/webhooks/webhook-events-and-payloads?actionType=opened#issues)를 자동으로 제공합니다. 여기에는 이슈 제목, 본문 내용 및 작성자 정보가 포함됩니다 — `ai-inference`가 지능적인 분석을 제공하는 데 필요한 바로 그 데이터입니다.

   ```yaml
   jobs:
     analyze:
       name: AI Issue Completeness
       runs-on: ubuntu-latest
       steps:
         - name: Analyze issue with AI
           id: ai-response
           uses: actions/ai-inference@v2
           with:
             token: {% raw %}${{ secrets.GITHUB_TOKEN }}{% endraw %}
             max-tokens: 1000
             system-prompt: |
               You are a GitHub issue assistant. Your task is to analyze newly opened issues for completeness.

               Provide concise, helpful suggestions, ask clarifying questions and identify any missing information that would help resolve the issue faster.

               Always respond with ready-to-use markdown content (no code blocks) that can be posted directly as an issue comment.

               Sign off as AI assistant.
             prompt: |
               New issue was opened by {% raw %}${{ github.event.issue.user.login }}{% endraw %}
               Title: {% raw %}${{ github.event.issue.title }}{% endraw %}
               Body:
               ---
               {% raw %}${{ github.event.issue.body }}{% endraw %}
               ---
   ```

   > 🪧 **참고:** **`max-tokens`** 매개변수는 응답의 최대 길이를 제어하는 데 사용됩니다. 값이 너무 낮으면 응답이 중간에 잘릴 수 있습니다.

1. 이제 `ai-inference`의 `response` 출력을 사용하여 이슈 댓글을 게시하는 단계를 추가합니다:

   ```yaml
   - name: Comment results on the issue
     uses: peter-evans/create-or-update-comment@v4
     with:
       token: {% raw %}${{ secrets.GITHUB_TOKEN }}{% endraw %}
       issue-number: {% raw %}${{ github.event.issue.number }}{% endraw %}
       body: {% raw %}${{ steps.ai-response.outputs.response }}{% endraw %}

   ```

   > ⚠️ **중요:** 이 단계가 이전 단계와 동일한 수준으로 올바르게 들여쓰기되어 있는지 확인하세요. 두 단계 모두 `steps:` 섹션 아래에서 동일한 들여쓰기 수준이어야 합니다.

1. 완료입니다! 파일을 `main` 브랜치에 직접 커밋한 다음 **Actions** 탭을 열어 워크플로우가 나타나는지 확인합니다.

<details>
<summary>문제가 있으신가요? 🤷</summary><br/>

- 액션이 **Actions** 탭에 나타나지 않으면 `.github/workflows/` 디렉토리에 `.yml` 확장자로 넣었는지 확인합니다

</details>

### ⌨️ 활동: 워크플로우 테스트하기

1. Issues 탭으로 이동하여 **New issue**를 클릭합니다.
1. 원하는 제목과 본문으로 이슈를 만들거나, 다음 예시를 사용합니다:

   **제목 예시:**

   ```text
   Login form throwing 500 errors on mobile
   ```

   **본문 예시:**

   ```markdown
   Getting 500 errors when trying to log in on my phone. It works sometimes but not always.
   ```

1. 이슈를 만들면 **Actions** 탭에서 워크플로우가 실행되는 것을 확인합니다.
1. 워크플로우가 완료되면, 이슈에 AI 이슈 분석이 포함된 새 댓글이 표시됩니다.

<details>
<summary>문제가 있으신가요? 🤷</summary><br/>

- 워크플로우가 실행되지 않았다면 다음을 확인합니다:
  - 워크플로우가 `.github/workflows` 디렉토리에 있는지
  - 트리거가 `issues: [opened]`이고 기존 이슈를 편집한 것이 아닌 새 이슈를 만들었는지
- 워크플로우 파싱에 실패했다면, YAML 들여쓰기가 올바르고 모든 필수 필드가 있는지 확인합니다.
- 볼완전하거나 잘못된 형식 등 어떤 이유로든 워크플로우가 실패했다면, 수정한 후 다른 이슈를 열어 워크플로우를 다시 트리거해야 합니다.

</details>
