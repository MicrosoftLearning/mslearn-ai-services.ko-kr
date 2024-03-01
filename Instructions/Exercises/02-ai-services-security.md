---
lab:
  title: Azure AI 서비스 보안 관리
  module: Module 2 - Developing AI Apps with Azure AI Services
---

# Azure AI 서비스 보안 관리

보안은 모든 애플리케이션에서 중요한 고려 사항이며, 개발자는 Azure AI 서비스 등의 리소스가 필요한 사용자만 해당 리소스에 액세스할 수 있도록 제한해야 합니다.

Azure AI 서비스에 대한 액세스는 일반적으로 Azure AI 서비스 리소스를 처음 만들 때 만들어지는 인증 키를 통해 제어됩니다.

## Visual Studio Code에서 리포지토리 복제

Visual Studio Code를 사용하여 코드를 개발합니다. 앱의 코드 파일은 GitHub 리포지토리에 제공되었습니다.

> **팁**: 최근에 이미 **mslearn-ai-services** 리포지토리를 복제한 경우 Visual Studio Code에서 엽니다. 그렇지 않은 경우에는 다음 단계에 따라 개발 환경에 복제합니다.

1. Visual Studio Code 시작
2. 팔레트를 열고(Shift+Ctrl+P) **Git: Clone** 명령을 실행하여 `https://github.com/MicrosoftLearning/mslearn-ai-services` 리포지토리를 로컬 폴더(아무 폴더나 관계없음)에 복제합니다.
3. 리포지토리가 복제되면 Visual Studio Code에서 폴더를 엽니다.
4. 필요한 경우 리포지토리에서 C# 코드 프로젝트를 지원하기 위해 추가 파일이 설치되는 동안 기다립니다.

    > **참고**: 빌드 및 디버깅에 필요한 자산을 추가하라는 메시지가 표시되면 **나중에**를 선택합니다.

5. 
          `Labfiles/02-ai-services-security` 폴더를 펼칩니다.

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
3. 필요한 확인란을 선택하고 리소스를 만듭니다.
4. 배포가 완료될 때까지 기다린 다음, 배포 세부 정보를 봅니다.

## 인증 키 관리

Azure AI 서비스 리소스를 만들 때 두 개의 인증 키가 만들어졌습니다. 이러한 키는 Azure Portal에서 관리할 수도 있고 Azure CLI(명령줄 인터페이스)를 사용하여 관리할 수도 있습니다.

1. Azure Portal에서 Azure AI 서비스 리소스로 이동하여 해당 **키 및 엔드포인트** 페이지를 확인합니다. 이 페이지에는 리소스에 연결하여 직접 개발한 애플리케이션에서 해당 리소스를 사용하려면 필요한 정보가 포함되어 있습니다. 특히 다음에 대해 주의하세요.
    - 클라이언트 애플리케이션이 요청을 보낼 수 있는 HTTP *엔드포인트*입니다.
    - 인증에 사용할 수 있는 두 개의 *키*(클라이언트 애플리케이션은 두 키 중 하나를 사용할 수 있습니다. 일반적으로는 개발과 프로덕션용으로 키를 하나씩 사용합니다. 개발자가 작업을 완료하고 나면 리소스에 계속 액세스할 수 없도록 개발 키를 쉽게 다시 생성할 수 있습니다.)
    - 리소스가 호스트되는 *위치*입니다. 일부 API(모든 API는 아님)에 요청을 보내려면 이 위치가 필요합니다.
2. 이제 다음 명령을 사용하여 Azure AI 서비스 키 목록을 가져올 수 있습니다. 여기서 *&lt;resourceName&gt;* 을 Azure AI 서비스 리소스의 이름으로 바꾸고, *&lt;resourceGroup&gt;* 을 이를 만든 리소스 그룹의 이름으로 바꿉니다.

    ```
    az cognitiveservices account keys list --name <resourceName> --resource-group <resourceGroup>
    ```

    이 명령은 Azure AI 서비스 리소스에 대한 키 목록을 반환합니다. **key1** 및 **key2**라는 두 개의 키가 있습니다.

    > **팁**: 아직 Azure CLI를 인증하지 않은 경우 `az login`을 실행하고 계정에 로그인합니다.

