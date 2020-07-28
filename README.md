# Day003_HW.ipynb
# 「File I/O」與「xmltodict」讀出來的內容有什麼差異
File  I/O 會將完整的內容讀取出來，例如 XML這個檔案格式會利⽤ <Label>...</Label> 標籤的⽅方式記錄資料，讀取時會將 標籤顯示出來
xmltodict部會將標籤讀取出來，只留下文字部分
# 1. 請問高雄市有多少地區有溫度資料？
import urllib.request  # 下載檔案
import zipfile
res= "http://opendata.cwb.gov.tw/govdownload?dataid=F-D0047-093&authorizationkey=rdec-key-123-45678-011121314"
urllib.request.urlretrieve(res  ,  "./data/example.zip "  )  #這時 把資料放在自動建立的ZIP檔案裡
f=zipfile.ZipFile( "./data/example.zip") # 將ZIP檔案裡的資料先存在一個register裡面
f.extractall('./data')  # 將register 裡的資料釋放到資料夾中
import os, sys
dirs = os.listdir( './data/' )  # 打开文件
for file in dirs:  # 输出所有文件和文件夹
    print(file)   #確認file裡有文件
  fh=open("./data/64_72hr_CH.xml", "r" , encoding="utf-8")  #  64_72hr_CH.xml 為高雄市的資料
xml = fh.read()
fh.close()
print(xml)  
import xmltodict
d = dict(xmltodict.parse(xml))  # 解析檔案內容

location=d['cwbopendata']['dataset']['locations']['location']  # 取出高雄市
j=0
for i in location:
    print( i['locationName'] )    # 取出高雄市有多少地區有溫度資料
    j=j+1
print(j)  #總計38個地區
# 2. 請取出每一個地區所記錄的第一個時間點跟溫度
 import xmltodict
d = dict(xmltodict.parse(xml))  # 解析檔案內容
location=d['cwbopendata']['dataset']['locations']['location']  # 取出高雄市
for i in location:
    for j in i['weatherElement']:  # j equals to weatherElements ,though that is one of them
            if j['description'] == '溫度':
                print(i['locationName'], j['time'][0]['dataTime'], j['time'][0]['elementValue']['value'])#用[0]去代表要索引的是同個層級下有一樣名字的標籤的第一個
# 3. 請取出第一個地區所記錄的每一個時間點跟溫度                
location=d['cwbopendata']['dataset']['locations']['location']  # 取出高雄市 , 且注意 , 不能直接放同個層級下有一樣名字的標籤
for i in location:
    for j in i['weatherElement']:  # j equals to weatherElements ,though that is one of them
            if j['description'] == '溫度':  # weatherElement 有許多資料 , 用if 選取 "溫度" 
                for k in j['time']:     # weatherElement的 溫度 有許多不同的時間點 利用for迴圈讓 list indices 為 integers or slices
                    if  i['locationName'] != '鹽埕區':  # 由於locationName 有好幾個 用 continue 篩掉剩下區域
                        continue
                    print(i['locationName'], k['dataTime'], k['elementValue']['value'])                
                
