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
- Jobs
- Actions
- Runners

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

2. AWS가 아닌 클라우드 스토리지

대표적인 클라우드 업체인 MS의 Azure와 Google의 GCP 또한 스토리지 서비스를 제공한다

- Azure Blob Storage
- Google Cloud Storage

#### CloudFront와 CDN

#### 캐시 무효화(Cache Invalidation)

#### Repository secret과 환경변수
