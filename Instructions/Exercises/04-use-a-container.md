---
lab:
  title: Azure AI 서비스 컨테이너 사용
  module: Module 2 - Developing AI Apps with Azure AI Services
---

# Azure AI 서비스 컨테이너 사용

Azure에서 호스트되는 Azure AI 서비스를 사용하는 애플리케이션 개발자는 직접 작성하는 코드용 인프라 관련 작업을 중점적으로 수행하는 동시에 Microsoft에서 관리하는 확장성 있는 서비스도 활용할 수 있습니다. 그러나 조직에서는 서비스 인프라 및 서비스 간에 전달되는 데이터를 더욱 철저하게 제어해야 하는 경우가 많습니다.

다수의 Azure AI 서비스 API는 *컨테이너*에 패키지되어 배포될 수 있으므로 조직은 자체 인프라에서 Azure AI 서비스를 호스팅할 수 있습니다. 예를 들어, 로컬 Docker 서버, Azure Container Instances 또는 Azure Kubernetes Services 클러스터에 있습니다. 컨테이너화된 Azure AI 서비스는 청구 지원을 위해 Azure 기반 Azure AI 서비스 계정과 통신해야 합니다. 그러나 애플리케이션 데이터는 백 엔드 서비스로 전달되지 않으며 조직은 컨테이너 배포 구성을 더욱 자세히 제어할 수 있습니다. 그러므로 인증, 확장성 및 기타 고려 사항 충족을 위한 사용자 지정 솔루션을 활용할 수 있습니다.

## Visual Studio Code에서 리포지토리 복제

Visual Studio Code를 사용하여 코드를 개발합니다. 앱의 코드 파일은 GitHub 리포지토리에 제공되었습니다.

> **팁**: **mslearn-ai-services** 리포지토리를 이미 복제한 경우 Visual Studio Code에서 엽니다. 그렇지 않은 경우에는 다음 단계에 따라 개발 환경에 복제합니다.

1. Visual Studio Code 시작
2. 팔레트를 열고(Shift+Ctrl+P) **Git: Clone** 명령을 실행하여 `https://github.com/MicrosoftLearning/mslearn-ai-services` 리포지토리를 로컬 폴더(아무 폴더나 관계없음)에 복제합니다.
3. 리포지토리가 복제되면 Visual Studio Code에서 폴더를 엽니다.
4. 필요한 경우 리포지토리에서 C# 코드 프로젝트를 지원하기 위해 추가 파일이 설치되는 동안 기다립니다.

    > **참고**: 빌드 및 디버깅에 필요한 자산을 추가하라는 메시지가 표시되면 **나중에**를 선택합니다.

5. 
          `Labfiles/04-use-a-container` 폴더를 펼칩니다.

## Azure AI 서비스 리소스 프로비전

구독에 아직 없는 경우 **Azure AI 서비스** 리소스를 프로비전해야 합니다.

1. `https://portal.azure.com`의 Azure Portal을 열고 Azure 구독과 연관된 Microsoft 계정을 사용하여 로그인합니다.
2. 상단 검색 창에서 *Azure AI 서비스*를 검색하고 **Azure AI 서비스 다중 서비스 계정**을 선택한 후 다음 설정으로 리소스를 만듭니다.
    - **구독**: ‘Azure 구독’
    - **리소스 그룹**: 리소스 그룹 선택 또는 만들기(제한된 구독을 사용 중이라면 새 리소스 그룹을 만들 권한이 없을 수도 있으므로 제공된 리소스 그룹 사용)**
    - **지역**: *사용 가능한 지역 선택*
    - **이름**: *고유 이름 입력*
    - **가격 책정 계층**: 표준 S0
3. 필요한 확인란을 선택하고 리소스를 만듭니다.
4. 배포가 완료될 때까지 기다린 다음, 배포 세부 정보를 봅니다.
5. 리소스가 배포되면 해당 리소스로 이동하여 **키 및 엔드포인트** 페이지를 확인합니다. 다음 절차에서 이 페이지에 표시되는 키 중 하나와 엔드포인트가 필요합니다.

## 감성 분석 컨테이너 배포 및 실행

