Automation of a WEBSITE-1
from selenium import webdriver
from selenium.webdriver.common.by import By
import time
import re
from datetime import datetime,date
def driver():
    browser=webdriver.Chrome("C:\Translation EXE\chromedriver.exe")
    browser.maximize_window()
    browser.get("https://sppp.rajasthan.gov.in/sppp/index.php")
    time.sleep(2)
    navigation(browser)

def cleanhtml(raw_html):
  CLEANR = re.compile('<.*?>') 
  CLEANR1= re.compile('<.*?>|&([a-z0-9]+|#[0-9]{1,6}|#x[0-9a-f]{1,6});')
  cleantext = re.sub(CLEANR, '', raw_html)
  return cleantext

def navigation(browser):
    for latest in browser.find_elements(By.XPATH,'//*[@id="latest_active_bid"]/a'):
        latest.click()
        time.sleep(2)
        break
    count = 1
    Collected_Data = []
    pageCount = 1
    next = True
    while next == True:
        for dataofnext in browser.find_elements(By.XPATH,'//*[@id="examplesearch"]/tbody/tr'):
            for Btitle in browser.find_elements(By.XPATH,'//*[@id="examplesearch"]/tbody/tr['+str(count)+']/td[5]'):
                Btitle_text = Btitle.get_attribute('innerText')
        
            for Department in browser.find_elements(By.XPATH,'//*[@id="examplesearch"]/tbody/tr['+str(count)+']/td[6]'):
                Department_text = Department.get_attribute('innerText')
                
            Collected_Data.append({"Btitle": Btitle_text,"Department":Department_text})

            for details in browser.find_elements(By.XPATH,'//*[@id="examplesearch"]/tbody/tr['+str(count)+']/td[8]'):
                browser.execute_script("arguments[0].scrollIntoView();", details)
                details.click()
                time.sleep(2)
                break
            
            count += 1
            browser.switch_to.window(browser.window_handles[1])
            # window_before = browser.window_handles[1] 
            scrapping(browser)
            browser.switch_to.window(browser.window_handles[0])

        for next_page in browser.find_elements(By.XPATH,'//*[@id="examplesearch_wrapper"]/div[3]/div[2]/div/a[3]'):
            next_page.click()
            time.sleep(2)
            count = 1
    
        pageCount += 1
        if pageCount >= 5:
            next = False
            break 
    
def scrapping(browser):
    for Inner_Details in browser.find_elements(By.XPATH,'//*[@id="print-this-table"]/tbody/tr[2]/td/table/tbody'):
        Inner_Details_text = Inner_Details.get_attribute('outerHTML')
        r =  re.sub("\s+"," ",Inner_Details_text)
        print(r)
        Dept_Name = r.partition("Department Name</td>")[2].partition('</td>')[0]
        final_dpartment = cleanhtml(Dept_Name)
        print(final_dpartment)
        final_dpartment.strip()
        B_Date = r.partition("Bid Uploaded Date</td>")[2].partition('</td>')[0]
        BU_Date = cleanhtml(B_Date)
        final_date = BU_Date.replace("&nbsp;","")
        print(final_date)
        # print(final_date.strptime("%Y-%m-%d"))
        phone_no = r.partition("PIN:312601,")[2].partition('</td>')[0]
        p_no = cleanhtml(phone_no)
        final_p_no = p_no.replace(", Fax No.:","")
        print(final_p_no)
        
    print
         
    browser.close()     
driver()