3. Azure AI 서비스를 테스트하려면 HTTP 요청용 명령줄 도구인 **curl**을 사용할 수 있습니다. **02-ai-services-security** 폴더에서 **rest-test.cmd**를 열고 여기에 포함된 **curl** 명령을 편집합니다(아래 참조). 여기서 Azure AI 서비스 리소스에서 Analyze Text API를 사용하려면 *&lt;yourEndpoint&gt;* 및 *&lt;yourKey&gt;* 를 엔드포인트 URI 및 **Key1** 키로 바꿉니다.

    ```bash
    curl -X POST "<yourEndpoint>/language/:analyze-text?api-version=2023-04-01" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: 81468b6728294aab99c489664a818197" --data-ascii "{'analysisInput':{'documents':[{'id':1,'text':'hello'}]}, 'kind': 'LanguageDetection'}"
    ```

4. 변경 내용을 저장한 후 다음 명령을 실행합니다.

    ```
    sh rest-test.sh
    ```

이 명령은 입력 데이터에서 감지된 언어(영어) 관련 정보가 포함된 JSON 문서를 반환합니다.

5. 키가 손상되었거나 키를 소유한 개발자가 더 이상 리소스에 액세스할 필요가 없는 경우 Azure Portal에서 Azure CLI를 사용하여 키를 다시 생성할 수 있습니다. 다음 명령을 실행하여 **key1** 키를 다시 생성합니다(*&lt;resourceName&gt;* 및 *&lt;resourceGroup&gt;* 은 리소스 관련 정보로 바꿔야 함).

    ```
    az cognitiveservices account keys regenerate --name <resourceName> --resource-group <resourceGroup> --key-name key1
    ```

Azure AI 서비스 리소스에 대한 키 목록이 반환됩니다. 마지막으로 검색한 이후 **key1**이 변경되었습니다.

6. 이전 키를 사용하여 **rest-test** 명령을 다시 실행하고(키보드의 **^** 화살표를 사용하여 이전 명령을 차례로 다시 실행할 수 있음) 이번에는 명령 실행이 실패함을 확인합니다.
7. **rest-test.cmd**의 *curl* 명령을 편집하여 키를 새 **key1** 값으로 바꾸고 변경 내용을 저장합니다. 그런 다음 **rest-test** 명령을 다시 실행하여 정상적으로 실행되는지 확인합니다.

