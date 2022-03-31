# Web-crawler

##Before you start 
1.open your Chrome 
2.go to setting
3.click about Chrome
4.see your Chrome version(for example x86_64)
5.here to download your version
https://chromedriver.chromium.org/downloads
6.put it in your folder with this code file

##Installation
from selenium import webdriver
import matplotlib.pyplot as plt
import time
from tkinter import*
from wordcloud import WordCloud
import jieba

##code
def eat():   
    food_name=food_name_input.get()  ##獲取使用者輸入的品名
    chrome_browser = webdriver.Chrome() ##自動化瀏覽器會依據品項爬取內容
    chrome_browser.get("https://www.mcdonalds.com/tw/zh-tw/full-menu.html")
    link=chrome_browser.find_element_by_link_text(food_name)
    link.click()##讓滑鼠點入對應的品名
    time.sleep(1) ##等待一秒鐘
    title=chrome_browser.find_elements_by_class_name("copy") ##根據class去爬取卡洛里資料
    time.sleep(10)##等待10秒鐘讓使用者瀏覽
    text.insert(END,title[1].text,food_name) ##將搜尋內容顯示在tkinter上
    chrome_browser.close()   ##關閉chrome頁面
    
def main():
    global food_name_input,text,A,background,b,a##將這些設定為全域參數
    background=Tk()      
    background.title("麥當勞成分查詢") 
    background.geometry("750x350")       
    Label(background,text='請輸入想吃的東西,且正確的品名：',font=("微軟雅黑",18),fg='black').grid() 
    food_name_input=Entry(background,font=("微軟雅黑",15))
    food_name_input.grid(row=0,column=1) 
    text=Listbox(background,font=("微軟雅黑",15),width=45,height=10)
    
    var1=IntVar()
    Checkbutton(background,text="請注意搜尋雞請更正為鷄",variable=var1).grid(row=1,column=2,sticky=W)
    text.grid(row=1,columnspan=2)
    Button(background,text="搜尋",font=("微軟雅黑",15),command=eat).grid(row=0,column=2,sticky=W) 
    ##將搜尋按鈕和eat函示連結
    Button(background,text="離開",font=("微軟雅黑",15),command=background.quit).grid(row=2,column=1,sticky=E)
    ##將離開按鈕設定為點擊關閉連結
    Button(background,text="下載麥當勞文字雲",font=("微軟雅黑",15),command=download).grid(row=2,column=2,sticky=E)
    ##將下載按鈕和download函示連結
    mainloop() 
def download():
    jieba.load_userdict('dict.txt.big')
    chrome_browser = webdriver.Chrome()
    chrome_browser.get("https://www.mcdonalds.com/tw/zh-tw/full-menu.html")
    time.sleep(1)
    a=[]
    for i in range(1,138): ###總共有137個品項 將每一個名稱一一爬取下來
        title=chrome_browser.find_elements_by_xpath(f'//*[@id="maincatcontent"]/div/ul/li[{i}]/a/div/div[2]/span[2]')
        for j in title:
            a.append(j.text)
    A=str(a)
    b={}
    for ch in A: ##去除符號等多餘字元
        if ch in "+'$HappyMealOreo30612/()BLTRE48]z一[,":
            A=A.replace(ch,'') ##以空白代替
    for x in A: ##計算各中文字出現字數
        b[x]=A.count(x) 
    wc = WordCloud(font_path="NotoSansTC-Bold.otf",   ##文字雲繪圖，font_path是字型的樣式，backgroung設定為白色，最多字數為200
                   background_color="white", 
                   max_words = 200)      
    wc.generate_from_frequencies(b)
    plt.imshow(wc)
    plt.axis("off")
    plt.figure(figsize=(50, 50), dpi = 600) ##設定尺寸
    plt.show()
    wc.to_file('output.jpg') ##設定檔名
    chrome_browser.close() ##將麥當勞頁面關閉



    
