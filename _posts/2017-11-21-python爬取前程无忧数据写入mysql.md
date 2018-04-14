只获取公司、职位、地区、薪水，没有其他详细的信息，所以代码很简单。体验体验python和mysql的交互，刚开始插进去的数据都是乱码，在连接mysql的时候指定utf8编码就解决了
```  
# -*- coding:utf-8 -*-  
import requests  
import re,pymysql  
  
def get_content(page):  
    url ='http://search.51job.com/list/000000,000000,0000,00,9,99,%25E6%2595%25B0%25E6%258D%25AE%25E5%2588%2586%25E6%259E%2590,2,'+ str(page)+'.html'  
    html=requests.get(url)  
    s = requests.session()  
    s.keep_alive = False  
    html.encoding='gbk'  
    return html  
  
def get(html):  
    reg = re.compile(r'class="t1 ">.*? <a target="_blank" title="(.*?)".*? <span class="t2"><a target="_blank" title="(.*?)".*?<span class="t3">(.*?)</span>.*?<span class="t4">(.*?)</span>.*? <span class="t5">(.*?)</span>',re.S)#匹配换行符  
    items = re.findall(reg,html.text)  
    return items  
def savetosql(items):  
    print('正在连接到服务器')  
    db=pymysql.connect('localhost','root','123456','justtest', charset="utf8")  
    print('连接成功')  
    cursor=db.cursor()  
    cursor.execute('DROP TABLE IF EXISTS JOBS')  
    SQL='''''CREATE TABLE JOBS(  
        POSITION TEXT(1000) NOT NULL, 
        COMPANY TEXT(1000), 
        ADDRESS TEXT(1000), 
        SALARY TEXT(1000), 
        DATE TEXT(1000))'''  
    cursor.execute(SQL)  
    print('创建成功')  
    for item in items:  
        sql="insert into JOBS values('%s','%s','%s','%s','%s')"%(item[0],item[1],item[2],item[3],item[4])  
        print(item[1])  
        try:  
            cursor.execute(sql)  
            db.commit()  
        except:  
            print('插入失败')  
            db.rollback()  
for page in range(1,20):  
    print('正在爬取第{}页'.format(page))  
    savetosql(get(get_content(page))) 
```   
   
 ![image](https://raw.githubusercontent.com/lbship/lbship.github.io/master/img/51job9.png)  
