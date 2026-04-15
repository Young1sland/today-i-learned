# Passkey

## 용어

- 인증기(Authenticator)
실제로 인증 작업을 수행하고 credential을 보관하거나 서명하는 주체
- Relying Party
우리 서비스 서버. passkey를 등록해주고 검증하는 쪽
- resident/discoverable credential
credential 자체에 사용자 식별 정보가 인증기 쪽에 함께 저장되어, 나중에 서버가 credential_id 목록을 미리 안 줘도 인증기가 어떤 계정의 passkey인지 스스로 고를 수 있는 형태
일반적인 passkey UX에 맞음
- non-resident credential
인증기 쪽에는 계정을 찾기 위한 충분한 정보가 없고, 서버가 먼저  credential_id를 지정해줘야 함
예전 U2F/FIDO 보안키 쪽 흐름에 더 가까움
- aaguid: 영어로 Authenticator Attestation GUID. 어떤 종류의 인증기(authenticator)인지 식별
- transports
  - credential이 어떤 transport로 사용될 수 있는지 나타내는 정보
  - 휴대폰/플랫폼 내부 passkey면 internal
  - 외장 보안키면 usb, nfc, ble
  - 크로스디바이스 성격이면 hybrid
- signcount
  - WebAuthn 응답의 authenticatorData 안에 포함된 4바이트 카운터
  - WebAuthn 스펙상 구현되지 않을 수도 있음.
  - 이전 값과 새 값이 모두 0이면 정상. 새값이 이전값보다 작으면 위험신호로 판단하도록 사용할 수 있음.
- 