> **팁**: 이 연습에서는 **--resource-group**과 같은 Azure CLI 매개 변수의 전체 이름을 사용했습니다.  **-g** 등의 더 짧은 이름을 사용하면 명령을 더 간단하게 작성할 수도 있습니다(하지만 명령을 이해하기는 좀 더 어려울 수도 있음).  [Azure AI 서비스 CLI 명령 참조](https://docs.microsoft.com/cli/azure/cognitiveservices?view=azure-cli-latest)에 각 Azure AI 서비스 CLI 명령에 대한 매개 변수 옵션이 나와 있습니다.

## Azure Key Vault를 사용하여 키 액세스 보호

인증용 키를 사용하여 Azure AI 서비스를 사용하는 애플리케이션을 개발할 수 있습니다. 하지만 이 경우에는 애플리케이션 코드가 키를 가져올 수 있어야 합니다. 애플리케이션이 배포되는 구성 파일이나 환경 변수에 키를 저장하는 옵션을 사용할 수도 있습니다. 그러나 이 방식을 사용하면 키가 무단 액세스에 취약해집니다. Azure에서 애플리케이션을 개발할 때 사용 가능한 더 효율적인 방식은 Azure Key Vault에 키를 안전하게 저장한 다음 *관리 ID*(애플리케이션 자체에 사용되는 사용자 계정)를 통해 키 액세스 권한을 제공하는 것입니다.

### 키 자격 증명 모음 만들기 및 비밀 추가

먼저 키 자격 증명 모음을 만들고 Azure AI 서비스 키에 대한 *비밀*을 추가해야 합니다.

1. Azure AI 서비스 리소스의 **key1** 값을 기록해 둡니다(또는 클립보드에 복사).
2. Azure Portal의 **홈** 페이지에서 **&#65291;리소스 만들기** 단추를 선택하고 *Key Vault*를 검색한 후 다음 설정으로 **Key Vault** 리소스를 만듭니다.

    - **기본 사항 탭**
        - **구독**: ‘Azure 구독’
        - **리소스 그룹**: *Azure AI 서비스 리소스와 동일한 리소스 그룹*
        - **Key Vault 이름**: 고유한 이름 입력**
        - **지역**: *Azure AI 서비스 리소스와 동일한 지역*
        - **가격 책정 계층**: 표준
    
    - **액세스 구성** 탭
        -  **권한 모델**: Vault 액세스 정책
        - **액세스 정책** 섹션까지 아래로 스크롤하고 왼쪽에 있는 확인란을 사용하여 사용자를 선택합니다. 그런 다음 **검토 + 만들기**를 선택하고 **만들기**를 선택하여 리소스를 만듭니다.
     
3. 배포가 완료될 때까지 기다렸다가 키 자격 증명 모음 리소스로 이동합니다.
4. 왼쪽 탐색 창에서 **비밀**(개체 섹션)을 선택합니다.
5. **+ 생성/가져오기**를 선택하고 다음 설정으로 새 비밀을 추가합니다.
    - **업로드 옵션**: 수동
    - **이름**: AI-Services-Key *(나중에 이 이름을 기준으로 비밀을 검색하는 코드를 실행할 것이므로 이 이름을 정확하게 사용해야 함)*
    - **값**: *사용자의 **key1** Azure AI 서비스 키*
6. **만들기**를 실행합니다.

### 서비스 주체 만들기

키 자격 증명 모음의 비밀에 액세스하려면 애플리케이션이 비밀 액세스 권한이 있는 서비스 주체를 사용해야 합니다. Azure CLI(명령줄 인터페이스)를 사용하여 서비스 주체를 만들고, 해당 개체 ID를 찾고, Azure Vault의 비밀에 대한 액세스 권한을 부여합니다.

1. 다음 Azure CLI 명령을 실행하여 *&lt;spName&gt;* 을 애플리케이션 ID에 적합한 고유한 이름으로 바꿉니다(예: 끝에 이니셜이 추가된 *ai-app*. 이름은 테넌트 내에서 고유해야 함). 또한 *&lt;subscriptionId&gt;* 및 *&lt;resourceGroup&gt;* 을 구독 ID와 Azure AI 서비스 및 키 자격 증명 모음 리소스가 포함된 리소스 그룹의 올바른 값으로 바꿉니다.

    > **팁**: 구독 ID가 확실하지 않은 경우 **az account show** 명령을 사용하여 구독 정보를 검색합니다. 구독 ID는 출력의 **id** 특성입니다. 이미 존재하는 개체에 대한 오류가 표시되면 다른 고유 이름을 선택하세요.

    ```
    az ad sp create-for-rbac -n "api://<spName>" --role owner --scopes subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>
    ```

이 명령의 출력에는 새 서비스 주체 관련 정보가 포함되어 있습니다. 다음과 같이 표시됩니다.

    ```
    {
        "appId": "abcd12345efghi67890jklmn",
        "displayName": "api://ai-app-",
        "password": "1a2b3c4d5e6f7g8h9i0j",
        "tenant": "1234abcd5678fghi90jklm"
    }
    ```

**appId**, **password** 및 **tenant** 값은 뒷부분에서 필요하므로 적어 두세요(이 터미널을 닫으면 암호를 검색할 수가 없으므로 값을 지금 적어 두어야 합니다. 나중에 필요한 값을 찾을 수 있도록 출력을 로컬 컴퓨터의 새 텍스트 파일에 붙여넣을 수 있습니다.)

2. 서비스 주체의 **개체 ID**를 가져오려면 다음 Azure CLI 명령을 실행하여 *&lt;appId&gt;* 를 서비스 주체의 앱 ID 값으로 바꿉니다.

    ```
    az ad sp show --id <appId>
    ```

3. 응답으로 반환된 json에 `id` 값을 복사합니다. 
3. 새 서비스 주체에게 Key Vault의 비밀에 액세스할 수 있는 권한을 할당하려면 다음 Azure CLI 명령을 실행합니다. 이 때 *&lt;keyVaultName&gt;* 을 Azure Key Vault 리소스 이름으로 바꾸고, *&lt;objectId&gt;* 를 방금 복사한 서비스 주체의 ID 값으로 바꿉니다.

    ```
    az keyvault set-policy -n <keyVaultName> --object-id <objectId> --secret-permissions get list
    ```

### 애플리케이션에서 서비스 주체 사용

이제 애플리케이션에서 서비스 주체 ID를 사용할 준비가 되었으므로 키 자격 증명 모음의 비밀 Azure AI 서비스 키에 액세스하고 이를 사용하여 Azure AI 서비스 리소스에 연결할 수 있습니다.

> **참고**: 이 연습에서는 애플리케이션 구성에 서비스 주체 자격 증명을 저장한 다음 애플리케이션 코드의 **ClientSecretCredential** ID를 인증하는 데 사용합니다. 개발 및 테스트 애플리케이션에서는 이러한 방식을 사용해도 되지만 실제 프로덕션 애플리케이션에서는 관리자가 애플리케이션에 *관리 ID*를 할당합니다. 그러면 애플리케이션이 암호를 저장하거나 캐시하지 않고 서비스 주체 ID를 사용해 리소스에 액세스할 수 있습니다.

1. 터미널에서 `cd C-Sharp` 또는 `cd Python`을 실행하여 언어 선택에 따라 **C-Sharp** 또는 **Python** 폴더로 전환합니다. 그런 다음 `cd keyvault_client`를 실행하여 앱 폴더로 이동합니다.
2. 언어 선택에 적절한 명령을 실행하여 Azure AI 서비스 리소스에서 Azure Key Vault 및 Text Analytics API에 사용해야 하는 패키지를 설치합니다.

    **C#**

    ```
    dotnet add package Azure.AI.TextAnalytics --version 5.3.0
    dotnet add package Azure.Identity --version 1.5.0
    dotnet add package Azure.Security.KeyVault.Secrets --version 4.2.0-beta.3
    ```

    **Python**

    ```
    pip install azure-ai-textanalytics==5.3.0
    pip install azure-identity==1.5.0
    pip install azure-keyvault-secrets==4.2.0
    ```

3. **keyvault-client** 폴더의 콘텐츠를 확인하고 구성 설정을 위한 파일이 포함되어 있는지 확인합니다.
    - **C#**: appsettings.json
    - **Python**: .env

    구성 파일을 열고 포함되어 있는 구성 값을 업데이트하여 다음 설정을 반영합니다.
    
    - Azure AI 서비스 리소스의 **엔드포인트**
    - **Azure Key Vault** 리소스의 이름
    - 서비스 주체의 **테넌트**
    - 서비스 주체의 **appId**
    - 서비스 주체의 **암호**

     **CTRL+S**를 눌러 변경 내용을 저장합니다.
4. **keyvault-client** 폴더에는 클라이언트 애플리케이션용 코드 파일이 포함되어 있습니다.

    - **C#**: Program.cs
    - **Python**: keyvault-client.py

    코드 파일을 열고 포함되어 있는 코드를 검토하여 다음 세부 정보를 확인합니다.
    - 설치한 SDK의 네임스페이스를 가져왔습니다.
    - **Main** 함수의 코드는 애플리케이션 구성 설정을 검색한 다음 서비스 주체 자격 증명을 사용하여 키 자격 증명 모음에서 Azure AI 서비스 키를 가져옵니다.
    - **GetLanguage** 함수는 SDK를 사용하여 서비스용 클라이언트를 만든 다음 클라이언트를 사용하여 입력된 텍스트의 언어를 검색합니다.
5. 프로그램을 실행하려면 다음 명령을 입력합니다.

    **C#**

    ```
    dotnet run
    ```

    **Python**

    ```
    python keyvault-client.py
    ```

6. 메시지가 표시되면 텍스트를 입력하고 서비스가 감지하는 언어를 검토합니다. 예를 들어, “Hello”, “Bonjour”, “Hola” 등을 입력해 봅니다.
7. 애플리케이션 테스트를 완료한 후 "quit"을 입력하여 프로그램을 중지합니다.

## 리소스 정리

다른 학습 모듈을 위해 이 랩에서 만들어진 Azure 리소스를 사용하지 않는 경우 해당 리소스를 삭제하여 추가 요금이 발생하지 않도록 할 수 있습니다.

1. `https://portal.azure.com`에서 Azure Portal을 열고 상단 검색 창에서 이 랩에서 만든 리소스를 검색합니다.

2. 리소스 페이지에서 **삭제**를 선택하고 지침에 따라 리소스를 삭제합니다. 또는 전체 리소스 그룹을 삭제하여 모든 리소스를 동시에 정리할 수 있습니다.

## 자세한 정보

Azure AI 서비스 보안에 대한 자세한 내용은 [Azure AI 서비스 보안 설명서](https://docs.microsoft.com/azure/ai-services/security-features)를 참조하세요.
