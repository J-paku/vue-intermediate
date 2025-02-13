client_credentials 방식으로 Power BI API 토큰 요청

curl -X POST "https://login.microsoftonline.com/{tenant_id}/oauth2/v2.0/token" \
    -H "Content-Type: application/x-www-form-urlencoded" \
    -d "grant_type=client_credentials" \
    -d "client_id={client_id}" \
    -d "client_secret={client_secret}" \
    -d "scope=https://analysis.windows.net/powerbi/api/.default"
