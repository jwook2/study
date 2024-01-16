# 젠킨스 Job DSL

{% embed url="https://plugins.jenkins.io/job-dsl" %}

## 1. Job DSL

Job DSL은  젠킨스의 플러그인입니다.

DSL 은 Domain Specific Language(도메인 특화 언어) 의 약어입니다.

Groovy 기반 언어를 사용하여 젠킨스에서 작업을 작성 할 수 있습니다.

Groovy 를 자바 플랫폼용 스크립트언어라고 생각하면 됩니다.

## 2. 예제

### example (npm install)

NodeJS 프로젝트의 종속성을 설치하기 위한 npm 설치

```
job('NodeJS example') {
    scm {
        git('https://github.com/wardviaene/docker-demo.git') {  node -> 
            node / gitConfigName('DSL User')
            node / gitConfigEmail('jenkins-dsl@newtech.academy')
        }
    }
    triggers {
        scm('H/5 * * * *')
    }
    wrappers {
        nodejs('nodejs') // this is the name of the NodeJS installation in 
                         // Manage Jenkins -> Configure Tools -> NodeJS Installations -> Name
    }
    steps {
        shell("npm install")
    }
}
```

작업(job)에 이어 작업에 이름이 있습니다.

다음으로는 이 작업의 모든 매개변수를 정의합니다.



scm : 버전관리, 즉, 소프트웨어 구성 관리

```
git('https://github.com/wardviaene/docker-demo.git')
```

* 작업에 사용될 깃 저장소 입니다.

```
node / gitConfigName('DSL User')
node / gitConfigEmail('jenkins-dsl@newtech.academy')
```

* 우리가 사용할 이 깃 저장소의 매개변수는 이름과 이메일 주소를 구성하며 이 젠킨스에서 깃 클라이언트를 구성하는데 사용됩니다.
* 이름을 'DSL User' 이메일을 'jenkins-dsl@newtech.academy' 로 설정했다.
* 이 매개변수들을 설정하지 않으면 서버(droplet) 가 실패하고 깃 이름과 깃 이메일이 구성되지 않았다고 표시될것입니다.



triggers : 몇번이나 구축할 것인가

* scm 을 5분마다 가져오도록&#x20;

wrappers : nodejs 가 필요하다고 정의

* wrapper 를 정의하지 않으면 커맨드노드 또는 커맨드 npm 을 사용할 수 없습니다.
  * 따라서 nodejs 를 입력한 다음 nodejs 설치 이름을 입력하면 된다

steps : 깃허브 프로젝트 를 복제하고 나면 단계 를 정의하고 shell 단계를 수행한다.
