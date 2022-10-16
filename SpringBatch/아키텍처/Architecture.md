# Spring Batch Architecture

## 스프링 배치 탄생 배경
- 배치 처리에서 요구하는 재사용 가능한 자바 기반 배치 아키텍쳐 표준 필요성 대두
- https://docs.spring.io/spring-batch/docs/current/reference/html/spring-batch-intro.html

## 배치 핵심 패턴
  - Read : 데이터베이스,파일,큐에서 다량의 데이터 조회한다.
  - Process : 특정 방법으로 데이터를 가공한다.
  - Write : 데이터를 수정된 양식으로 다시 저장한다.
  * 이 개념은 Database의 ETL개념과 거의 동일.  
    E(Extract), T(Transform), L(Load)

## 배치 시나리오
  - 배치 프로세스를 주기적으로 커밋
  - 동시 다발적인 Job의 배치 처리, 대용량 병렬 처리
  - 실패 후 수동 또는 스케줄링에 의한 재시작
  - 의존관계가 있는 step 여러개를 순차적으로 처리
  - 조건적 Flow 구성을 통한 체계적이고 유연한 배치 모델 구성
  - 반복, 재시도, Skip 처리

## 아키텍쳐
  - Application
    - 스프링 배치 프레임워크를 통해 개발자가 만든 모든 배치 Job과 커스텀 코드를 포함
    - 개발자는 업무로직의 구현에만 집중하고 공통적인 기반기술은 프레임웍이 담당하게 함
  - Batch Core
    - Job을 실행, 모니터링, 관리하는 API로 구성되어 있다.
    - JobLauncher, Job, Step, Flow 등이 속한다.
  -  Batch Infrastructure
    - Application, Core 모두 공통 Infrastructure 위에서 빌드
    - Job 실행의 흐름과 처리를 위한 틀 제공
    - Reader, Processor Writer, Skip, Retry 등이 속한다.

