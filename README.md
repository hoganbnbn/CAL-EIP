# CAL-EIPfrom numpy import true_divide
from selenium import webdriver
from selenium.webdriver.chrome.options import Options
from selenium.webdriver.common import by
from selenium.webdriver.common.by import By
from selenium.webdriver.support.ui import WebDriverWait
from selenium.webdriver.support import expected_conditions as EC
import time
from selenium.common.exceptions import NoSuchElementException
from selenium.webdriver.support.ui import Select
import pandas as pd


#origin region: A - ASIA; E - EUROPE; S - OCEANIA; U - AMERICA
dep_reg = "A"
dep_city = "TPE"

#arr region: A - ASIA; E - EUROPE; S - OCEANIA; U - AMERICA
arr_reg = "A"
arr_city = "HKG"

#date
travel_day = "2024/10/06"

#EIP username and password
eipuser = ""
eippass = ""

# 設定 Chrome 瀏覽器的選項
options = Options()

# 初始化 WebDriver，並使用選項
driver = webdriver.Chrome(options=options)

# 打開目標網址
driver.get("https://eip.china-airlines.com/cas/login?service=https%3A%2F%2Feip.china-airlines.com%2Flportal%2F%3Bjsessionid%3D74FEF1B4D411C46747E365FD4A6ADF1D.slbt1")


# 等待頁面加載並找到 "進入" 按鈕（ID 為 "btn-login-style001"）
login_button = WebDriverWait(driver, 1).until(
        EC.element_to_be_clickable((By.ID, "btn-login-style001"))
    )

    # 點擊 "進入" 按鈕
login_button.click()


#登入網頁填入帳號密碼
driver.implicitly_wait(30)

usernameInput=driver.find_element(By.NAME, "username")
passwordInput=driver.find_element(By.NAME, "password")
usernameInput.send_keys(eipuser) #EIP帳號
passwordInput.send_keys(eippass) #EIP密碼

login_button = WebDriverWait(driver, 1).until(
        EC.element_to_be_clickable((By.ID, "btn-submitButton"))
    )

    # 點擊 "進入" 按鈕
login_button.click()

#在動態密碼選擇傳送方式
login_button = WebDriverWait(driver, 1).until(
        EC.element_to_be_clickable((By.ID, "teamplus")) 
        # EC.element_to_be_clickable((By.ID, "email")) 
    )

    # 點擊 "進入" 按鈕
login_button.click()

#動態密碼網頁點選送出
login_button = WebDriverWait(driver, 1).until(
        EC.element_to_be_clickable((By.NAME, "Submit"))
    )

    # 點擊 "進入" 按鈕
login_button.click()


#等待輸入動態密碼
time.sleep(18)

#點選動態密碼輸入送出按鈕
login_button = WebDriverWait(driver, 3).until(
        EC.element_to_be_clickable((By.NAME, "Submit"))
    )

    # 點擊 "進入" 按鈕
login_button.click()


# 使用 XPath 和 href 屬性來找到連結並點擊機票專區
ticket_line = WebDriverWait(driver, 5).until(
        EC.element_to_be_clickable((By.XPATH, "//a[@href='/lportal/web/home/7']"))
)

ticket_line.click()

# 使用 XPath 和 href 屬性來找到連結並點擊員工優待機票系統
ticket_line = WebDriverWait(driver, 1).until(
        EC.element_to_be_clickable((By.XPATH, "//a[@href='https://calcfec.china-airlines.com/staffticketweb/IAMCheck.aspx']"))
)

ticket_line.click()

# 獲取所有的窗口句柄
windows = driver.window_handles

# 切換到新打開的分頁（通常是第二個分頁）
driver.switch_to.window(windows[1])

# 等待頁面加載並找到 "進入系統" 按鈕
login_button = WebDriverWait(driver, 1).until(
        EC.element_to_be_clickable((By.ID, "linkNext"))
    )

    # 點擊 "進入" 按鈕
login_button.click()

# 等待一會兒，讓點擊生效
driver.implicitly_wait(5)
# time.sleep (20)

#關閉跳出視窗
rep = 0
while True:
    try:
        rep = rep + 1
        cancel_button = driver.find_element(By.XPATH, "//*[@id='modalnews%s']/div/div/div[3]/button" %(rep))
        cancel_button.click()
    except NoSuchElementException:
        break


