내 작업 영역(My Workspace)에서의 권한을 확인하는 방법은 Power BI REST API를 통해 가능합니다.

✅ 1. 내 작업 영역의 권한 확인 (me/permissions API)

내 작업 영역에서 현재 계정이 가진 권한을 조회하려면 다음 API를 호출하면 됩니다.

curl -X GET "https://api.powerbi.com/v1.0/myorg/me/permissions" \
  -H "Authorization: Bearer {access_token}" \
  -H "Content-Type: application/json"

🔹 응답 예시

{
  "value": [
    {
      "name": "View",
      "hasPermission": true
    },
    {
      "name": "Create",
      "hasPermission": true
    },
    {
      "name": "Delete",
      "hasPermission": false
    },
    {
      "name": "Share",
      "hasPermission": true
    }
  ]
}

	•	"View": true → 조회(읽기) 권한 있음 (Report.Read.All이 정상 작동하는지 확인 가능)
	•	"Create": true → 새 보고서 생성 가능
	•	"Delete": false → 보고서 삭제 불가능
	•	"Share": true → 공유 가능

✅ 이 응답을 확인하면 현재 계정이 “내 작업 영역”에서 가질 수 있는 권한을 알 수 있음.

✅ 2. 현재 계정의 모든 권한 확인 (me API)

현재 로그인한 계정의 전반적인 정보 및 권한을 조회하려면 Microsoft Graph API 또는 Power BI API를 호출할 수도 있습니다.

curl -X GET "https://graph.microsoft.com/v1.0/me" \
  -H "Authorization: Bearer {access_token}" \
  -H "Content-Type: application/json"

✔ 이 API를 호출하면, 현재 로그인한 사용자 계정(Azure AD 계정)의 정보가 반환됨.
✔ 조직에서 부여된 권한(Azure AD 설정 확인용)도 함께 볼 수 있음.

✅ 3. 내 작업 영역의 보고서 목록 확인 (reports API)

현재 계정이 “내 작업 영역”에서 접근 가능한 보고서가 있는지 확인하려면:

curl -X GET "https://api.powerbi.com/v1.0/myorg/reports" \
  -H "Authorization: Bearer {access_token}" \
  -H "Content-Type: application/json"

✔ 응답이 빈 배열([])이면, 내 작업 영역에 보고서가 없거나, Report.Read.All 권한이 없는 것일 가능성이 높음.

✅ 결론
	•	내 작업 영역(My Workspace)에서의 권한을 확인하려면 me/permissions API 사용.
	•	현재 로그인한 계정의 권한 확인은 graph.microsoft.com/v1.0/me API를 활용.
	•	보고서 목록을 조회하려면 reports API를 호출하여 실제 접근 가능 여부 확인.

👉 위 명령어를 차례로 실행하면 현재 계정이 내 작업 영역에서 어떤 권한을 가졌는지 확실하게 확인할 수 있음! 🚀


섹섹보
curl -X GET "https://graph.microsoft.com/v1.0/me" \
  -H "Authorization: Bearer {access_token}" \
  -H "Content-Type: application/json"

Graph API의 로그인 정보를 사용하여 Power BI에 접근할 수 있는지 확인해 보겠습니다.

가능 여부

Graph API는 Microsoft의 여러 서비스(Outlook, OneDrive, Teams 등)와 통합되지만, Power BI에 대한 직접적인 데이터 액세스 권한을 제공하지 않습니다.
Power BI에 접근하려면 Power BI REST API를 사용해야 하며, 이 API는 Azure AD의 OAuth 인증을 필요로 합니다.

즉, Microsoft Graph API의 로그인 정보를 직접 활용해서 Power BI 데이터에 접근할 수는 없습니다.
하지만, Graph API를 통해 획득한 Access Token을 Power BI REST API에서 사용할 수는 있습니다.

CLI에서 Power BI 레포트 다운로드하는 방법
	1.	Azure AD 앱 등록
Power BI REST API에 접근하려면 Azure AD에 애플리케이션을 등록하고 OAuth2 인증을 설정해야 합니다.
	•	Azure Portal에서 앱 등록 (App registration)
	•	Power BI Service API 권한 추가 (Reports.Read.All 권한 필요)
	•	클라이언트 ID, 테넌트 ID, 클라이언트 시크릿 발급
	2.	Access Token 획득 (CLI로 실행 가능)
Power BI REST API를 호출하려면 OAuth2 인증을 수행해야 합니다.
CLI 환경에서는 curl이나 httpie 같은 툴을 사용하여 Access Token을 발급받을 수 있습니다.

curl -X POST "https://login.microsoftonline.com/{TENANT_ID}/oauth2/v2.0/token" \
-H "Content-Type: application/x-www-form-urlencoded" \
-d "client_id={CLIENT_ID}" \
-d "scope=https://graph.microsoft.com/.default" \
-d "client_secret={CLIENT_SECRET}" \
-d "grant_type=client_credentials"

위 명령어는 Graph API용 Access Token을 가져오는 예시지만,
Power BI API에 접근하려면 scope=https://graph.microsoft.com/.default 대신
scope=https://analysis.windows.net/powerbi/api/.default 를 사용해야 합니다.

