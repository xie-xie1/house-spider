#####首先导入所需要的包：
import requests
from bs4 import BeautifulSoup
import pandas as pd
from pandas import DataFrame
import os

#####主函数中调用spider_house()：
def main():
    # 调用函数
    spider_house()

#####读取链家网页并进行异常处理，代码如下：
def spider_house():
    # 循环实现翻页
    for i in range(page):
        url = 'https://nj.lianjia.com/ershoufang/pg'+str(i)
        try:        #异常处理
            r=requests.get(url)
            #如果状态不是200，则引发异常
            r.raise_for_status()
            #配置编码
            r.encoding=r.apparent_encoding
        except:
            print("产生异常")

#####验证请求头部信息：
 headers = {
        'User-Agent':'Mozilla/5.0 (Windows NT 10.0; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/76.0.3809.87 Safari/537.36'
        }

############定义数据文件名称及写入位置：
 file_path = os.path.join(os.path.dirname(os.path.abspath(__file__)), 'lianjia_data.xlxs')
        resp = requests.get(url,headers=headers)

#########利用BeautifulSoup语法解析网页
soup = BeautifulSoup(html,'html.parser')

########获取ul标签下所有的li标签
infos = soup.find('ul',{'class':'sellListContent'}).find_all('li') 

#######对li标签进行遍历，获取每一个li标签下的所需要数据
for info in infos:
            #获取楼房的标题名称
            name = info.find('div',{'class':'title'}).find('a').get_text()
            print(name)
            #获取楼房的价格（套/万元）
            price = info.find('div',{'class':'priceInfo'}).find('div',{'class':'totalPrice'}).find('span').get_text()
            print(price)
            #获取楼房单价（m2/元）
            totleprice =info.find('div',{'class':'priceInfo'}).find('div',{'class':'unitPrice'}).get_text()
            # 对输出的楼房单价进行分割  只获取其单价
            totle = totleprice.split(r'单价')
            date0=totle[1]
            date1 =date0.split(r'元')          
            date2 =date1[0]
            print(date2)
            #获取楼房的地址信息
            address = info.find('div',{'class':'flood'}).find('a').get_text()
            print(address)
            #获取房间样式信息
            new = info.find('div',{'class':'houseInfo'}).get_text()
            list = new.split(r'|')
            area =list[0]
            #获取房间规模信息（m2）
            print(area)
            area1=list[1]
            area2=area1.split(r'平')
            area3 =area2[0]
            print(area3)
            area2=list[4]
            #获取楼层规模信息
            lou=area2.split(r'(')
            lou1=lou[0]
            print(lou1)

#######定义一个字典用于存放创建列的名称
infos={}
            # 定义表格中列的名称
            infos['楼房名称'] = name
            infos['价格'] = price
            infos['单价'] = date2
            infos['地址'] = address
            infos['房间样式'] = area
            infos['房间规模'] = area3
            infos['楼层规模'] = lou1
            df = DataFrame(infos, index=[0])
            if os.path.exists(file_path):
                # 字符编码采用utf-8
                df.to_csv(file_path, header=False, index=False, mode="a+", encoding="utf_8_sig")
            else:
                df.to_csv(file_path, index=False, mode="w+", encoding="utf_8_sig")