# Actions에서 AI 활용하기

_GitHub Models를 사용하여 AI 모델을 GitHub Actions 워크플로우에 직접 통합하는 방법을 배워봅니다._

## 환영합니다

- **대상**: AI 통합이 처음인 개발자로, GitHub Actions 워크플로우에 AI 기능을 통합하는 방법을 배우고 싶은 분
- **학습 내용**: GitHub Models 서비스를 사용하여 워크플로우에 AI 추론을 추가하고, 동적 데이터를 AI 프롬프트에 전달하며, AI 액션을 다른 GitHub Actions와 결합하는 방법
- **만들 것**: 기본 AI 질문-답변 워크플로우와 새 이슈에 자동으로 댓글을 다는 자동화된 이슈 분석 시스템
- **사전 요구사항**: GitHub Actions에 대한 기본적인 이해 (예: [GitHub Actions 시작하기](https://github.com/skills-kr/hello-github-actions) 실습에서 배운 내용)
- **소요 시간**: 이 실습은 완료하는 데 30분 미만이 소요됩니다.

이 실습에서는 다음을 수행합니다:

1. 적절한 권한을 가진 기본 AI 추론 워크플로우를 설정합니다
1. AI 추론과 다른 GitHub Actions를 결합하여 지능적인 자동화 워크플로우를 만드는 방법을 배웁니다
1. 자동화된 AI 이슈 분석 워크플로우를 구축합니다

### 이 실습을 시작하는 방법

실습을 계정에 복사한 다음 여러분의 Octocat(Mona)에게 **약 20초**를 주어 첫 번째 수업을 준비하게 한 뒤, **페이지를 새로고침**합니다.

[![](https://img.shields.io/badge/%EC%8B%A4%EC%8A%B5%20%EB%B3%B5%EC%82%AC-%E2%86%92-1f883d?style=for-the-badge&logo=github&labelColor=197935)](https://github.com/new?template_owner=skills-kr&template_name=ai-in-actions&owner=%40me&name=skills-ai-in-actions&description=%EC%8B%A4%EC%8A%B5%3A+Actions%EC%97%90%EC%84%9C+AI+%ED%99%9C%EC%9A%A9%ED%95%98%EA%B8%B0&visibility=public)

<details>
<summary>문제가 있으신가요? 🤷</summary><br/>

실습을 복사할 때 다음 설정을 권장합니다:

- 소유자(owner)는 개인 계정 또는 리포지토리를 호스팅할 조직을 선택합니다.

- 프라이빗 리포지토리는 Actions 시간을 사용하므로 퍼블릭 리포지토리를 만드는 것을 권장합니다.

20초 후에도 실습이 준비되지 않으면 [Actions](../../actions) 탭을 확인하세요.

- 작업이 실행 중인지 확인합니다. 때로는 시간이 조금 더 걸릴 수 있습니다.

- 페이지에 실패한 작업이 표시되면 이슈를 제출해 주세요. 버그를 발견하셨네요! 🐛

</details>

---

> **참고**: 이 실습은 [skills/ai-in-actions](https://github.com/skills/ai-in-actions)를 기반으로 한글화하고, [🏆 GitHub Skills Workshop Dashboard](https://github-skills.studydev.com)와 연계되어 있습니다.

&copy; 2025 GitHub &bull; [행동 강령](https://www.contributor-covenant.org/version/2/1/code_of_conduct/code_of_conduct.md) &bull; [MIT 라이선스](https://gh.io/mit)