curl -X POST "https://login.microsoftonline.com/{TENANT_ID}/oauth2/v2.0/token" \
-H "Content-Type: application/x-www-form-urlencoded" \
-d "client_id={CLIENT_ID}" \
-d "scope=https://analysis.windows.net/powerbi/api/.default" \
-d "client_secret={CLIENT_SECRET}" \
-d "grant_type=client_credentials"


	3.	Power BI REST API를 사용해 레포트 다운로드
Access Token을 획득한 후, Power BI REST API를 사용하여 레포트를 다운로드할 수 있습니다.
	•	워크스페이스 내 레포트 목록 조회

curl -X GET "https://api.powerbi.com/v1.0/myorg/reports" \
-H "Authorization: Bearer {ACCESS_TOKEN}" \
-H "Content-Type: application/json"


	•	특정 레포트를 PDF로 내보내기

curl -X POST "https://api.powerbi.com/v1.0/myorg/reports/{REPORT_ID}/ExportTo" \
-H "Authorization: Bearer {ACCESS_TOKEN}" \
-H "Content-Type: application/json" \
-d '{
      "format": "PDF"
    }'


	•	내보내기 완료 여부 확인

curl -X GET "https://api.powerbi.com/v1.0/myorg/reports/{REPORT_ID}/ExportTo/Status" \
-H "Authorization: Bearer {ACCESS_TOKEN}" \
-H "Content-Type: application/json"


	•	완료된 PDF 다운로드

curl -X GET "https://api.powerbi.com/v1.0/myorg/reports/{REPORT_ID}/ExportTo/File" \
-H "Authorization: Bearer {ACCESS_TOKEN}" \
-H "Content-Type: application/json" \
-o output.pdf

정리

✅ Graph API의 로그인 정보를 직접 사용할 수 없음
✅ Azure AD OAuth2 인증을 사용하여 Access Token을 획득해야 함
✅ CLI에서 Access Token을 발급받고 Power BI REST API를 호출하여 레포트 다운로드 가능
✅ Power BI REST API를 이용하여 PDF, PPTX 등의 형식으로 내보낼 수 있음

CLI 환경에서만 구현할 수 있으므로, curl이나 httpie와 같은 명령어를 조합하여 스크립트를 작성하면 자동화가 가능합니다.
추가적인 기능이 필요하면 PowerShell, Python(requests) 같은 도구를 활용하는 것도 고려할 수 있습니다.

서비스 프린시펄이 꺼져 있는 경우, **사용자 계정 기반 로그인(Delegated Permission)**을 사용하여 Power BI 보고서를 다운로드할 수 있습니다. 아래 절차를 따르면 CLI에서 사용자 계정 로그인 방식으로 PBIX 파일을 다운로드할 수 있습니다.

✅ Power BI 사용자 계정 로그인 방식으로 PBIX 파일 다운로드하기

1️⃣ OAuth 2.0 디바이스 코드 인증으로 사용자 로그인

우선, **사용자 계정(조직 계정, AAD 계정)**을 통해 인증해야 합니다.

curl -X POST https://login.microsoftonline.com/common/oauth2/devicecode \
  -H "Content-Type: application/x-www-form-urlencoded" \
  -d "client_id={client_id}" \
  -d "scope=https://graph.microsoft.com/.default"

이 명령어를 실행하면 응답에 device_code와 verification_uri가 포함됩니다.

