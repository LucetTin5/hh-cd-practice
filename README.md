# Frontend CI/CD Pipeline Practice

## Introduction

![ci/cd pipeline image](<CD Practice.png>)

GitHub Actions 워크플로우를 작성해 다음과 같이 배포가 진행되도록 함

1. 저장소를 체크아웃합니다.
2. Node.js 20.x 버전을 설정합니다.
3. 프로젝트 의존성을 설치합니다.
4. Next.js 프로젝트를 빌드합니다.
5. AWS 자격 증명을 구성합니다.
6. 빌드된 파일을 S3 버킷에 동기화합니다.
7. CloudFront 캐시를 무효화합니다.

### 주요 링크

- S3 버킷 웹사이트 엔드포인트: [S3_Bucket](http://alchive-bucket.s3-website.ap-northeast-2.amazonaws.com/)
- Cloudfront 배포 도메인 이름: [CloudFront](https://do9ouuzervqcl.cloudfront.net)
- 배포 도메인: [CD-practice](https://hhplus-cd-practice.kro.kr)

### 주요 개념

#### GitHub Action과 CI/CD 도구

1. What is Github Actions?

> GitHub Actions is a continuous integration and continuous delivery (CI/CD) platform that allows you to automate your build, test, and deployment pipeline.

**Linux**, **Windows**, **maxOS** 가상환경을 통해 빌드, 테스트, 배포 등의 파이프라인을 진행할 수 있게 해주는 깃허브가 제공하는 도구

2. Components

- Workflows
  - 하나 혹은 그 이상의 job을 실행하기 위해 구성하는 자동화된 프로세스를 의미
  - YAML 포맷으로 설정되며, 이벤트에 의해, 직접, 스케쥴링에 의해 트리거될 수 있다
  - `.github/workflows`에 작성해야 한다
- Events
  - Workflow를 트리거하는 특정 활동이나 규칙을 의미
  - `pull request`, `push`, `특정 브랜치로의 커밋` 등
  - 스케쥴링에 의해서 트리거될수도, 외부 트리거로 인해 발생할수도 있음
- Jobs
  - 동일한 러너에서 실행되는 `step`들의 집합
  - 기본적으로 워크플로위 내의 여러 job은 병렬로 실행됨
    - 동시성 제한(`concurrency`), 환경 제한(production/staging 등), 러너 가용성(러너의 수가 제한되는 겨웅 등), 리소스 제한(깃허브 플랜에 의해 제한이 발생할 경우), 시간 제한(최대 실행 시간 제한이 있다면), 조건부 실행(if 조건을 사용하여 success, failure 등) 등으로 제한을 걸 수 있음
  - 각 job은 독립적으로 실행되나, 필요에 따라 job 간의 의존성을 설정 가능
- Actions
  - GitHub Actions 플랫폼에서 자주 반복되는 작업을 수행하기 위한 커스텀 앱
  - 액션을 사용하면 워크플로에 작성될 반복 작업을 줄일 수 있음
- Runners
  - 워크플로우가 실행되는 서버
  - 각 러너는 한 번에 하나의 job을 실행
  - 기본적으로는 GitHub에서 호스팅하는 러너를 사용하나, 직접 호스팅하는 러너를 사용할 수 있음
  - Ubuntu Linux, MS Windows, macOS 환경이 제공됨

3. Alternatives

- [Jenkins](https://www.jenkins.io/)
- [Travis CI](https://www.travis-ci.com/)
- [Circle CI](https://circleci.com/)
- [GitLab](https://about.gitlab.com/)

#### S3와 스토리지

1. AWS S3?

데이터를 버킷 내의 객체로 저장하는 `객체 스토리지 서비스`
여기서 객체는 해당 파일을 설명하는 모든 메타데이터이며, 버킷은 객체의 컨테이너를 의미한다

S3에 데이터를 저장하려면 버킷의 이름과 함께 적합한 AWS 리전에 버킷을 생성해야 한다.
업로드되는 데이터들은 고유 키를 가지며 이는 버킷 내 객체의 고유 식별자가 된다.

정적 웹사이트 호스팅, 백업 및 복구용 스토리지, 콘텐츠 배포, 데이터 아카이빙 등 다양한 용도의 데이터 호스팅을 위해 사용됨

2. AWS가 아닌 클라우드 스토리지

대표적인 클라우드 업체인 MS의 Azure와 Google의 GCP 또한 스토리지 서비스를 제공한다.
IBM, Wasabi, Backblaze 등 다양한 스토리지 서비스 또한 존재한다.

- Azure Blob Storage
- Google Cloud Storage

#### CloudFront와 CDN

1. AWS CloudFront?

AWS에서 제공하는 콘텐츠 전송 네트워크(CDN) 서비스

- 분산 엣지 로케이션을 통해 콘텐츠를 캐싱하고 제공
- static/dynamic 콘텐츠, 스트리밍 비디오를 빠르게 전달할 수 있음
- AWS의 다른 서비스들과 쉽게 통합이 가능
- https를 통한 보안 컨텐츠 전송을 지원

2. CDN(Content Delivery Network)

CDN은 지리적으로 분산된 서버 네트워크를 이용하여 웹 컨텐츠를 최종 사용자와 가까운 위치에서 전달하기 위한 시스템

- 캐싱된 데이터라면 콘텐츠의 전달 속도가 향상됨
- 원본 서버의 부하를 줄일 수 있음
- 분산 처리를 통해 대규모 트래픽 처리 능력이 향상
- CDN 공급자의 네트워크에 따라 전 세계 사용자에게 일관된 성능을 제공할 수 있음

사용자가 웹사이트에 접속 -> CDN이 사용자에게 가장 가까운 엣지 서버로 요청을 라우팅함 -> 엣지 서버에 콘텐츠가 있다면 캐싱데이터를, 없다면 원본 서버에서 가져와 캐싱 후에 전달

3. vs Cloudflare

- DDoS 보호, 웹앱 방화벽 등 보안 기능을 제공
- 그 외에도 DNS, 로드 밸런싱, 캡챠 등 여러 기능 제공
- AWS 생태계를 주로 사용한다면 CloudFront가 통합에 유리할 수 있음
- Cloudflare는 무료 티어와 함께 정액제 요금 구조 제공

#### 캐시 무효화(Cache Invalidation)

1. 캐시 무효화란?

캐시 무효화는 CDN이나 캐싱 시스템에 저장된 컨텐츠를 강제로 새로고침하거나 제거하는 프로세스를 의미, 이는 원본이 업데이트되었을 때 오래된 버전이 사용자에게 제공되는 것을 방지합니다

2. 방법?

- 전체 무효화: 특정 URL, 전체 사이트 캐시를 모두 지우기
- 부분 무효화: 특정 객체나 파일만 선택적으로 무효화하기
- 버전 관리: URL에 버전 정보를 포함시켜 자동으로 새 버전을 가져오게

3. CloudFront에서는 어떻게?

- CloudFront 콘솔, API, AWS CLI를 통해 무효화 요청이 가능
- 몇 분 내로 무효화는 완료되나, 전체 네트워크로 전파될 때 시간이 걸릴 수 있음
- 무효화는 어느 정도까지는 비용이 없으나, 이후에는 비용이 발생 -> 따라서, 무효화 비용과 한도를 고려한 무효화가 필요

4. 고려사항

- TTL(Time To Live) 설정: 컨텐츠 특성에 맞게 캐시 유효 기간을 설정해야 함
- Cache-Control 헤더: 서버에서 클라이언트로, 해당 리소스를 어떻게 캐싱해야할지 지시
  - 캐시 가능 여부 지정
  - 캐시 유효 기간 설정: max-age(클라이언트 캐시 유효 기간), s-maxage(CDN 최대 유효 기간) 등
  - 캐시 재검증 정책 설정
  - 캐시 공유 가능 여부 짖정
- 개인 정보가 포함된 페이지는 `Cache-Control: private, no-store, max-age=0`과 같이 설정하여 캐싱을 하지 않도록 할 수 있음

#### Repository secret과 환경변수

1. Repository Secrets?

GitHub 저장소에 저장되는 암호화된 환경 변수
주로 CI/CD 파이프라인에서 사용되며, 민감한 정보를 코드단에 노출시키지 않고 사용할 수 있게 해줌
저장소 설정에서 관리되며, 값을 직접 볼 수 없고, 한번 저장하면 다시 볼 수 없음

2. 환경변수(Environment Variables)

OS 레벨에서 정의되는 동적 값들의 집합
App이나 process가 실행될 때 참조할 값들을 저장

- process의 환경에 따라 다르게 설정 가능
- app 설정, API Key, DB 연결 문자열 등을 저장
- code 변경 없이 prod, test, dev 등 다양한 환경에서 app의 동작을 변경 가능
