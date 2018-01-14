#### django iamport 연동

- jquery < 1.대 이상

- iamport id, 결제 id 두개 존재(rsp.imp_uid, IMP.rqsuest_pay.merchant_uid) - pdf 1/13p

- rest-api를 통한 결제 관리 지원

- 결제 정보찾기, 가격 확인, 취소, 비인증 결제(허가 필요), 정기 예약 결제

- 결제 프로세스 상 금액 정보가 사용자에 의해 변경될 수 있기 때문에 방어적으로 코딩이 필요함

- admin.iamport.kr 에서 아임포트 관리자 체험 가능

- admin.iamport.kr에서 이메일정보로만 회원가입도 가능하다

- 회원가입 - 로그인 후 시스템 설정에 가면 정보확인 가능

  - 가맹점 식별 코드 - uid
  - 결제확인, 결제취소 키: REST API키, REST API secret 활용

- 시스템설정 - pg설정에서 pg 선택 가능

  - 이니시스 선택시 테스트 모드를 해제하면 이니시스와 계약이 필요함

  ​