{
  "device_code": "xxxxxxxxxxxxxxx",
  "user_code": "ABCD-1234",
  "verification_uri": "https://microsoft.com/devicelogin",
  "expires_in": 900,
  "interval": 5
}

	•	브라우저에서 verification_uri (예: https://microsoft.com/devicelogin)에 접속
	•	터미널에서 받은 user_code를 입력
	•	조직 계정(Azure AD 계정)으로 로그인

로그인이 완료되면, 해당 계정으로 Power BI API 호출이 가능해집니다.

2️⃣ 액세스 토큰 발급

디바이스 코드로 로그인한 후, 액세스 토큰을 발급받습니다.

curl -X POST https://login.microsoftonline.com/common/oauth2/token \
  -H "Content-Type: application/x-www-form-urlencoded" \
  -d "grant_type=device_code" \
  -d "client_id={client_id}" \
  -d "device_code={device_code}"

성공하면 다음과 같은 JSON 응답을 받습니다.

{
  "token_type": "Bearer",
  "expires_in": 3600,
  "access_token": "eyJ0eXAiOiJKV1QiLCJhbGci..."
}

	•	여기서 "access_token" 값을 복사하여 Power BI API 호출에 사용합니다.

3️⃣ Power BI에서 보고서 목록 조회

보고서 ID(report_id)를 찾기 위해 Power BI API를 호출합니다.

curl -X GET "https://api.powerbi.com/v1.0/myorg/reports" \
  -H "Authorization: Bearer {access_token}" \
  -H "Content-Type: application/json"

이 명령어를 실행하면 조직 내 Power BI 보고서 목록이 반환됩니다.

예제 응답:

{
  "value": [
    {
      "id": "report-12345",
      "name": "Sales Report",
      "webUrl": "https://app.powerbi.com/reports/report-12345"
    }
  ]
}

👉 id 값을 복사하여 다음 단계에서 사용합니다.

4️⃣ PBIX 파일 다운로드

이제 report_id를 사용하여 PBIX 파일을 다운로드합니다.

curl -X GET "https://api.powerbi.com/v1.0/myorg/reports/report-12345/Export" \
  -H "Authorization: Bearer {access_token}" \
  -H "Content-Type: application/json" \
  --output report.pbix

👉 실행이 성공하면 report.pbix 파일이 현재 디렉토리에 다운로드됩니다.

📌 정리
	1.	디바이스 코드 인증 방식으로 사용자 로그인.
	2.	액세스 토큰을 발급받음.
	3.	Power BI 보고서 목록을 조회하여 report_id를 확인.
	4.	Power BI API를 호출하여 PBIX 파일을 다운로드.

이 방식은 CLI 환경에서도 사용자 계정만으로 Power BI 보고서를 다운로드할 수 있는 방법이며, 서비스 프린시펄을 활성화하지 않아도 실행 가능합니다.



세ㄱㄱ스

서비스 프린시펄이 꺼져 있는 경우, **사용자 계정 기반 로그인(Delegated Permission)**을 사용하여 Power BI 보고서를 다운로드할 수 있습니다. 아래 절차를 따르면 CLI에서 사용자 계정 로그인 방식으로 PBIX 파일을 다운로드할 수 있습니다.

✅ Power BI 사용자 계정 로그인 방식으로 PBIX 파일 다운로드하기

1️⃣ OAuth 2.0 디바이스 코드 인증으로 사용자 로그인

우선, **사용자 계정(조직 계정, AAD 계정)**을 통해 인증해야 합니다.

curl -X POST https://login.microsoftonline.com/common/oauth2/devicecode \
  -H "Content-Type: application/x-www-form-urlencoded" \
  -d "client_id={client_id}" \
  -d "scope=https://graph.microsoft.com/.default"

이 명령어를 실행하면 응답에 device_code와 verification_uri가 포함됩니다.

{
  "device_code": "xxxxxxxxxxxxxxx",
  "user_code": "ABCD-1234",
  "verification_uri": "https://microsoft.com/devicelogin",
  "expires_in": 900,
  "interval": 5
}

	•	브라우저에서 verification_uri (예: https://microsoft.com/devicelogin)에 접속
	•	터미널에서 받은 user_code를 입력
	•	조직 계정(Azure AD 계정)으로 로그인

로그인이 완료되면, 해당 계정으로 Power BI API 호출이 가능해집니다.

2️⃣ 액세스 토큰 발급

디바이스 코드로 로그인한 후, 액세스 토큰을 발급받습니다.

curl -X POST https://login.microsoftonline.com/common/oauth2/token \
  -H "Content-Type: application/x-www-form-urlencoded" \
  -d "grant_type=device_code" \
  -d "client_id={client_id}" \
  -d "device_code={device_code}"

성공하면 다음과 같은 JSON 응답을 받습니다.

{
  "token_type": "Bearer",
  "expires_in": 3600,
  "access_token": "eyJ0eXAiOiJKV1QiLCJhbGci..."
}

	•	여기서 "access_token" 값을 복사하여 Power BI API 호출에 사용합니다.

3️⃣ Power BI에서 보고서 목록 조회

보고서 ID(report_id)를 찾기 위해 Power BI API를 호출합니다.

curl -X GET "https://api.powerbi.com/v1.0/myorg/reports" \
  -H "Authorization: Bearer {access_token}" \
  -H "Content-Type: application/json"

이 명령어를 실행하면 조직 내 Power BI 보고서 목록이 반환됩니다.

예제 응답:

{
  "value": [
    {
      "id": "report-12345",
      "name": "Sales Report",
      "webUrl": "https://app.powerbi.com/reports/report-12345"
    }
  ]
}

👉 id 값을 복사하여 다음 단계에서 사용합니다.

4️⃣ PBIX 파일 다운로드

이제 report_id를 사용하여 PBIX 파일을 다운로드합니다.

curl -X GET "https://api.powerbi.com/v1.0/myorg/reports/report-12345/Export" \
  -H "Authorization: Bearer {access_token}" \
  -H "Content-Type: application/json" \
  --output report.pbix

👉 실행이 성공하면 report.pbix 파일이 현재 디렉토리에 다운로드됩니다.

📌 정리
	1.	디바이스 코드 인증 방식으로 사용자 로그인.
	2.	액세스 토큰을 발급받음.
	3.	Power BI 보고서 목록을 조회하여 report_id를 확인.
	4.	Power BI API를 호출하여 PBIX 파일을 다운로드.

이 방식은 CLI 환경에서도 사용자 계정만으로 Power BI 보고서를 다운로드할 수 있는 방법이며, 서비스 프린시펄을 활성화하지 않아도 실행 가능합니다.