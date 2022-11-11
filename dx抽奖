import requests
import json
import time
import random
import re
import os
from concurrent.futures import ThreadPoolExecutor


#注意事项：
'''
注意事项：
    
    脚本优先领取每日1G，领取每日1G完成后才会触发，7天10G和我的特权签到功能
    
    仅限广州电信号码，仅限广州电信号码，仅限广州电信号码，仅限广州电信号码
    仅限广州电信号码，仅限广州电信号码，仅限广州电信号码，仅限广州电信号码
    仅限广州电信号码，仅限广州电信号码，仅限广州电信号码，仅限广州电信号码
    仅限广州电信号码，仅限广州电信号码，仅限广州电信号码，仅限广州电信号码
    
    CK有效期30分钟，脚本随机等在2分钟内，所以脚本在25分钟内必须运行2次，
    才能有保证容许1次网络不通的情况，确保1次网络不通CK不失效，提高可用性
    
    建议最少每12分钟运行一次脚本，确保容许1次网络不通CK不失效，设置定时参考:    10 0/12 * * * ? 
    建议最少每12分钟运行一次脚本，确保容许1次网络不通CK不失效，设置定时参考:    10 0/12 * * * ?
    建议最少每12分钟运行一次脚本，确保容许1次网络不通CK不失效，设置定时参考:    10 0/12 * * * ?
    建议最少每12分钟运行一次脚本，确保容许1次网络不通CK不失效，设置定时参考:    10 0/12 * * * ?
    
    获取ck地址：
    https://vip.mini189.cn/yqt_fans/html/wechatIndex/index.html

'''

#CK配置
dx_cookies=[
    #参考示例：是否启用总开关(必须)，是否7天10G(必须),是否特权签到(必须),ck值(必须)，结束日期(必须),昵称/手机号(可选)
    {"status":True,"sevenG":True,"sign":True,"ck":"JSESSIONID=XXXXXXX.mini189c","endDate":"2022-12-31","user":"篮球恶霸ik"},
    #{"status":True,"sevenG":True,"sign":True,"ck":"JSESSIONID=XXXXXXX.mini189c","endDate":"2022-12-31","user":"菜狗小兵"},
    #{"status":False,"sevenG":True,"sign":True,"ck":"JSESSIONID=XXXXX.mini189c","endDate":"2022-12-31","user":"无敌风火轮"},
]

#是否发送青龙通知
sendNotify=True
#收集通知内容
msgg=[]
#通知的标题
title='广州电信每日1G青龙脚本'


#入口，处理配置文件
def main():
    global dx_cookies
    try:         
        start = time.time()
        i=1;
        with ThreadPoolExecutor(20) as  thread_pool:#定义20个线程池执行此任务
            for item in dx_cookies:
                if item['status']:              
                    endDate=item['endDate']
                    strtime = time.strftime('%Y-%m-%d',time.localtime())
                    if strtime<endDate:
                        thread_pool.submit(distribute,[i,item])    #开启线程                
                        i+=1
                    else:                        
                        user = getUserName(item['ck'])
                        print(f'{user}:配置时间已过期')
                        msgg.append(f'{user}:配置时间已过期')
        size = len(dx_cookies)
        end = time.time()
        print(f'总共耗时:{int(end - start)}秒,共{size}条记录，运行{i-1}条')    
    except Exception as e:
        print(f'每日1G青龙脚本运行出错,{e}')
        msgg.append(f'每日1G青龙脚本运行出错,{e}')
    finally:
        #有异常则，发送青龙通知
        if sendNotify and os.path.exists("./sendNotify.js") and msgg:
            if not os.path.exists("./invoke_notify.js"):
                invoke_notify='''
                    const notify = require('./sendNotify.js');
                    const title = process.argv[2];
                    const content = process.argv[3];
                    notify.sendNotify(`${title}`, `${content}`);
                '''
                with open("./invoke_notify.js",'w',encoding='utf-8') as f:
                    f.write(invoke_notify)
                    f.close()
            content='\n'.join(msgg)            
            os.system(f"node invoke_notify.js '{title}' '{content}'")
            
