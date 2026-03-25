## 1단계: AI 액션 소개

이 실습에서는 GitHub Models를 사용하여 AI 기능을 GitHub Actions 워크플로우에 직접 통합하는 방법을 배웁니다. 먼저 핵심 개념을 이해한 다음 첫 번째 AI 기반 워크플로우를 만들어 봅시다!

### 📖 이론: Actions에서의 GitHub Models

#### 🤖 GitHub Models란?

**[GitHub Models](https://docs.github.com/github-models)**는 주요 제공업체의 AI 모델을 큐레이션한 카탈로그를 제공하는 서비스입니다. 다양한 사용 사례 중에서, GitHub Models는 `https://models.github.ai/inference`에서 사용 가능한 추론 API를 포함하고 있어 개발자가 AI 기능을 GitHub 워크플로우와 애플리케이션에 직접 통합할 수 있습니다.

#### ⚙️ GitHub Actions와 GitHub Models의 작동 방식

GitHub Actions와 GitHub Models 간의 [통합](https://docs.github.com/en/github-models/use-github-models/integrating-ai-models-into-your-development-workflow#using-ai-models-with-github-actions)은 원활하게 설계되어 있습니다:

- 🔑 **내장 인증**: GitHub Actions의 내장 [`GITHUB_TOKEN`](https://docs.github.com/en/actions/tutorials/authenticate-with-github_token#modifying-the-permissions-for-the-github_token)을 사용하여 GitHub Models 서비스에 대한 호출을 인증할 수 있어, 타사 제공업체와의 추가 API 키나 복잡한 인증 설정이 필요 없습니다.
- 🔐 **간단한 권한**: [`models: read`](https://docs.github.com/en/actions/tutorials/authenticate-with-github_token#modifying-the-permissions-for-the-github_token) 권한은 `GITHUB_TOKEN`에 AI 요청을 위한 GitHub Models 추론 API 접근 권한을 부여합니다.
- 🎯 **쉬운 통합**: 공식 [actions/ai-inference](https://github.com/actions/ai-inference) 액션은 GitHub Actions에서 GitHub Models를 사용하는 매우 간단한 경로를 제공합니다.

> [!TIP]
>
> 더 깊이 알아보고 싶으신가요? 다음 리소스를 확인하세요:
>
> - 📖 [GitHub Models 문서](https://docs.github.com/en/github-models)
> - ⚡ GitHub Models의 [사용량 제한](https://docs.github.com/en/github-models/use-github-models/prototyping-with-ai-models#rate-limits) 및 [무료 제한 초과](https://github.blog/changelog/2025-06-24-github-models-now-supports-moving-beyond-free-limits/)

### ⌨️ 활동: 첫 번째 AI 워크플로우 만들기

이제 개념을 이해했으니, 실습해 봅시다! 이 리포지토리의 새 탭을 열어 다음 단계를 따르세요.

GitHub UI에서 수동으로 트리거할 수 있는 간단한 워크플로우를 만들어 봅시다.

1. 리포지토리의 `Code` 탭으로 이동합니다. 그런 다음 `.github/workflows/` 디렉토리로 이동합니다.

1. `Add File`을 클릭하고 다음 이름의 새 워크플로우 파일을 만듭니다

   ```text
   ask-ai.yml
   ```

1. 워크플로우 이름, 수동 이벤트 트리거 및 필요한 권한을 추가합니다:

   ```yaml
   name: Ask AI
   on:
     workflow_dispatch:

   permissions:
     models: read
   ```

   > ❗ **주의:** 내용을 그대로 복사하세요. 이 정확한 워크플로우 이름(`Ask AI`)이 이 실습의 다음 단계로 진행하는 데 필요합니다.

1. 이제 AI 추론 액션을 사용하는 작업을 추가합니다.

   이 간단한 시나리오에서는 AI에게 간단한 하드코딩된 질문을 하고 워크플로우 요약에 응답을 표시합니다:

   ```yaml
   jobs:
     ask-ai:
       runs-on: ubuntu-latest

       steps:
         - name: AI Inference
           id: ai-response
           uses: actions/ai-inference@v2
           with:
             token: {% raw %}${{ secrets.GITHUB_TOKEN }}{% endraw %}
             prompt: |
               Give me a programming joke.

         - name: Display AI Response
           run: |
             echo "## 🤖 AI Response" >> $GITHUB_STEP_SUMMARY
             echo "" >> $GITHUB_STEP_SUMMARY
             echo "{% raw %}${{ steps.ai-response.outputs.response }}{% endraw %}" >> $GITHUB_STEP_SUMMARY
   ```

   > ❗ **주의:** YAML 형식에 주의하세요! GitHub의 파일 에디터는 특정 YAML 오류에 빨간 밑줄을 표시합니다.

1. 워크플로우 파일을 `main` 브랜치에 직접 커밋합니다.

### ⌨️ 활동: AI 워크플로우 테스트하기

이제 방금 만든 워크플로우를 테스트하여 AI가 작동하는 것을 확인해 봅시다!

1. 리포지토리의 **Actions** 탭으로 이동합니다.

1. 왼쪽 사이드바에서 워크플로우 목록에 있는 **Ask AI** 워크플로우를 찾아 클릭합니다.

1. **Run workflow** 버튼을 클릭하고, 기본 브랜치를 선택한 상태에서 녹색 **Run workflow** 버튼을 클릭하여 트리거합니다.

   <img width="900" alt="run workflow manual trigger" src="https://github.com/user-attachments/assets/89d96ce7-ca5e-4f5f-b8d0-25ebd5cdc4d6" />

1. 워크플로우가 완료될 때까지 기다린 후 워크플로우 실행 요약을 확인하여 AI의 응답이 깔끔하게 형식화되어 표시되는지 확인합니다.

1. 워크플로우가 성공적으로 완료되면, Mona가 자동으로 학습 여정의 다음 단계를 준비합니다!

<details>
<summary>문제가 있으신가요? 🤷</summary><br/>

- **워크플로우가 실행되지 않음**: 워크플로우가 완전하고 올바른 YAML 형식인지 확인합니다. 그렇지 않다면:
  - 워크플로우의 문제를 찾아 `main` 브랜치에 변경 사항을 다시 커밋합니다
  - 워크플로우를 다시 실행해 봅니다
- **AI 응답 없음**: `id: ai-response`가 AI Inference 단계에 설정되어 있고 Display 단계에서 올바르게 참조되는지 확인합니다
- **권한 오류**: 워크플로우 파일에서 `models: read` 권한이 올바르게 구성되어 있는지 다시 확인합니다
- **액션을 찾을 수 없음**: 정확한 액션 이름을 사용하고 있는지 확인합니다: `actions/ai-inference@v2`

</details>