일반적으로 사용되는 많은 Azure AI 서비스 API를 컨테이너 이미지에서 사용할 수 있습니다. 전체 목록을 보려면 [Azure AI 서비스 설명서](https://learn.microsoft.com/en-us/azure/ai-services/cognitive-services-container-support#containers-in-azure-ai-services)를 확인합니다. 이 연습에서는 Text Analytics *감정 분석* API용 컨테이너 이미지를 사용하지만 사용 가능한 모든 이미지에는 동일한 원칙이 적용됩니다.

1. Azure Portal **홈** 페이지에서 **&#65291;리소스 만들기** 단추를 선택하고 *container instances*를 검색한 후에 다음 설정을 사용하여 **Container Instances** 리소스를 만듭니다.

    - **기본 사항**:
        - **구독**: ‘Azure 구독’
        - **리소스 그룹**: *Azure AI 서비스 리소스가 포함된 리소스 그룹을 선택합니다.*
        - **컨테이너 이름**: 고유한 이름 입력**
        - **지역**: *사용 가능한 지역 선택*
        - **가용성 영역**: 없음
        - **SKU**: 표준
        - **이미지 원본**: 기타 레지스트리
        - **이미지 형식**: 공개
        - **이미지**: `mcr.microsoft.com/azure-cognitive-services/textanalytics/sentiment:latest`
        - **OS 유형**: Linux
        - **크기**: 1 vcpu, 8GB 메모리
    - **네트워킹**:
        - **네트워킹 유형**: 공개
        - **DNS 이름 레이블**: 컨테이너 엔드포인트의 고유한 이름 입력**
        - **포트**: TCP 포트를 80에서 **5000**으로 변경**
    - **고급:**
        - **정책 다시 시작**: 실패 시
        - **환경 변수**:

            | 원본으로 표시 | 키 | 값 |
            | -------------- | --- | ----- |
            | 예 | `ApiKey` | *Azure AI 서비스 리소스에 대한 키 중 하나* |
            | 예 | `Billing` | *Azure AI 서비스 리소스의 엔드포인트 URI* |
            | 아니요 | `Eula` | `accept` |

        - **명령 재정의**: [ ]
        - **키 관리**: Microsoft 관리형 키(MMK)
    - **태그**:
        - 태그 추가 안 함**

2. **검토 + 만들기**, **만들기**를 차례로 선택합니다. 배포가 완료될 때까지 기다린 다음, 배포된 리소스로 이동합니다.
    > **참고** Azure AI 컨테이너를 Azure Container Instances에 배포하는 데는 일반적으로 사용할 준비가 되기까지 5~10분(프로비전)이 소요됩니다.
3. 컨테이너 인스턴스 리소스의 **개요** 페이지에서 다음 속성을 살펴봅니다.
    - **상태**: *실행 중*이어야 합니다.
    - **IP 주소**: Container Instances에 액세스하는 데 사용할 수 있는 공용 IP 주소입니다.
    - **FQDN**: Container Instances 리소스의 *정규화된 도메인 이름*입니다. IP 주소 대신 이 이름을 사용하여 Container Instances에 액세스할 수 있습니다.

    > **참고**: 이 연습에서는 ACI(Azure Container Instances) 리소스로의 감정 분석을 위해 Azure AI 서비스 컨테이너 이미지를 배포했습니다. 비슷한 접근 방식을 사용하여 사용자 컴퓨터 또는 네트워크의 *[Docker](https://www.docker.com/products/docker-desktop)* 호스트에 배포하려면 다음 명령(한 줄)을 실행하여 감정 분석 컨테이너를 로컬 Docker 인스턴스에 배포하고, *&lt;yourEndpoint&gt;* 및 *&lt;yourKey&gt;* 를 엔드포인트 URI 및 Azure AI 서비스 리소스에 대한 키 중 하나로 대체하면 됩니다.
    > 이 명령은 로컬 컴퓨터에서 이미지를 찾은 후 이미지가 없으면 *mcr.microsoft.com* 이미지 레지스트리에서 이미지를 끌어와 Docker 인스턴스에 배포합니다. 배포가 완료되면 컨테이너가 시작되어 포트 5000에서 들어오는 요청을 수신 대기합니다.

    ```
    docker run --rm -it -p 5000:5000 --memory 8g --cpus 1 mcr.microsoft.com/azure-cognitive-services/textanalytics/sentiment:latest Eula=accept Billing=<yourEndpoint> ApiKey=<yourKey>
    ```

## 컨테이너 사용

1. 편집기에서 **rest-test.cmd**를 열고 여기에 포함된 **curl** 명령을 편집합니다(아래 참조). 여기서 *&lt;your_ACI_IP_address_or_FQDN&gt;* 을 컨테이너의 IP 주소 또는 FQDN으로 바꿉니다.

    ```
    curl -X POST "http://<your_ACI_IP_address_or_FQDN>:5000/text/analytics/v3.1/sentiment" -H "Content-Type: application/json" --data-ascii "{'documents':[{'id':1,'text':'The performance was amazing! The sound could have been clearer.'},{'id':2,'text':'The food and service were unacceptable. While the host was nice, the waiter was rude and food was cold.'}]}"
    ```

2. **CTRL+S**를 눌러 스크립트 변경 내용을 저장합니다. Azure AI 서비스 엔드포인트 또는 키를 지정할 필요가 없습니다. 이 요청은 컨테이너화된 서비스에 의해 처리됩니다. 컨테이너는 Azure의 서비스와 주기적으로 통신하여 청구용으로 사용량을 보고하지만 요청 데이터를 전송하지는 않습니다.
3. 다음 명령을 입력하여 스크립트를 실행합니다.

    ```
    ./rest-test.cmd
    ```

4. 명령이 입력 문서 2개에서 감지된 감정(긍정 및 부정, 순서대로)에 대한 정보가 포함된 JSON 문서를 반환하는지 확인합니다.

## 정리

컨테이너 인스턴스를 사용한 실험을 완료한 후에는 컨테이너 인스턴스를 삭제해야 합니다.

1. Azure Portal에서 이 연습용으로 리소스를 만든 리소스 그룹을 엽니다.
2. 컨테이너 인스턴스 리소스를 선택하여 삭제합니다.

## 리소스 정리

다른 학습 모듈을 위해 이 랩에서 만들어진 Azure 리소스를 사용하지 않는 경우 해당 리소스를 삭제하여 추가 요금이 발생하지 않도록 할 수 있습니다.

1. `https://portal.azure.com`에서 Azure Portal을 열고 상단 검색 창에서 이 랩에서 만든 리소스를 검색합니다.

2. 리소스 페이지에서 **삭제**를 선택하고 지침에 따라 리소스를 삭제합니다. 또는 전체 리소스 그룹을 삭제하여 모든 리소스를 동시에 정리할 수 있습니다.

## 자세한 정보

Azure AI 서비스 컨테이너화에 대한 자세한 내용은 [Azure AI 서비스 컨테이너 설명서](https://learn.microsoft.com/azure/ai-services/cognitive-services-container-support)를 참조하세요.
