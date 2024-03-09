---
lab:
  title: 랩 환경 설정
  module: Setup
---

# 랩 환경 설정

연습은 호스트된 랩 환경에서 완료되어야 합니다. 사용자의 개인 컴퓨터에서 완료하려면 다음 소프트웨어를 설치하여 완료하면 됩니다. 사용자의 개인 환경을 사용할 경우 예기치 않은 대화 상자와 동작이 발생할 수 있습니다. 가능한 로컬 구성이 광범위하기 때문에 과정 팀에서는 사용자의 개인 환경에서 발생할 수 있는 문제를 지원할 수 없습니다.

> **참고**: 아래 지침은 Windows 11용입니다. Linux 또는 MacOS를 사용할 수도 있습니다. 선택한 OS의 랩 지침을 조정해야 할 수도 있습니다.

### 기본 운영 체제(Windows 11)

#### Windows 10

Windows 11을 설치하고 모든 업데이트를 적용합니다.

#### Edge

[Edge(Chromium)](https://microsoft.com/edge) 설치

### .NET Core SDK

1. https://dotnet.microsoft.com/download에서 다운로드 및 설치합니다(런타임뿐만 아니라 .NET Core SDK 다운로드). 이 과정의 랩을 사용자의 컴퓨터에서 실행하는 경우 .NET 7.0이 설치되어 있어야 합니다.

### C++ 재배포 가능 패키지

1. https://aka.ms/vs/16/release/vc_redist.x64.exe에서 Visual C++ 재배포 가능 패키지(x64) 다운로드 및 설치

### Node.JS

1. https://nodejs.org/en/download/에서 최신 LTS 버전 다운로드 
2. 기본 옵션을 사용하여 설치합니다.

### Python(및 필수 패키지)

1. https://docs.conda.io/en/latest/miniconda.html에서 버전 3.11 다운로드 
2. 설치 프로그램을 실행하여 설치 - **중요**: PATH 변수에 Miniconda를 추가하고 Miniconda를 기본 Python 환경으로 등록하는 옵션을 선택합니다.
3. 설치 후 Anaconda 프롬프트를 열고 다음 명령을 입력하여 패키지를 설치합니다. 

```
pip install flask requests python-dotenv pylint matplotlib pillow
pip install --upgrade numpy
```

### Azure CLI

1. https://docs.microsoft.com/en-us/cli/azure/install-azure-cli?view=azure-cli-latest에서 다운로드 
2. 기본 옵션을 사용하여 설치합니다.

### Git

1. https://git-scm.com/download.html에서 기본 옵션을 사용하여 다운로드 및 설치


### Visual Studio Code(및 확장)

1. https://code.visualstudio.com/Download에서 다운로드 
2. 기본 옵션을 사용하여 설치합니다. 
3. 설치 후 Visual Studio Code를 시작하고 **확장** 탭(Ctrl+Shift+X를 누르면 표시됨)에서 다음 Microsoft 확장을 검색하여 설치합니다.
    - Python
    - C#
    - Azure 기능
    - PowerShell