#分发任务
def distribute(args):
    t=args[0]
    item=args[1]
    ck=item['ck']
    user = getUserName(ck)
    t = 10 if t>10 else t
    tt = random.randint(t*10,t*10+20)
    print(f'{args[0]}:{user};随机等待{tt}秒')    
    time.sleep(tt)
    #每日1G
    ret = getEveryDay1G(ck)    
    
    if ret : 
        #7天10G
        if item['sevenG']:
            getSummer10GActivityZg(ck)
        #特权签到
        if item['sign']:
            getGrowthTaskExperienceDetail(ck)    


#   提交7天10G
def submitModelOrder10G(userInfoId,customerNumber,ck,checkMd):
    user = getUserName(ck)
    url = 'https://vip.mini189.cn/yqt_fans/orders/submitModelOrder'
    headers={
        "User-Agent":"Mozilla/5.0 (Windows NT 6.1; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/105.0.0.0 Safari/537.36",    
        "Cookie":ck
    }    
    data_10Gbody={
        "credentialsType":"身份证",
        "applyCustomerModel":"true",
        "remark":"",
        "specStr1":"",
        "specStr2":"WAP",
        "productName":"280",
        "goodsName":"7天10GB流量包",
        "payType":"",
        "goodsPrice":"0",
        "userActivityStgId":"",
        "userActivityId":"1958",
        "businessType":"7",
        "orderType":"7",
        "jobNumber":"33000682",
        "userInfoId":userInfoId,
        "isPushZn":"1",
        "agreementTypeName":"增值业务",
        "userActivityIsVali":"1",
        "refer":"gzdxYLYHD-10G",
        "autoFillCustomer":"1",
        "customerNumber":customerNumber,
        "number":customerNumber,
        "activityInfoModel":"true",
        "agreeGw":"0",
        "mpopenid":"",
        "zwNum":"",
        "busiNo":"",
        "aiNum":"",
        "checkMd":checkMd        
    }
    response = requests.post(url=url,data=data_10Gbody,headers=headers)
    html_text = response.text;
    obj = re.compile(r'提交失败',re.S)
    m = obj.search(html_text)
    obj2 = re.compile(r'提交成功',re.S)
    m2 = obj2.search(html_text)
    if m:
        #结果
        sc1 = re.search('p10">(?P<result>.*?)</p>',html_text)
        #原因
        sc2 = re.search('mt10">(?P<reson>.*?)<',html_text)        
        if sc1 and sc2:            
            sc1s = sc1.group('result')
            sc2s = sc2.group('reson')
            print(f'{user}:7天10G:{sc1s}--->{sc2s}')
            msgg.append(f'{user}:7天10G:{sc1s}--->{sc2s}')
    elif m2:
        resp = re.search('</i>(?P<resp>.*?)！',html_text)
        order = re.search('fwb">(?P<order>.*?)</p>',html_text)
        tip = re.search('fcgray">(?P<tip>.*?)</p>',html_text)
        if resp and order and tip:            
            respt = resp.group('resp')
            ordert = order.group('order')
            tipt = tip.group('tip')
            print(f'{user}:7天10G:{respt}--->{ordert}--->{tipt}')
            msgg.append(f'{user}:7天10G:{respt}--->{ordert}--->{tipt}')
            