# 使用 XPath 和 href 屬性來找到連結並點擊空位查詢 <a href="et_seat_list_step01.aspx" class="blockui"> 
ticket_line = WebDriverWait(driver, 1).until(
        EC.element_to_be_clickable((By.XPATH, "//*[@id='ContentPlaceHolder1_lbl_item']"))
)

ticket_line.click()

#選擇台北到曼谷
# 找到下拉選單<select name="ctl00$ContentPlaceHolder1$ddlArrStn" id="ContentPlaceHolder1_ddlArrStn" class="form-control ">
# 使用 Select 類來選擇下拉選單的值

# 選擇台北 (ASIA TPE) //*[@id="ContentPlaceHolder1_ddlDepRegion"] //*[@id="ContentPlaceHolder1_ddlDepStn"]
select_dep_region = Select(driver.find_element(By.XPATH, "//*[@id='ContentPlaceHolder1_ddlDepRegion']"))
select_dep_region.select_by_value(dep_reg)

select_dep= Select(driver.find_element(By.XPATH, "//*[@id='ContentPlaceHolder1_ddlDepStn']"))
select_dep.select_by_value(dep_city)  

# 選擇曼谷 (ASIA BKK) //*[@id="ContentPlaceHolder1_ddlArrRegion"] //*[@id="ContentPlaceHolder1_ddlArrStn"]
select_arr_region = Select(driver.find_element(By.XPATH, "//*[@id='ContentPlaceHolder1_ddlArrRegion']"))
select_arr_region.select_by_value(arr_reg)

select_arr = Select(driver.find_element(By.XPATH, "//*[@id='ContentPlaceHolder1_ddlArrStn']"))
select_arr.select_by_value(arr_city)  

# 使用 JavaScript 設置日期 //*[@id="ContentPlaceHolder1_txtDate"]
date_element = driver.find_element(By.XPATH, "//*[@id='ContentPlaceHolder1_txtDate']")
driver.execute_script("arguments[0].value = '"+ travel_day + "';", date_element)

#點前後一天<input id="ContentPlaceHolder1_rbsearchFB" type="radio" name="ctl00$ContentPlaceHolder1$querycondition" value="rbsearchFB">
#//*[@id="ContentPlaceHolder1_rbsearchFB"]
login_button = WebDriverWait(driver, 1).until(  
        EC.element_to_be_clickable((By.XPATH, "//*[@id='ContentPlaceHolder1_rbsearchFB']"))
    )

    # 點擊 "進入" 按鈕
login_button.click()

#點查詢
login_button = WebDriverWait(driver, 1).until(
        EC.element_to_be_clickable((By.ID, "ContentPlaceHolder1_LinkButton1"))
    )

    # 點擊 "進入" 按鈕
login_button.click()

# 3 days data 
for t in range(1,4):
    table_trs = WebDriverWait(driver, 1).until(
        EC.presence_of_all_elements_located((By.XPATH, "//*[@id='ContentPlaceHolder1_div_sector%s']/table/tbody/tr" %(t)))
    )

    # print date
    print(driver.find_element(By.ID, "ContentPlaceHolder1_lbl_Sector_date%s" %(t)).get_attribute("innerHTML"))
    
    if driver.find_elements(By.ID, "ContentPlaceHolder1_rpt_Sector%s_dvNoRecords"%(t)):
        print("no flight!")
    else: 
        value_list = []
        for row in table_trs:
            value_list.append({
                'Flight':row.find_elements(By.TAG_NAME, "td")[0].text,
                #'departure':row.find_elements(By.TAG_NAME, "td")[1].text,
                #'arrival':row.find_elements(By.TAG_NAME, "td")[2].text,
                'Type':row.find_elements(By.TAG_NAME, "td")[3].text,
                #'config':row.find_elements(By.TAG_NAME, "td")[5].text,
                '空位':row.find_elements(By.TAG_NAME, "td")[6].text,
                'waiting':row.find_elements(By.TAG_NAME, "td")[7].text
            })
        
        df = pd.DataFrame(value_list)
        print(df.to_string())


# driver.quit()
