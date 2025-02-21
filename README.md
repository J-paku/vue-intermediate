```python
from selenium import webdriver
from selenium.webdriver.common.by import By
from selenium.webdriver.common.keys import Keys
from selenium.webdriver.support.ui import WebDriverWait
from selenium.webdriver.support import expected_conditions as EC
import time
import os

# 1. Edge WebDriver 설정
options = webdriver.EdgeOptions()
options.use_chromium = True
options.add_argument("--start-maximized")

# 다운로드 폴더 설정
download_dir = r"C:\path\to\download\folder"
if not os.path.exists(download_dir):
    os.makedirs(download_dir)

prefs = {
    "download.default_directory": download_dir,
    "download.prompt_for_download": False,
    "download.directory_upgrade": True
}
options.add_experimental_option("prefs", prefs)

driver = webdriver.Edge(executable_path="C:\\Program Files (x86)\\Microsoft\\Edge WebDriver\\msedgedriver.exe", options=options)

# 2. Power BI 로그인 페이지 이동
driver.get("https://login.microsoftonline.com")

# 3. 이메일 입력
email_input = WebDriverWait(driver, 10).until(
    EC.presence_of_element_located((By.NAME, "loginfmt"))
)
email_input.send_keys("your_email@example.com")
email_input.send_keys(Keys.RETURN)

# 4. 비밀번호 입력
time.sleep(2)
password_input = WebDriverWait(driver, 10).until(
    EC.presence_of_element_located((By.NAME, "passwd"))
)
password_input.send_keys("your_password")
password_input.send_keys(Keys.RETURN)

# 5. '다른 사람의 로그인을 유지하지 않음' 선택
time.sleep(2)
no_button = WebDriverWait(driver, 10).until(
    EC.presence_of_element_located((By.ID, "idBtn_Back"))
)
no_button.click()

# 6. Power BI 포털로 이동
time.sleep(3)
driver.get("https://app.powerbi.com")

# 7. 워크스페이스로 이동
time.sleep(5)
workspace_link = WebDriverWait(driver, 10).until(
    EC.presence_of_element_located((By.LINK_TEXT, "Workspace Name"))  # 워크스페이스 이름 입력
)
workspace_link.click()

# 8. 모든 파일 링크 수집
time.sleep(5)
file_links = driver.find_elements(By.XPATH, "//a[@href and contains(@href, '/reportEmbed')]")

# 9. 수집된 링크 출력 및 다운로드
for index, file in enumerate(file_links):
    file_url = file.get_attribute("href")
    print(f"[{index+1}] Downloading: {file_url}")
    driver.execute_script("window.open('');")
    driver.switch_to.window(driver.window_handles[-1])
    driver.get(file_url)
    
    # 다운로드 대기 (파일 크기에 따라 조정 필요)
    time.sleep(10)
    driver.close()
    driver.switch_to.window(driver.window_handles[0])

# 10. 드라이버 종료
driver.quit()