client_credentials 방식으로 Power BI API 토큰 요청

curl -X POST "https://login.microsoftonline.com/{tenant_id}/oauth2/v2.0/authorize" \
    -H "Content-Type: application/x-www-form-urlencoded" \
    -d "client_id={client_id}" \
    -d "response_type=code" \
    -d "redirect_uri={redirect_uri}" \
    -d "scope=https://analysis.windows.net/powerbi/api/.default" \
    -d "response_mode=query"