#查询7天10G资格
def getSummer10GActivityZg(ck):
    user = getUserName(ck)
    url = 'https://vip.mini189.cn/yqt_fans/activtiyById/getSummer10GActivityZg'
    headers={
        "User-Agent":"Mozilla/5.0 (Windows NT 6.1; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/105.0.0.0 Safari/537.36",    
        "Cookie":ck
    }
    jsont = requests.post(url=url,headers=headers).json()
    #logger.debug(jsont)
    if  jsont['status'] != 'success':
        print(f'{user}:查询7天10G资格异常,{jsont["msg"]}')
        msgg.append(f'{user}:查询7天10G资格异常,{jsont["msg"]}')
    else:
        if jsont['data']['accountList']:
           userInfoId= jsont['data']['userInfoId']  #获取用户ID
           customerNumber=jsont['data']['loginNumber']  #获取手机号
           checkMd=jsont['data']['accountMap'][customerNumber]  #获取校验码
           #print(f'{user}:查询7天10G,可以领取啦') 
           submitModelOrder10G(userInfoId,customerNumber,ck,checkMd)    #领取7天10G
        else:
            print(f'{user}:查询7天10G,暂不可领取')

#获取签到当前用户等级
def getUserVipGradeInfo(ck):
    user = getUserName(ck)
    url = 'https://vip.mini189.cn/yqt_fans/vipGradeExperienceInfo/getUserVipGradeInfo'
    headers={
        "User-Agent":"Mozilla/5.0 (Windows NT 6.1; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/105.0.0.0 Safari/537.36",    
        "Cookie":ck
    }
    jsont = requests.post(url=url,headers=headers).json()
    if jsont['status'] != 'success':
        print(f'{user}:获取特权签到异常,{jsont["msg"]}')
        print(jsont)
    else:
        currentGrade = jsont['data']['currentGrade']   #当前等级
        currentGradeContent = jsont['data']['currentGradeContent']  #当前称号
        currentMonthGrade = jsont['data']['currentMonthGrade']  #本月已获得
        total = jsont['data']['total']  #总经验
        nextGradeDifferExperience = jsont['data']['nextGradeDifferExperience']  #下一级还需
        nextGradeExperience = jsont['data']['nextGradeExperience']  #下一级经验
        print(f'{user}:特权签到,当前经验:{total},等级:V{currentGrade}级,称号:{currentGradeContent},下一级还需:{nextGradeDifferExperience},本月已获得:{currentMonthGrade}')
        msgg.append(f'{user}:特权签到,当前经验:{total},等级:V{currentGrade}级,称号:{currentGradeContent},下一级还需:{nextGradeDifferExperience},本月已获得:{currentMonthGrade}')
        
        
#特权签到
def receiveExperience(ck):
    user = getUserName(ck)
    url = 'https://vip.mini189.cn/yqt_fans/vipGradeExperienceDetail/receiveExperience?type=1'
    headers={
        "User-Agent":"Mozilla/5.0 (Windows NT 6.1; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/105.0.0.0 Safari/537.36",    
        "Cookie":ck
    }
    jsont = requests.post(url=url,headers=headers).json()
    #logger.debug(jsont)
    if jsont['status'] != 'success':
        print(f'{user}:特权签到异常,{jsont["msg"]}')
        print(jsont)
    else:
        exp = jsont['data']['currentGiftExp']
        print(f'{user}:特权签到成功！获得{exp}经验')
        msgg.append(f'{user}:特权签到成功！获得{exp}经验')
    
#获取签到信息
def getGrowthTaskExperienceDetail(ck):
    user = getUserName(ck)
    url = 'https://vip.mini189.cn/yqt_fans/vipGradeExperienceDetail/getGrowthTaskExperienceDetail'
    headers={
        "User-Agent":"Mozilla/5.0 (Windows NT 6.1; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/105.0.0.0 Safari/537.36",    
        "Cookie":ck
    }
    jsont = requests.post(url=url,headers=headers).json()
    if jsont['status'] != 'success':
        print(f'{user}:获取特权签到异常,{jsont["msg"]}')        
        print(jsont)        
    else:
        if not jsont['data']['isSign']:
            receiveExperience(ck) #签到
        else:
            print(f'{user}:特权签到,今日已签到')
        getUserVipGradeInfo(ck)   #签到完成后再查询结果




