📌 1️⃣ 사용자 인증을 위한 URL 열기
사용자가 로그인하여 인증할 수 있도록 아래 URL에 접속합니다.


https://login.microsoftonline.com/{tenant_id}/oauth2/v2.0/authorize?
client_id={client_id}
&response_type=code
&redirect_uri={redirect_uri}
&scope=https://analysis.windows.net/powerbi/api/.default
&response_mode=query
로그인 후, redirect_uri에 ?code=xxxxx 값이 포함된 URL이 반환됨
📌 2️⃣ 반환된 authorization_code로 토큰 요청

curl -X POST https://login.microsoftonline.com/{tenant_id}/oauth2/v2.0/token \
    -H "Content-Type: application/x-www-form-urlencoded" \
    -d "grant_type=authorization_code" \
    -d "client_id={client_id}" \
    -d "client_secret={client_secret}" \
    -d "code={authorization_code}" \
    -d "redirect_uri={redirect_uri}" \
    -d "scope=https://analysis.windows.net/powerbi/api/.default"
📌 결과:

scp 필드에 Workspace.Read.All / Report.Read.All 같은 Power BI API 권한이 포함되는지 확인
✅ Power BI API 권한이 포함되었다면?
→ device_code 방식에서는 Power BI API가 정상적으로 작동하지 않는 것이므로,
authorization_code 방식으로 사용하는 것이 해결책.

✅ 그래도 scp가 안 나오면?
→ Application Permissions 문제일 가능성이 높음 → 3번 진행6


http://localhost:3000/callback

