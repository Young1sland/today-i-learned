### Credentials
AWS는 정책은 크게 자격 증명 기반 정책, 리소스 기반 정책으로 나뉜다.
- Identitiy-Based Policy : 주체가 할 수 있는 행동 정의
- Resource-Based Policy : 어떤 리소스를 대상으로 누가 무엇을 할 수 있는지 정의
1. CR-01 : 루트 사용자 API 키 비활성화.
2. CR-02 : 다요소 인증 활성화(Multi Factor Authentication)
3. CR-03: 최소 권한의 원칙 적용
4. CR-04 : MFA 강제 정책 도입


### Data Security (S3)
1. DS-S3-01 : 퍼블릭 액세스 최소화. 버킷에 모든 퍼블릭 액세스 차단 설정 제공
2. DS-S3-02 : 버킷 버전 관리 활성화. 데이터 손실 시 복구 지점으로 사용
3. DS-S3-03 : 버킷 삭제 MFA 활성화
    - 버킷 버전 관리 상태 변경, 특정 버전 삭제하려고 하면 추가 인증 요구
    - 버전 관리 활성화하면 객체를 삭제해도 실제 삭제되지 않고 마커만 부착 됨. 실제 삭제할려면 버젼 삭제 필요. 이 때 추가 인증 요구
    - AWS CLI, SDK, S3 Rest API로 활성화 가능
        ```
        터미널에 다음 명령어를 입력하여 액세스키와 비밀키를 프로필에 저장합니다.
        $ aws configure --profile <profile-name>

        프로필 등록이 제대로 되었다면, 다음 명령어를 입력하여 등록된 MFA 장치 정보가 제대로 출력되는지 확인합니다.
        $ aws iam list-virtual-mfa-devices --profile <profile-name>

        다음 명령어를 입력하여 MFADelete를 활성화합니다.
        $ aws s3api put-bucket-versioning --profile <profile-name> --bucket <bucket-name> --versioning-configuration Status=Enabled,MFADelete=Enabled --mfa "<mfa-serial-number> <OTP digits>”
        ```
4. DS-S3-04 : 유휴 데이터 암호화
    - 서버 측 암호화, 클라이언트 측 암호화 방법 있음.
    - 서버 측 암호화하면 AWS가 데이터 암호화해서 저장하고 조회 시 복호화해서 보여줌. 사용자는 인식하지 못함.
5. DS-S3-05 : CSP에 버킷 이름으로 와일드카드 사용 금지
    ```
    Figure 7. 올바른 CSP의 예시
    Content-Security-Policy: default-src 'self' dreamhack-public-imgs.s3.ap-northeast-2.amazonaws.com dreamhack-public-js.s3.ap-northesat-2.amazonaws.com
    
    Figure 8. 올바르지 않은 CSP의 예시
    Content-Security-Policy: default-src 'self' *.s3.ap-northeast-2.amazonaws.com    
    ```
6. DS-S3-06 : Cloudtrail로 객체 수준 API의 사용 로그 기록

### NETWORK
1. NET-01 : 라우팅 테이블을 활용한 망분리
2. NET-02 : 보안 그룹을 활용한 접근 제어
3. NET-03 : ACL을 활용한 접근 제어
4. NET-04 : 플로우 로그 수집

### Monitoring $ Inspection
1. CloudTrail : 사용자의 활동 추적하여 로그 생성하는 서비스
2. CloudWatch : 애플리케이션 관리자에게 다양한 정보를 시작화해서 제시. 시스템 성능 변화에 실시간으로 대응할 수 있는 솔루션 제공하는 모니터링 서비스
3. Inspector : EC2 인스턴스에 사용할 수 있는 보안성 평가 도구. 인스턴스의 네트워크 접근성, CVE 유무, 보안 설정 등을 평가하고, 취약점 식별하여 감사 보고서 생성.