---
lab:
  title: Azure AI 서비스 모니터링
  module: Module 2 - Developing AI Apps with Azure AI Services
---

# Azure AI 서비스 모니터링

Azure AI 서비스는 전체 애플리케이션 인프라의 중요한 부분이 될 수 있습니다. 그러므로 활동을 모니터링하여 확인이 필요할 수 있는 문제 관련 경고를 받을 수 있어야 합니다.

## Visual Studio Code에서 리포지토리 복제

Visual Studio Code를 사용하여 코드를 개발합니다. 앱의 코드 파일은 GitHub 리포지토리에 제공되었습니다.

> **팁**: **mslearn-ai-services** 리포지토리를 이미 복제한 경우 Visual Studio Code에서 엽니다. 그렇지 않은 경우에는 다음 단계에 따라 개발 환경에 복제합니다.

1. Visual Studio Code 시작
2. 팔레트를 열고(Shift+Ctrl+P) **Git: Clone** 명령을 실행하여 `https://github.com/MicrosoftLearning/mslearn-ai-services` 리포지토리를 로컬 폴더(아무 폴더나 관계없음)에 복제합니다.
3. 리포지토리가 복제되면 Visual Studio Code에서 폴더를 엽니다.
4. 필요한 경우 리포지토리에서 C# 코드 프로젝트를 지원하기 위해 추가 파일이 설치되는 동안 기다립니다.

    > **참고**: 빌드 및 디버깅에 필요한 자산을 추가하라는 메시지가 표시되면 **나중에**를 선택합니다.

5. 
          `Labfiles/03-monitor-ai-services` 폴더를 펼칩니다.

C# 및 Python용 코드가 모두 제공되었습니다. 기본 설정 언어의 폴더를 확장합니다.

## Azure AI 서비스 리소스 프로비전

구독에 아직 없는 경우 **Azure AI 서비스** 리소스를 프로비전해야 합니다.

1. `https://portal.azure.com`의 Azure Portal을 열고 Azure 구독과 연관된 Microsoft 계정을 사용하여 로그인합니다.
2. 상단 검색 창에서 *Azure AI 서비스*를 검색하고 **Azure AI 서비스**를 선택한 후 다음 설정을 사용하여 Azure AI 서비스 다중 서비스 계정 리소스를 만듭니다.
    - **구독**: ‘Azure 구독’
    - **리소스 그룹**: 리소스 그룹 선택 또는 만들기(제한된 구독을 사용 중이라면 새 리소스 그룹을 만들 권한이 없을 수도 있으므로 제공된 리소스 그룹 사용)**
    - **지역**: *사용 가능한 지역 선택*
    - **이름**: *고유 이름 입력*
    - **가격 책정 계층**: 표준 S0
3. 필요한 체크박스를 선택하여 리소스를 만듭니다.
4. 배포가 완료될 때까지 기다린 다음, 배포 세부 정보를 봅니다.
5. 리소스가 배포되면 해당 리소스로 이동하여 **키 및 엔드포인트** 페이지를 확인합니다. 나중에 필요하므로 엔드포인트 URI를 적어 둡니다.

## 경고 구성

Azure AI 서비스 리소스에서 작업을 검색할 수 있도록 경고 규칙을 정의하여 모니터링을 시작할 예정입니다.

1. Azure Portal에서 Azure AI 서비스 리소스로 이동하여 **경고** 페이지(**모니터링** 섹션)를 확인합니다.
2. **+ 만들기** 드롭다운을 선택하고 **경고 규칙**을 선택합니다.
3. **경고 규칙 만들기** 페이지의 **범위** 아래에 Azure AI 서비스 리소스가 나열되어 있는지 확인합니다. (**신호 선택** 창이 열려 있으면 닫습니다)
4. **조건** 탭을 선택하고 **모든 신호 보기** 링크를 선택하면 모니터링할 신호 유형을 선택할 수 있는 **신호 선택** 창이 오른쪽에 나타납니다.
5. **신호 유형** 목록에서 **활동 로그** 섹션까지 아래로 스크롤한 다음 **키 목록(Cognitive Services API 계정)** 을 선택합니다. 그런 다음 **적용**을 선택합니다.
6. 지난 6시간 동안의 활동을 검토합니다.
7. **작업** 탭을 선택합니다. 작업 그룹을 지정할 수 있습니다.** 작업 그룹을 지정하면 경고 발생 시에 수행되는 전자 메일 알림 전송 등의 자동화된 작업을 구성할 수 있습니다. 이 연습에서는 자동화된 작업을 구성하지 않지만 프로덕션 환경에서는 이러한 작업을 구성하면 유용할 수 있습니다.
8. **세부 정보** 탭에서 **경고 규칙 이름**을 **키 목록 경고**로 설정합니다.
9. **검토 + 만들기**를 선택합니다. 
10. 경고에 대한 구성을 검토합니다. **만들기**를 선택하고, 경고 규칙이 만들어질 때까지 기다립니다.
11. 이제 다음 명령을 사용하여 Azure AI 서비스 키 목록을 가져올 수 있습니다. 여기서 *&lt;resourceName&gt;* 을 Azure AI 서비스 리소스의 이름으로 바꾸고, *&lt;resourceGroup&gt;* 을 이를 만든 리소스 그룹의 이름으로 바꿉니다.

    ```
    az cognitiveservices account keys list --name <resourceName> --resource-group <resourceGroup>
    ```

    이 명령은 Azure AI 서비스 리소스에 대한 키 목록을 반환합니다.

    > **참고** Azure CLI에 로그인하지 않은 경우 목록 키 명령이 작동하기 전에 `az login`을 실행해야 할 수도 있습니다.

