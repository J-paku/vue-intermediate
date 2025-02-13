curl -X POST https://login.microsoftonline.com/{tenant_id}/oauth2/v2.0/token \
  -H "Content-Type: application/x-www-form-urlencoded" \
  -d "grant_type=device_code" \
  -d "client_id={client_id}" \
  -d "device_code={device_code}" \
  -d "scope=https://api.powerbi.com/.default"

curl -X POST https://login.microsoftonline.com/{tenant_id}/oauth2/v2.0/token \
  -H "Content-Type: application/x-www-form-urlencoded" \
  -d "grant_type=device_code" \
  -d "client_id={client_id}" \
  -d "device_code={device_code}" \
  -d "scope=https://api.powerbi.com/.default"

4️⃣ 액세스 토큰이 Power BI API 범위를 포함하지 않는 경우

현재 사용하는 액세스 토큰이 Power BI API에 대한 권한을 포함하지 않는 경우, API 호출이 실패할 수 있음.

✅ 해결 방법: Power BI API 토큰 범위 확인

(1) 현재 토큰의 범위 확인

curl -X GET "https://graph.microsoft.com/v1.0/me" \
  -H "Authorization: Bearer {access_token}" \
  -H "Content-Type: application/json"

✔ 이 응답에서 scopes 항목에 Power BI 관련 API 권한이 포함되어 있는지 확인.

(2) 토큰 발급 시 Power BI API 범위를 명확하게 지정

OAuth 2.0 인증 시, Power BI API 범위를 명확히 지정하여 토큰을 다시 발급해야 함.

curl -X POST https://login.microsoftonline.com/common/oauth2/v2.0/token \
  -H "Content-Type: application/x-www-form-urlencoded" \
  -d "grant_type=device_code" \
  -d "client_id={client_id}" \
  -d "device_code={device_code}" \
  -d "scope=https://graph.microsoft.com/.default https://api.powerbi.com/.default"

✔ https://api.powerbi.com/.default 범위가 포함되어 있어야 함.


세ㄱ섹

403 Forbidden 에러가 발생하는 원인과 해결 방법을 하나씩 정리해 줄게.

🚀 403 Forbidden 에러 원인 및 해결 방법

1️⃣ API 권한(OAuth Scope) 부족

현재 발급받은 액세스 토큰이 Power BI API를 호출할 수 있는 충분한 권한을 포함하고 있지 않을 가능성이 있음.

✅ 해결 방법: 액세스 토큰의 권한 확인

현재 사용 중인 access_token이 올바른 범위를 가지고 있는지 확인해야 함.

curl -X GET "https://graph.microsoft.com/v1.0/me" \
  -H "Authorization: Bearer {access_token}" \
  -H "Content-Type: application/json"

✔ 정상적으로 응답이 오면 현재 토큰이 Microsoft Graph API에 접근할 수 있다는 의미.
✔ 하지만 Power BI API(https://api.powerbi.com/.default)가 포함되지 않았을 가능성이 있음.

2️⃣ Power BI API 범위를 포함한 새 토큰 발급

기존 토큰이 Power BI API에 대한 범위를 포함하고 있는지 확인하고, 없다면 다시 발급해야 함.

✅ 올바른 토큰을 받기 위한 명령어

curl -X POST https://login.microsoftonline.com/{tenant_id}/oauth2/v2.0/token \
  -H "Content-Type: application/x-www-form-urlencoded" \
  -d "grant_type=device_code" \
  -d "client_id={client_id}" \
  -d "device_code={device_code}" \
  -d "scope=https://graph.microsoft.com/.default https://api.powerbi.com/.default"

✔ 기존 토큰에 https://api.powerbi.com/.default 범위를 포함했는지 확인!
✔ 새 토큰으로 다시 API 호출해보기.

3️⃣ Power BI 테넌트 관리자 설정에서 API 호출이 차단된 경우

✅ 조직의 Power BI 테넌트 관리자가 API 호출을 차단했을 가능성
	1.	“Power BI 서비스 관리자 설정”에서 API 호출이 허용되어 있는지 확인 필요
	2.	조직 내에서 Power BI API 호출이 특정 사용자 또는 보안 그룹에만 허용되었는지 확인 필요

🚨 테넌트 관리자에게 문의해야 할 사항:
	•	Power BI REST API 접근이 차단되었는지 확인 요청
	•	내 계정이 Power BI API를 사용할 수 있는 보안 그룹에 포함되었는지 확인 요청

👉 테넌트 관리자가 차단한 경우, API를 사용할 수 없기 때문에 관리자 승인 필요.

4️⃣ Power BI 라이선스 부족

Power BI API를 사용하려면 Power BI Pro 또는 Premium 라이선스가 필요함.
🚨 라이선스가 없으면 API 접근 자체가 차단됨!

✅ 내 계정이 Power BI Pro 또는 Premium 라이선스를 가지고 있는지 확인
	1.	Power BI 웹사이트에 로그인
	2.	우측 상단 “내 계정” 클릭
	3.	Power BI Pro 또는 Premium 라이선스가 있는지 확인

✔ 라이선스가 없다면, Power BI API는 403 Forbidden 에러를 반환할 가능성이 높음.
✔ 관리자에게 라이선스 할당 요청 필요.

5️⃣ Power BI API가 특정 보안 그룹에만 허용된 경우

🚨 조직의 보안 정책에 따라 Power BI API 사용이 특정 보안 그룹에 속한 사용자만 가능할 수도 있음.
🚨 403 Forbidden이 발생하는 경우, 내 계정이 API를 호출할 수 있는 보안 그룹에 속해 있는지 확인해야 함.

✅ 확인 방법
	1.	Power BI 관리자에게 문의하여 API 호출이 특정 보안 그룹에만 허용되어 있는지 확인
	2.	내 계정이 해당 보안 그룹에 포함되지 않은 경우, 관리자에게 추가 요청

🚀 최종 결론

403 Forbidden 오류가 발생하는 주요 원인과 해결 방법
1️⃣ 액세스 토큰에 Power BI API 범위(https://api.powerbi.com/.default)가 포함되지 않았을 가능성
→ 새 토큰 발급해서 다시 테스트
2️⃣ Power BI 테넌트 관리자 설정에서 API 호출이 차단되었을 가능성
→ 관리자에게 Power BI API 사용 가능 여부 확인 요청
3️⃣ Power BI 라이선스(Pro 또는 Premium) 부족
→ 내 계정이 Power BI Pro 라이선스가 있는지 확인
4️⃣ Power BI API가 특정 보안 그룹에만 허용된 경우
→ 관리자에게 내 계정이 Power BI API를 사용할 수 있는 보안 그룹에 포함되어 있는지 확인 요청

🚀 위 4가지 중 하나가 문제일 가능성이 큼.
🚀 관리자 승인 없이는 해결할 수 없는 경우도 있으니, 관리자에게 확인이 필요할 수도 있음.