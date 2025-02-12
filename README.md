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