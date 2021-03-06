---
title: GitHub 작업을 사용 하 여 Azure Functions에서 코드 업데이트 수행
description: Github 작업을 사용 하 여 GitHub에서 Azure Functions 프로젝트를 빌드하고 배포 하는 워크플로를 정의 하는 방법을 알아봅니다.
author: craigshoemaker
ms.topic: conceptual
ms.date: 10/07/2020
ms.author: cshoe
ms.custom: devx-track-csharp, devx-track-python, github-actions-azure
ms.openlocfilehash: 651c1913491952c53af42abec5ce5d5009da06a0
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/26/2020
ms.locfileid: "96168069"
---
# <a name="continuous-delivery-by-using-github-action"></a>GitHub 작업을 사용 하 여 지속적인 배달

[GitHub 작업](https://github.com/features/actions) 을 사용 하 여 Azure 함수 앱에 코드를 자동으로 빌드 및 배포 하는 워크플로를 정의 합니다. 

GitHub 작업에서 [워크플로](https://help.github.com/articles/about-github-actions#workflow) 는 github 리포지토리에서 정의 하는 자동화 된 프로세스입니다. 이 프로세스는 github에서 함수 앱 프로젝트를 빌드하고 배포 하는 방법을 GitHub에 알려 줍니다. 

워크플로는 리포지토리의 `/.github/workflows/` 경로에 있는 YAML(.yml) 파일에서 정의됩니다. 이 정의는 워크플로를 구성하는 다양한 단계와 매개 변수를 포함합니다. 

Azure Functions 워크플로의 경우 파일에는 다음과 같은 세 개의 섹션이 있습니다. 

| 섹션 | 작업 |
| ------- | ----- |
| **인증** | 게시 프로필을 다운로드 합니다.<br/>GitHub 비밀을 만듭니다.|
| **빌드** | 환경을 설정 합니다.<br/>함수 앱을 빌드합니다.|
| **배포** | 함수 앱을 배포 합니다.|

## <a name="prerequisites"></a>사전 요구 사항

- 활성 구독이 있는 Azure 계정. [체험 계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- GitHub 계정. 없는 경우 [평가판](https://github.com/join)에 등록하세요.  
- GitHub 리포지토리를 사용 하 여 Azure에서 호스트 되는 작업 중인 함수 앱입니다.   
    - [빠른 시작: Visual Studio Code를 사용하여 Azure에서 함수 만들기](./create-first-function-vs-code-csharp.md)

## <a name="generate-deployment-credentials"></a>배포 자격 증명 생성

GitHub 작업에 대해 Azure Functions를 사용 하 여 인증 하는 권장 방법은 게시 프로필을 사용 하는 것입니다. 서비스 주체를 사용 하 여 인증할 수도 있습니다. 자세히 알아보려면 [이 GitHub 작업 리포지토리](https://github.com/Azure/functions-action)를 참조 하세요. 

게시 프로필 자격 증명을 [GitHub 암호로](https://docs.github.com/en/actions/reference/encrypted-secrets)저장 한 후 워크플로 내에서이 암호를 사용 하 여 Azure에 인증 합니다. 

#### <a name="download-your-publish-profile"></a>게시 프로필 다운로드

함수 앱의 게시 프로필을 다운로드 하려면 다음을 수행 합니다.

1. 함수 앱의 **개요** 페이지를 선택한 다음 **게시 프로필 가져오기** 를 선택 합니다.

   :::image type="content" source="media/functions-how-to-github-actions/get-publish-profile.png" alt-text="게시 프로필 다운로드":::

1. 파일의 내용을 저장 하 고 복사 합니다.


### <a name="add-the-github-secret"></a>GitHub 암호 추가

1. [GitHub](https://github.com)에서 리포지토리로 이동 하 고 **설정**  >  **비밀**  >  **새 비밀 추가** 를 선택 합니다.

   :::image type="content" source="media/functions-how-to-github-actions/add-secret.png" alt-text="비밀 추가":::

1. 이름으로를 사용 하 여 새 암호를 추가 하 `AZURE_FUNCTIONAPP_PUBLISH_PROFILE` 고 **값** 에 대 한 게시 프로필 파일의 콘텐츠를 추가한 다음 **비밀 추가** 를 선택 합니다. **Name**

이제 GitHub에서 Azure의 함수 앱에 인증할 수 있습니다.

## <a name="create-the-environment"></a>환경 만들기 

환경 설정은 언어별 게시 설정 작업을 사용 하 여 수행 됩니다.

# <a name="net"></a>[.NET](#tab/dotnet)

.NET (ASP.NET 포함)은 작업을 사용 `actions/setup-dotnet` 합니다.  
다음 예에서는 환경을 설정 하는 워크플로의 일부를 보여 줍니다.

```yaml
    - name: Setup DotNet 2.2.402 Environment
      uses: actions/setup-dotnet@v1
      with:
        dotnet-version: 2.2.402
```

# <a name="java"></a>[Java](#tab/java)

Java는 작업을 사용  `actions/setup-java` 합니다.  
다음 예에서는 환경을 설정 하는 워크플로의 일부를 보여 줍니다.

```yaml
    - name: Setup Java 1.8.x
      uses: actions/setup-java@v1
      with:
        # If your pom.xml <maven.compiler.source> version is not in 1.8.x
        # Please change the Java version to match the version in pom.xml <maven.compiler.source>
        java-version: '1.8.x'
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

JavaScript (Node.js)는 작업을 사용 `actions/setup-node` 합니다.  
다음 예에서는 환경을 설정 하는 워크플로의 일부를 보여 줍니다.

```yaml

    - name: Setup Node 12.x Environment
      uses: actions/setup-node@v1
      with:
        node-version: 12.x
```

# <a name="python"></a>[Python](#tab/python)

Python은 작업을 사용 `actions/setup-python` 합니다.  
다음 예에서는 환경을 설정 하는 워크플로의 일부를 보여 줍니다.

```yaml
    - name: Setup Python 3.7 Environment
      uses: actions/setup-python@v1
      with:
        python-version: 3.7
```
---

## <a name="build-the-function-app"></a>함수 앱 빌드

이 섹션은 언어와 Azure Functions에서 지 원하는 언어에 따라 달라 집니다 .이 섹션은 각 언어의 표준 빌드 단계 여야 합니다.

다음 예제에서는 언어와 관련 된 함수 앱을 빌드하는 워크플로의 일부를 보여 줍니다.

# <a name="net"></a>[.NET](#tab/dotnet)

```yaml
    env:
      AZURE_FUNCTIONAPP_PACKAGE_PATH: '.' # set this to the path to your web app project, defaults to the repository root

    - name: 'Resolve Project Dependencies Using Dotnet'
      shell: bash
      run: |
        pushd './${{ env.AZURE_FUNCTIONAPP_PACKAGE_PATH }}'
        dotnet build --configuration Release --output ./output
        popd
```

# <a name="java"></a>[Java](#tab/java)

```yaml
    env:
      POM_XML_DIRECTORY: '.'  # set this to the directory which contains pom.xml file

    - name: 'Restore Project Dependencies Using Mvn'
      shell: bash
      run: |
        pushd './${{ env.POM_XML_DIRECTORY }}'
        mvn clean package
        mvn azure-functions:package
        popd
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```yaml
    env:
      AZURE_FUNCTIONAPP_PACKAGE_PATH: '.'  # set this to the path to your web app project, defaults to the repository root

    - name: 'Resolve Project Dependencies Using Npm'
      shell: bash
      run: |
        pushd './${{ env.AZURE_FUNCTIONAPP_PACKAGE_PATH }}'
        npm install
        npm run build --if-present
        npm run test --if-present
        popd
```

# <a name="python"></a>[Python](#tab/python)

```yaml
    env:
      AZURE_FUNCTIONAPP_PACKAGE_PATH: '.' # set this to the path to your web app project, defaults to the repository root

    - name: 'Resolve Project Dependencies Using Pip'
      shell: bash
      run: |
        pushd './${{ env.AZURE_FUNCTIONAPP_PACKAGE_PATH }}'
        python -m pip install --upgrade pip
        pip install -r requirements.txt --target=".python_packages/lib/site-packages"
        popd
```
---

## <a name="deploy-the-function-app"></a>함수 앱 배포
`Azure/functions-action`함수 앱에 코드를 배포 하려면 작업을 사용 합니다. 이 작업에는 다음과 같은 세 가지 매개 변수가 있습니다.

|매개 변수 |설명  |
|---------|---------|
|_**앱 이름**_ | 강제로 함수 앱의 이름입니다. |
|_**슬롯-이름**_ | 필드 배포 하려는 [배포 슬롯](functions-deployment-slots.md) 의 이름입니다. 슬롯은 함수 앱에 이미 정의 되어 있어야 합니다. |
|_**publish-profile**_ | 필드 게시 프로필에 대 한 GitHub 암호의 이름입니다. |

다음 예제에서는 `functions-action` 인증을 위해 및의 버전 1을 사용 합니다. `publish profile` 

# <a name="net"></a>[.NET](#tab/dotnet)

게시 프로필을 사용 하는 .NET Linux 워크플로를 설정 합니다.

```yaml
name: Deploy DotNet project to Azure function app with a Linux environment

on:
  [push]

env:
  AZURE_FUNCTIONAPP_NAME: your-app-name  # set this to your application's name
  AZURE_FUNCTIONAPP_PACKAGE_PATH: '.'    # set this to the path to your web app project, defaults to the repository root
  DOTNET_VERSION: '2.2.402'              # set this to the dotnet version to use

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
    - name: 'Checkout GitHub Action'
      uses: actions/checkout@master

    - name: Setup DotNet ${{ env.DOTNET_VERSION }} Environment
      uses: actions/setup-dotnet@v1
      with:
        dotnet-version: ${{ env.DOTNET_VERSION }}

    - name: 'Resolve Project Dependencies Using Dotnet'
      shell: bash
      run: |
        pushd './${{ env.AZURE_FUNCTIONAPP_PACKAGE_PATH }}'
        dotnet build --configuration Release --output ./output
        popd
    - name: 'Run Azure Functions Action'
      uses: Azure/functions-action@v1
      id: fa
      with:
        app-name: ${{ env.AZURE_FUNCTIONAPP_NAME }}
        package: '${{ env.AZURE_FUNCTIONAPP_PACKAGE_PATH }}/output'
        publish-profile: ${{ secrets.AZURE_FUNCTIONAPP_PUBLISH_PROFILE }}
```
게시 프로필을 사용 하는 .NET Windows 워크플로를 설정 합니다.

```yaml
name: Deploy DotNet project to Azure function app with a Windows environment

on:
  [push]

env:
  AZURE_FUNCTIONAPP_NAME: your-app-name  # set this to your application's name
  AZURE_FUNCTIONAPP_PACKAGE_PATH: '.'    # set this to the path to your web app project, defaults to the repository root
  DOTNET_VERSION: '2.2.402'              # set this to the dotnet version to use

jobs:
  build-and-deploy:
    runs-on: windows-latest
    steps:
    - name: 'Checkout GitHub Action'
      uses: actions/checkout@master

    - name: Setup DotNet ${{ env.DOTNET_VERSION }} Environment
      uses: actions/setup-dotnet@v1
      with:
        dotnet-version: ${{ env.DOTNET_VERSION }}

    - name: 'Resolve Project Dependencies Using Dotnet'
      shell: pwsh
      run: |
        pushd './${{ env.AZURE_FUNCTIONAPP_PACKAGE_PATH }}'
        dotnet build --configuration Release --output ./output
        popd
    - name: 'Run Azure Functions Action'
      uses: Azure/functions-action@v1
      id: fa
      with:
        app-name: ${{ env.AZURE_FUNCTIONAPP_NAME }}
        package: '${{ env.AZURE_FUNCTIONAPP_PACKAGE_PATH }}/output'
        publish-profile: ${{ secrets.AZURE_FUNCTIONAPP_PUBLISH_PROFILE }}
```

# <a name="java"></a>[Java](#tab/java)

게시 프로필을 사용 하는 Java Linux 워크플로를 설정 합니다.

```yaml
name: Deploy Java project to Azure Function App

on:
  [push]

env:
  AZURE_FUNCTIONAPP_NAME: your-app-name      # set this to your function app name on Azure
  POM_XML_DIRECTORY: '.'                     # set this to the directory which contains pom.xml file
  POM_FUNCTIONAPP_NAME: your-app-name        # set this to the function app name in your local development environment
  JAVA_VERSION: '1.8.x'                      # set this to the dotnet version to use

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
    - name: 'Checkout GitHub Action'
      uses: actions/checkout@master

    - name: Setup Java Sdk ${{ env.JAVA_VERSION }}
      uses: actions/setup-java@v1
      with:
        java-version: ${{ env.JAVA_VERSION }}

    - name: 'Restore Project Dependencies Using Mvn'
      shell: bash
      run: |
        pushd './${{ env.POM_XML_DIRECTORY }}'
        mvn clean package
        mvn azure-functions:package
        popd
    - name: 'Run Azure Functions Action'
      uses: Azure/functions-action@v1
      id: fa
      with:
        app-name: ${{ env.AZURE_FUNCTIONAPP_NAME }}
        package: './${{ env.POM_XML_DIRECTORY }}/target/azure-functions/${{ env.POM_FUNCTIONAPP_NAME }}'
        publish-profile: ${{ secrets.AZURE_FUNCTIONAPP_PUBLISH_PROFILE }}
```

게시 프로필을 사용 하는 Java Windows 워크플로를 설정 합니다.

```yaml
name: Deploy Java project to Azure Function App

on:
  [push]

env:
  AZURE_FUNCTIONAPP_NAME: your-app-name      # set this to your function app name on Azure
  POM_XML_DIRECTORY: '.'                     # set this to the directory which contains pom.xml file
  POM_FUNCTIONAPP_NAME: your-app-name        # set this to the function app name in your local development environment
  JAVA_VERSION: '1.8.x'                      # set this to the java version to use

jobs:
  build-and-deploy:
    runs-on: windows-latest
    steps:
    - name: 'Checkout GitHub Action'
      uses: actions/checkout@master

    - name: Setup Java Sdk ${{ env.JAVA_VERSION }}
      uses: actions/setup-java@v1
      with:
        java-version: ${{ env.JAVA_VERSION }}

    - name: 'Restore Project Dependencies Using Mvn'
      shell: pwsh
      run: |
        pushd './${{ env.POM_XML_DIRECTORY }}'
        mvn clean package
        mvn azure-functions:package
        popd
    - name: 'Run Azure Functions Action'
      uses: Azure/functions-action@v1
      id: fa
      with:
        app-name: ${{ env.AZURE_FUNCTIONAPP_NAME }}
        package: './${{ env.POM_XML_DIRECTORY }}/target/azure-functions/${{ env.POM_FUNCTIONAPP_NAME }}'
        publish-profile: ${{ secrets.AZURE_FUNCTIONAPP_PUBLISH_PROFILE }}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

게시 프로필을 사용 하는 Node.JS Linux 워크플로를 설정 합니다.

```yaml
name: Deploy Node.js project to Azure Function App

on:
  [push]

env:
  AZURE_FUNCTIONAPP_NAME: your-app-name    # set this to your application's name
  AZURE_FUNCTIONAPP_PACKAGE_PATH: '.'      # set this to the path to your web app project, defaults to the repository root
  NODE_VERSION: '12.x'                     # set this to the node version to use (supports 8.x, 10.x, 12.x)

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
    - name: 'Checkout GitHub Action'
      uses: actions/checkout@master

    - name: Setup Node ${{ env.NODE_VERSION }} Environment
      uses: actions/setup-node@v1
      with:
        node-version: ${{ env.NODE_VERSION }}

    - name: 'Resolve Project Dependencies Using Npm'
      shell: bash
      run: |
        pushd './${{ env.AZURE_FUNCTIONAPP_PACKAGE_PATH }}'
        npm install
        npm run build --if-present
        npm run test --if-present
        popd
    - name: 'Run Azure Functions Action'
      uses: Azure/functions-action@v1
      id: fa
      with:
        app-name: ${{ env.AZURE_FUNCTIONAPP_NAME }}
        package: ${{ env.AZURE_FUNCTIONAPP_PACKAGE_PATH }}
        publish-profile: ${{ secrets.AZURE_FUNCTIONAPP_PUBLISH_PROFILE }}
```

게시 프로필을 사용 하는 Node.JS Windows 워크플로를 설정 합니다.

```yaml
name: Deploy Node.js project to Azure Function App

on:
  [push]

env:
  AZURE_FUNCTIONAPP_NAME: your-app-name    # set this to your application's name
  AZURE_FUNCTIONAPP_PACKAGE_PATH: '.'      # set this to the path to your web app project, defaults to the repository root
  NODE_VERSION: '10.x'                     # set this to the node version to use (supports 8.x, 10.x, 12.x)

jobs:
  build-and-deploy:
    runs-on: windows-latest
    steps:
    - name: 'Checkout GitHub Action'
      uses: actions/checkout@master

    - name: Setup Node ${{ env.NODE_VERSION }} Environment
      uses: actions/setup-node@v1
      with:
        node-version: ${{ env.NODE_VERSION }}

    - name: 'Resolve Project Dependencies Using Npm'
      shell: pwsh
      run: |
        pushd './${{ env.AZURE_FUNCTIONAPP_PACKAGE_PATH }}'
        npm install
        npm run build --if-present
        npm run test --if-present
        popd
    - name: 'Run Azure Functions Action'
      uses: Azure/functions-action@v1
      id: fa
      with:
        app-name: ${{ env.AZURE_FUNCTIONAPP_NAME }}
        package: ${{ env.AZURE_FUNCTIONAPP_PACKAGE_PATH }}
        publish-profile: ${{ secrets.AZURE_FUNCTIONAPP_PUBLISH_PROFILE }}

```
# <a name="python"></a>[Python](#tab/python)

게시 프로필을 사용 하는 Python Linux 워크플로를 설정 합니다.

```yaml
name: Deploy Python project to Azure Function App

on:
  [push]

env:
  AZURE_FUNCTIONAPP_NAME: your-app-name # set this to your application's name
  AZURE_FUNCTIONAPP_PACKAGE_PATH: '.'   # set this to the path to your web app project, defaults to the repository root
  PYTHON_VERSION: '3.7'                 # set this to the python version to use (supports 3.6, 3.7, 3.8)

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
    - name: 'Checkout GitHub Action'
      uses: actions/checkout@master

    - name: Setup Python ${{ env.PYTHON_VERSION }} Environment
      uses: actions/setup-python@v1
      with:
        python-version: ${{ env.PYTHON_VERSION }}

    - name: 'Resolve Project Dependencies Using Pip'
      shell: bash
      run: |
        pushd './${{ env.AZURE_FUNCTIONAPP_PACKAGE_PATH }}'
        python -m pip install --upgrade pip
        pip install -r requirements.txt --target=".python_packages/lib/site-packages"
        popd
    - name: 'Run Azure Functions Action'
      uses: Azure/functions-action@v1
      id: fa
      with:
        app-name: ${{ env.AZURE_FUNCTIONAPP_NAME }}
        package: ${{ env.AZURE_FUNCTIONAPP_PACKAGE_PATH }}
        publish-profile: ${{ secrets.AZURE_FUNCTIONAPP_PUBLISH_PROFILE }}
```

---

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Azure 및 GitHub 통합에 대 한 자세한 정보](/azure/developer/github/)