12. Azure Portal이 표시된 브라우저로 다시 전환하여 **경고 페이지**를 새로 고칩니다. 표에 **심각도 4** 경고가 나열됩니다(이 경고가 나열되지 않으면 최대 5분까지 기다렸다가 페이지를 다시 새로 고치세요).
13. 경고를 선택하여 세부 정보를 봅니다.

## 메트릭 시각화

경고를 정의하는 것 외에도 Azure AI 서비스 리소스에 대한 메트릭을 보고 사용률을 모니터링할 수 있습니다.

1. Azure Portal의 Azure AI 서비스 리소스 페이지에서 **메트릭**(**모니터링** 섹션)을 선택합니다.
2. 기존 차트가 없으면 **+ 새 차트**를 선택합니다. 그런 다음 **메트릭** 목록에서 시각화할 수 있는 메트릭을 검토하고 **총 통화**를 선택합니다.
3. **집계** 목록에서 **개수**를 선택합니다.  이렇게 하면 Azure AI 서비스 리소스에 대한 총 호출 수를 모니터링할 수 있습니다. 그러면 일정 기간 동안의 서비스 사용량을 확인하려는 경우 유용합니다.
4. Azure AI 서비스에 대한 일부 요청을 생성하려면 HTTP 요청용 명령줄 도구인 **curl**을 사용합니다. 편집기에서 **rest-test.cmd**를 열고 여기에 포함된 **curl** 명령을 편집합니다(아래 참조). 여기서 Azure AI 서비스 리소스의 Text Analytics API를 사용하도록 *&lt;yourEndpoint&gt;* 및 *&lt;yourKey&gt;* 를 엔드포인트 URI 및 **Key1** 키로 바꿉니다.

    ```
    curl -X POST "<your-endpoint>/language/:analyze-text?api-version=2023-04-01" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: <your-key>" --data-ascii "{'analysisInput':{'documents':[{'id':1,'text':'hello'}]}, 'kind': 'LanguageDetection'}"
    ```

5. 변경 내용을 저장한 후 다음 명령을 실행합니다.

    ```
    ./rest-test.cmd
    ```

    이 명령은 입력 데이터에서 감지된 언어(영어) 관련 정보가 포함된 JSON 문서를 반환합니다.

6. **rest-test** 명령을 여러 번 다시 실행하여 호출 활동을 생성합니다(**^** 키를 사용하여 이전 명령을 차례로 다시 실행할 수 있음).
7. Azure Portal의 **메트릭** 페이지로 돌아와 **총 통화** 개수 차트를 새로 고칩니다. *curl*을 사용하여 수행한 호출이 차트에 반영되려면 몇 분 정도 걸릴 수 있습니다. 해당 호출이 포함되어 차트가 업데이트될 때까지 차트를 계속 새로 고칩니다.

## 리소스 정리

다른 학습 모듈을 위해 이 랩에서 만들어진 Azure 리소스를 사용하지 않는 경우 해당 리소스를 삭제하여 추가 요금이 발생하지 않도록 할 수 있습니다.

1. `https://portal.azure.com`에서 Azure Portal을 열고 상단 검색 창에서 이 랩에서 만든 리소스를 검색합니다.

2. 리소스 페이지에서 **삭제**를 선택하고 지침에 따라 리소스를 삭제합니다. 또는 전체 리소스 그룹을 삭제하여 모든 리소스를 동시에 정리할 수 있습니다.

## 자세한 정보

Azure AI 서비스를 모니터링하는 옵션 중 하나는 *진단 로깅*을 사용하는 것입니다. 진단 로깅을 사용하도록 설정하면 Azure AI 서비스 리소스 사용량과 관련된 다양한 정보가 캡처됩니다. 따라서 진단 로깅을 유용한 모니터링 및 디버깅 도구로 활용할 수 있습니다. 진단 로깅을 설정한 후 정보가 생성되기까지는 1시간 넘게 걸릴 수 있습니다. 이러한 이유로 인해 이 연습에서는 진단 로깅 사용법을 살펴보지 않았습니다. [Azure AI 서비스 설명서](https://docs.microsoft.com/azure/ai-services/diagnostic-logging)에서 진단 로깅에 대해 자세히 알아볼 수 있습니다.