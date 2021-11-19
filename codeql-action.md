## CodeQL 작업

이 작업은 GitHub의 업계 최고의 의미 체계 코드 분석 엔진인 CodeQL을 저장소의 소스 코드에 대해 실행하여 보안 취약점을 찾습니다. 
그런 다음 리포지토리의 보안 탭에 표시될 수 있도록 결과를 GitHub에 자동으로 업로드합니다. 
CodeQL은 커뮤니티와 [GitHub 보안 연구소](https://securitylab.github.com/) 에서 개발한 확장 가능한 
[쿼리](https://github.com/github/codeql) 세트를 실행 하여 코드에서 일반적인 취약점을 찾습니다.

최근 변경 사항 목록은 CodeQL 작업의 [변경 로그](CHANGELOG.md)를 참조하십시오 .

## 특허
이 프로젝트는 [MIT 라이선스]에 따라 배포됩니다 .

이 작업에 사용되는 기본 CodeQL CLI는 [GitHub CodeQL 이용 약관] 에 따라 라이선스가 부여됩니다. 
따라서 이 작업은 GitHub에서 호스팅되는 오픈 소스 프로젝트와 GitHub 고급 보안이 활성화된 조직이 소유한 비공개 리포지토리에서 사용할 수 있습니다.

## 용법

이것은 짧은 연습이지만 자세한 내용은 [코드 스캔 구성] 을 참조하세요 .

리포지토리의 CodeQL 분석에서 코드 스캔 결과를 얻으려면 다음 워크플로를 템플릿으로 사용할 수 있습니다.

name: "Code Scanning - Action"

on:
  push:
    branches: [main]
  pull_request:
    branches: [main]
  schedule:
    #        ┌───────────── minute (0 - 59)
    #        │  ┌───────────── hour (0 - 23)
    #        │  │ ┌───────────── day of the month (1 - 31)
    #        │  │ │ ┌───────────── month (1 - 12 or JAN-DEC)
    #        │  │ │ │ ┌───────────── day of the week (0 - 6 or SUN-SAT)
    #        │  │ │ │ │
    #        │  │ │ │ │
    #        │  │ │ │ │
    #        *  * * * *
    - cron: '30 1 * * 0'

jobs:
  CodeQL-Build:
    # CodeQL runs on ubuntu-latest, windows-latest, and macos-latest
    runs-on: ubuntu-latest

    permissions:
      # required for all workflows
      security-events: write

      # only required for workflows in private repositories
      actions: read
      contents: read

    steps:
      - name: Checkout repository
        uses: actions/checkout@v2

      # Initializes the CodeQL tools for scanning.
      - name: Initialize CodeQL
        uses: github/codeql-action/init@v1
        # Override language selection by uncommenting this and choosing your languages
        # with:
        #   languages: go, javascript, csharp, python, cpp, java

      # Autobuild attempts to build any compiled languages (C/C++, C#, or Java).
      # If this step fails, then you should remove it and run the build manually (see below).
      - name: Autobuild
        uses: github/codeql-action/autobuild@v1

      # ℹ️ Command-line programs to run using the OS shell.
      # 📚 https://git.io/JvXDl

      # ✏️ If the Autobuild fails above, remove it and uncomment the following
      #    three lines and modify them (or add more) to build your code if your
      #    project uses a compiled language

      #- run: |
      #     make bootstrap
      #     make release

      - name: Perform CodeQL Analysis
        uses: github/codeql-action/analyze@v1
```

If you prefer to integrate this within an existing CI workflow, it should end up looking something like this:

```yaml
- name: Initialize CodeQL
  uses: github/codeql-action/init@v1
  with:
    languages: go, javascript