#每日1G提交
def submitOrder1G(userInfoId,customerNumber,ck):
    user = getUserName(ck)
    url = 'https://vip.mini189.cn/yqt_fans/orders/submitModelOrder'
    submit_body={
        "credentialsType":"身份证",
        "applyCustomerModel":"true",
        "remark":"roadType\":\"undefined",
        "specStr1":"",
        "specStr2":"WAP",
        "productName":"2703",
        "goodsName":"0元1G国内流量日包201511",
        "payType":"",
        "goodsPrice":"0",
        "userActivityStgId":"",
        "userActivityId":"1908",
        "businessType":"7",
        "orderType":"7",
        "jobNumber":"33000682",
        "userInfoId":userInfoId,
        "isPushZn":"1",
        "agreementTypeName":"增值业务",
        "userActivityIsVali":"1",
        "refer":"mr1G-qwyrym",
        "autoFillCustomer":"1",
        "customerNumber":customerNumber,
        "number":customerNumber,
        "activityInfoModel":"true",
        "couponId":""
    }
    headers={
        "User-Agent":"Mozilla/5.0 (Windows NT 6.1; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/105.0.0.0 Safari/537.36",    
        "Cookie":ck
    }
    response = requests.post(url=url,data=submit_body,headers=headers)
    html_text = response.text;
    obj = re.compile(r'提交失败',re.S)
    m = obj.search(html_text)
    obj2 = re.compile(r'提交成功',re.S)
    m2 = obj2.search(html_text)
    if m:
        #结果
        sc1 = re.search('p10">(?P<result>.*?)</p>',html_text)
        #原因
        sc2 = re.search('mt10">(?P<reson>.*?)<',html_text)        
        if sc1 and sc2:            
            sc1s = sc1.group('result')
            sc2s = sc2.group('reson')
            print(f'{user}:每日1G,{sc1s}--->{sc2s}')
            msgg.append(f'{user}:每日1G,{sc1s}--->{sc2s}')
    elif m2:
        resp = re.search('</i>(?P<resp>.*?)！',html_text)
        order = re.search('fwb">(?P<order>.*?)</p>',html_text)
        tip = re.search('fcgray">(?P<tip>.*?)</p>',html_text)
        if resp and order and tip:            
            respt = resp.group('resp')
            ordert = order.group('order')
            tipt = tip.group('tip')
            print(f'{user}:每日1G,{respt}--->{ordert}--->{tipt}')
            msgg.append(f'{user}:每日1G,{respt}--->{ordert}--->{tipt}')
            


#每日1G查询
def getEveryDay1G(ck):
    user = getUserName(ck)
    url = 'https://vip.mini189.cn/yqt_fans/activtiyById/getEveryDay1G'
    headers={
        "User-Agent":"Mozilla/5.0 (Windows NT 6.1; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/105.0.0.0 Safari/537.36",    
        "Cookie":ck
    }
    jsont = requests.post(url=url,headers=headers).json()
    if  jsont['status'] != 'success':
        print(f'{user}:出现异常,{jsont["msg"]}')
        msgg.append(f'{user}:出现异常,{jsont["msg"]}')
        print(jsont)
    else:
        if jsont['data']['accountList']:
           userInfoId= jsont['data']['userInfoId']
           customerNumber=jsont['data']['loginNumber']           
           submitOrder1G(userInfoId,customerNumber,ck)  #提交每日1G
           return True  #成功领取1G并触发其他任务
        else:
            print(f'{user}:每日1G暂不可领取,可能今日已领取过')
    return False


def getUserName(ck):    
    global dx_cookies
    for item in dx_cookies:
        if item['ck'] == ck:
            if 'user' in item.keys():
                if item['user'].strip():     
                    return item['user'].strip()
    return ck[11:22]

if __name__ == '__main__':
    main()
