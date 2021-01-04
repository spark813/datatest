---
layout: post
title:  "selenium+python常用代码块"
author: "数字"
comments: true
tags: 自动化测试
---

## selenium常用代码块
[toc]
### Selenium环境搭建
- Client
    - Python（IDE：Visual studio Code，安装Python插件）
- Driver（下载地址：https://www.seleniumhq.org/download/）(版本对应：http://chromedriver.storage.googleapis.com/index.html)
    - Chrom -> Chrome
    - Firefox -> FirefoxDriver
    - IE -> InternetExplorerDriver
- Selenium（下载地址：https://www.seleniumhq.org/）
    - Python安装方式：pip install selenium
- Python开发工具
    - Visual Studio Code
- 测试环境搭建成功的代码
    1. 把Driver放到python安装目录
    2. 新建start_browser.py文件，添加如下代码，运行，能够打开浏览器并访问网址就表示成功
        ```
        #coding=utf-8
        from selenium import webdriver
        import time
        driver = webdriver.Chrome()
        driver.get("http://www.baidu.com")
        time.sleep(5)
        driver.close()
        ```

### 启动不同浏览器
1. 把对应的浏览器驱动，放进python安装目录中
2. 使用webdriver下面的方法加载浏览器
    ```
    Driver=webdriver.Chrome()
    Driver=webdriver.Firefox()
    Driver=webdriver.le()
    Driver=webdriver.Edge()
    ```

### 不同方式定位元素
```
#coding=utf-8
from selenium import webdriver
from selenium.webdriver.support import expected_conditions as EC
import time
driver = webdriver.Chrome()
driver.get("http://mall.v1qqfh.top/user-register.html")
driver.find_element_by_id("username").send_keys('seleniumTEST')
driver.find_elements_by_class_name("user-content")[1].send_keys("selenium")
driver.find_element_by_xpath("//*[@id=\"phone\"]").send_keys("13012341234")
time.sleep(5)
driver.close()
```

### 查找页面是否包含指定元素
- 查找页面是否包含class为“user-content"的元素，搜索10秒钟。
    ```
    #coding=utf-8
    from selenium import webdriver
    from selenium.webdriver.support import expected_conditions as EC
    from selenium.webdriver.support.wait import WebDriverWait
    from selenium.webdriver.common.by import By
    driver = webdriver.Chrome()
    driver.get("http://mall.v1qqfh.top/user-register.html")
    #查看元素是否存在
    locator = (By.CLASS_NAME,"user-content")
    result = WebDriverWait(driver,10).until(EC.visibility_of_all_elements_located(locator))
    print(result)
    driver.close()
    ```

### 获取元素的值
- 可以使用element的get_attribute获取对应属性名的值。
    ```
    #coding=utf-8
    from selenium import webdriver
    driver = webdriver.Chrome()
    driver.get("http://mall.v1qqfh.top/user-register.html")
    username = driver.find_element_by_id("username")
    print(username.get_attribute("placeholder"))
    username.send_keys("qqfh")
    print(username.get_attribute("value"))
    driver.close()
    ```

### 生成随机用户名
- 通过random实现
    ```
    #coding=utf-8
    from selenium import webdriver
    import random
    import time
    driver = webdriver.Chrome()
    driver.get("http://mall.v1qqfh.top/user-register.html")
    #生成随机用户名，5位随机
    demo_email = "".join(random.sample('1234567890abcdefghijklmn',5))+"@163.com"
    username = driver.find_element_by_id("email").send_keys(demo_email)
    time.sleep(5)
    driver.close()
    ```

### 获取验证码图片
- 需要安装PIL库，可以使用命令``pip install pillow``进行安装
- 通过PIL库实现对图片的裁减
    ```python
    #coding=utf-8
    from selenium import webdriver
    import time
    from PIL import Image
    driver = webdriver.Chrome()
    driver.get("http://admin.leadscar.cn/application/modules/Lzadmin/login.html")
    driver.maximize_window()
    #保存整个页面
    driver.save_screenshot("G:/CESHI/test/selenium/img/getcode.png")
    code_element = driver.find_element_by_class_name("send-checkcode")
    #获取元素的定位
    print(code_element.location)
    left = code_element.location['x']
    top = code_element.location['y']
    right = code_element.size['width']+left
    height = code_element.size['height']+top
    #获取图片并裁减
    im = Image.open("G:/CESHI/test/selenium/img/getcode.png")
    img = im.crop((left,top,right,height))
    img.save("G:/CESHI/test/selenium/img/code.png")
    time.sleep(5)
    driver.close()
    ```

### 通过pytesseract解析验证码
- 安装解析工具``pip install pytesseract``
- 下载exe文件，https://digi.bib.uni-mannheim.de/tesseract/，安装tesseract。
- 如遇到报错``tesseract is not installed or it's not in your path``则在pytesseract.py文件中找到“tesseract_cmd”，更改为：
    ```
    tesseract_cmd = r'D:\Program Files1\Tesseract-OCR\tesseract.exe'
    ```
- 示例代码：
    ```
    #coding=utf-8
    import pytesseract
    from PIL import Image
    image = Image.open("G:/CESHI/test/selenium/img/code.png")
    text = pytesseract.image_to_string(image)
    print(text)
    ```

### 通过showapi（商用）解析验证码
- 帮助文档https://www.showapi.com/api/view/184
    ```
    # python3.6.5
    # 需要引入requests包 ：运行终端->进入python/Scripts ->输入：pip install requests
    from ShowapiRequest import ShowapiRequest
    
    r = ShowapiRequest("http://route.showapi.com/184-5","my_appId","my_appSecret" )
    r.addBodyPara("img_base64", "")
    r.addBodyPara("typeId", "34")
    r.addBodyPara("convert_to_jpg", "0")
    r.addBodyPara("needMorePrecise", "0")
    r.addFilePara("image", r"C:\Users\showa\Desktop\使用过的\4.png") #文件上传时设置
    res = r.post()
    print(res.text) # 返回信息
    ```

### 读取配置文件
1. 新建配置文件“LocalElment.ini”，内容为：
    ```
    [qqfhElement]
    user_name=id>username
    user_password=id>password
    user_email=id>email
    ```
2. 编写python代码：
    ```
    #coding=utf-8
    #如果是pytho2则需要改为import Configparser
    import configparser
    cf = configparser.ConfigParser()
    cf.read(r"G:/CESHI/test/selenium/LocalElement.ini")
    text = cf.get("qqfhElement","user_name")
    print(text)
    ```

### unittest基础使用（前置、后置、容器、断言）
```
#coding=utf-8
import unittest
class Demo_Unittest(unittest.TestCase):

    #全部case的前置
    @classmethod
    def setUpClass(cls):
        print("全部的前置")
    
    #全部case的后置
    @classmethod
    def tearDownClass(cls):
        print("全部的后置")

    #case前置
    def setUp(self):
        print("case前置")

    #case后置
    def tearDown(self):
        print("case后置")

    def testOne(self):
        print("------------第一个测试------------")

    def testTwo(self):
        print("------------第二个测试------------")
        self.assertFalse(Demo_Unittest.getTrue)

    def testThree(self):
        print("------------第三个测试------------")
        self.assertTrue(Demo_Unittest.getTrue)

    @unittest.skip("跳过第四个case")
    def testFour(self):
        print("------------第四个测试------------")

    def testFive(self):
        print("------------第五个测试------------")

    def getTrue(self):
        return True

    def getFalse(self):
        return False

if __name__ == "__main__":
    #unittest.main()
    #使用容器的方式执行case
    suite = unittest.TestSuite()
    twocase = [Demo_Unittest('testTwo'),Demo_Unittest('testThree'),Demo_Unittest('testFour')]
    suite.addTests(twocase)
    suite.addTest(Demo_Unittest('testFive'))
    unittest.TextTestRunner().run(suite)
```

### 通过unittest执行多个文件的case
```
#coding=utf-8
import unittest
import os
class Run_Case(unittest.TestCase):
        def test_run1(self):
            os_path = os.getcwd()
            suite = unittest.defaultTestLoader.discover(os_path,pattern="demo_unit*")
            unittest.TextTestRunner().run(suite)

if __name__ == "__main__":
    unittest.main()
```

### 使用HTMLTestRunner生成测试报告
- 访问http://tungwaiyip.info/software/HTMLTestRunner.html下载HTMLTestRunner.py文件
- 把下载的文件放到Python的Lib目录下
- 如果是python3，需要修改HTMLTestRunner.py文件中的内容，可以参照疑“难杂症六”解决（因为该文件是python2的代码所写）
- HTMLTestRunner使用方法：
    1. 在项目目录下新建report目录，新建case_report.html文件，内容为空
    2. 测试代码如下：
        ```
        #coding=utf-8
        import unittest
        import os
        import HTMLTestRunner
        class Demo_Unittest(unittest.TestCase):
        
            def testOne(self):
                self.assertTrue(False)
        
            def testTwo(self):
                self.assertTrue(True)
        
            def testThree(self):
                self.assertTrue(False)
        
            def testFour(self):
                self.assertTrue(False)
        
            def testFive(self):
                self.assertTrue(False)
        
        if __name__ == "__main__":
            suite = unittest.TestSuite()
            cases = [Demo_Unittest('testTwo'),Demo_Unittest('testThree'),Demo_Unittest('testFour')]
            suite.addTests(cases)
            suite.addTest(Demo_Unittest('testFive'))
            # unittest.TextTestRunner().run(suite)
            #生成报告
            report_path = os.path.join(os.getcwd()+"/report/"+"case_report.html")
            # #获取流媒体
            f = open(report_path,'wb')
            runner = HTMLTestRunner.HTMLTestRunner(stream=f,title="qqfh测试报告",verbosity=2)
            runner.run(suite)
        ```

### case执行失败自动截图
1. python2使用法法
    ```
    if sys.exc_info()[0]:
        ...截图操作...
    ```
2. python3使用方法
    ```
    #coding=utf-8
    from selenium import webdriver
    import unittest
    import os
    import HTMLTestRunner
    import time
    class Demo_ErrorScreenshot(unittest.TestCase):
    
        def tearDown(self):
            time.sleep(2)
            for method_name,error in self._outcome.errors:
                if error:
                    #case方法名
                    case_name = self._testMethodName
                    file_path = os.path.join(os.getcwd()+"/report/"+case_name+".png")
                    self.driver.save_screenshot(file_path)
            self.driver.close()
    
        def setUp(self):
            self.driver = webdriver.Chrome()
            self.driver.get("http://www.baidu.com/")
    
        @unittest.skip("跳过")
        def testOne(self):
            self.assertTrue(False)
    
        @unittest.skip("跳过")
        def testTwo(self):
            self.assertTrue(True)
    
        @unittest.skip("跳过")
        def testThree(self):
            self.assertTrue(False)
    
        @unittest.skip("跳过")
        def testFour(self):
            self.assertTrue(False)
    
        def testFive(self):
            self.driver.find_element_by_id("kw").send_keys("testFive")
            time.sleep(2)
            self.assertTrue(False)
    
    if __name__ == "__main__":
        suite = unittest.TestSuite()
        # cases = [Demo_Unittest('testTwo'),Demo_Unittest('testThree'),Demo_Unittest('testFour')]
        # suite.addTests(cases)
        suite.addTest(Demo_ErrorScreenshot('testFive'))
        # unittest.TextTestRunner().run(suite)
        #生成报告
        report_path = os.path.join(os.getcwd()+"/report/"+"case_report.html")
        # #获取流媒体
        f = open(report_path,'wb')
        runner = HTMLTestRunner.HTMLTestRunner(stream=f,title="qqfh测试报告",verbosity=2)
        runner.run(suite)
        # unittest.main()
    ```

### 数据驱动简单实现
1. 使用``pip install ddt``安装数据驱动
2. 编写测试代码
    ```
    #coding=utf-8
    import ddt
    import unittest
    @ddt.ddt
    class DataTest(unittest.TestCase):
    
        @ddt.data(
            [1,2],
            [3,4],
            [5,6]
        )
    
        @ddt.unpack
        def test_add(self,a,b):
            print(a+b)
    
    if __name__ == "__main__":
        unittest.main()
    ```

### 通过excel文件获取、写入数据，实现数据驱动
1. 新建data.xls文件，内容每一列为对应的数据
2. 安装xlrd用于处理excel文件，``pip install xlrd``
3. 编写excel处理工具
    ```
    #coding=utf-8
    import xlrd
    from xlutils.copy import copy
    class ExcelUtil:
        #初始化数据，获取文件中的页面
        def __init__(self,excel_path_c=None,index_c=None):
            if excel_path_c == None:
                self.excel_path = "G:/CESHI/test/selenium/data/data.xls"
            else:
                self.excel_path = excel_path_c
            if index_c == None:
                self.index = 0
            else:
                self.index = index_c
            #打开excel文件
            self.data = xlrd.open_workbook(self.excel_path)
            #第index张表
            self.table = self.data.sheets()[self.index]
    
        #获取页面全部数据，返回list
        def getAllDatas(self):
            result = []
            rows = self.getLines()
            if rows != None:
                for i in range(rows):
                    col = self.table.row_values(i)
                    result.append(col)
                return result
            return None
    
        #获取行数
        def getLines(self):
            rows = self.table.nrows
            return rows
    
        #获取指定单元格的数据
        def getData(self,row,col):
            if self.getLines() > row:
                data = self.table.cell(row,col).value
                return data
            return None
        
        #写入数据
        def writeValue(self,row,value):
            read_value = xlrd.open_workbook(self.excel_path)
            write_data = copy(read_value)
            write_data.get_sheet(self.index).write(row,9,value)
            write_data.save(self.excel_path)
    
    if __name__ == "__main__":
        eu = ExcelUtil()
        print(eu.writeValue(3,"test"))
    ```
4. 编写测试用例，加入excel获取的数据，实现数据驱动
    ```
    #codint=utf-8
    import ddt
    import unittest
    import sys
    sys.path.append("G:\\CESHI\\test\\selenium")
    from demo_excel_util import ExcelUtil
    eu = ExcelUtil()
    data = eu.getdata() 
    @ddt.ddt
    class demo_Excel_Data(unittest.TestCase):
    
        @ddt.data(*data)
        def test_add(self,data):
            a,b = data
            print(a+"~测试~"+b)
    
    if __name__ == "__main__":
        unittest.main()
    ```

### 使用logging记录操作日志
1. 新建日志处理logUtil.py文件
    ```
    #coding=utf-8
    import logging
    import os
    import datetime
    class UserLog:
        def __init__(self):
            #生成日志文件地址
            base_dir = os.path.dirname(os.path.abspath(__file__))
            log_dir = os.path.join(base_dir,"logs")
            log_file = datetime.datetime.now().strftime("%Y-%m-%d")+".log"
            log_name = log_dir+"/"+log_file
    
            #创建日志对象
            self.logger = logging.getLogger()
            #设置logging级别
            self.logger.setLevel(logging.DEBUG)
    
            #获取控制台流
            self.consle = logging.StreamHandler()
            self.logger.addHandler(self.consle)
    
            #获取文件流
            self.file_handle = logging.FileHandler(log_name,mode='a',encoding='utf-8')
            #设置文件流格式
            formatter = logging.Formatter('%(asctime)s %(filename)s --> %(funcName)s    %(levelno)s:%(levelname)s --> %(message)s')
            self.file_handle.setFormatter(formatter)
            self.logger.addHandler(self.file_handle)
    
    
        def get_log(self):
            self.logger.debug("获取日志操作对象")
            return self.logger
    
        def close_log(self):
            self.logger.debug('关闭日志操作对象')
            #关闭控制台流
            self.logger.removeHandler(self.consle)
            self.consle.close()
            #关闭文件流
            self.logger.removeHandler(self.file_handle)
            self.file_handle.close()
    ```
2. 在其他需要使用日志功能的文件中，加入日志代码：
    ```
    from keywordselenium.base.logUtil import UserLog
    logger = UserLog()
    log = logger.get_log()
    log.debug('开始执行第%s条用例',i)
    logger.close_log()
    ```

### 使用jenkins持续集成
1. 下载msi包或者java的war包，msi直接安装即可，war包执行以下命令：
    ```
    java -jar d:\java\jenkins\jenkins.war
    ```
2. 访问http://127.0.0.1:8080，打开jenkins首页，根据提示安装。
3. 配置完成后，进入主页。
4. 构建新项目
5. 在“构建”中选择Execute Windows batch command，表示执行windows命令，输入以下内容：
    ```
    python d:\test\case\keywordCase.py
    ```
6. 在“General”中找到“高级”按钮，点击展开后，勾选“使用自定义的工作空间”，然后在目录窗口中，填写工作目录路径：
    ```
    d:\test\case
    ```
7. 保存，点击立即构建即可。

### Jenkins邮箱配置
1. 系统管理-系统设置-邮件通知
2. 填写邮箱相关信息，例如：
```
SMTP服务器：smtp.163.com
用户默认邮件后缀：@163.com
勾选-使用SMTP认证
用户名：xxxxx@163.com
密码:xxxxxx
SMTP端口：25
勾选-通过发送测试邮件测试配置
```
3. 进入某个项目的配置页面
4. 在“构建后操作”中选择“E-mail Notification”，输入目标邮箱即可。

### Jenkins定时任务
1. 进入某个项目的配置页面
2. 找到“构建触发器”，勾选“定时构建”
3. 加入需要定时执行的内容即可
    ```
    分 时 日 月 星期
    1 * * * * #每分钟执行一次
    */15 * * * * #每15分钟执行一次
    * * 2 * * #每月2号执行一次
    * 23 * * * #每天23点执行一次
    ```




### 疑难杂症
#### 一、Visual Studio Code终端中文乱码问题
在终端显示乱码

**解决方法：**
1. 打开cmd
2. 通过``chcp``查看原有编码，默认是936，既代表GBK2312
3. 执行``chcp 65001``更改为utf-8
4. 搞定

#### 二、使用pip安装软件太慢
使用pip安装软件太慢

**解决方法：**
- 国内源
    ```
    新版ubuntu要求使用https源，要注意。
    清华：https://pypi.tuna.tsinghua.edu.cn/simple
    阿里云：http://mirrors.aliyun.com/pypi/simple/
    中国科技大学 https://pypi.mirrors.ustc.edu.cn/simple/
    华中理工大学：http://pypi.hustunique.com/
    山东理工大学：http://pypi.sdutlinux.org/ 
    豆瓣：http://pypi.douban.com/simple/
    ```
- 可以在使用pip的时候加参数-i https://pypi.tuna.tsinghua.edu.cn/simple
    ```
    例如：pip install -i https://pypi.tuna.tsinghua.edu.cn/simple pyspider
    ```
- 永久修改：
    ```
    修改 ~/.pip/pip.conf (没有就创建一个文件夹及文件。文件夹要加“.”，表示是隐藏文件夹)
    内容如下：
    
    [global]
    index-url = https://pypi.tuna.tsinghua.edu.cn/simple
    [install]
    trusted-host=mirrors.aliyun.com
    
    windows下，直接在user目录中创建一个pip目录，如：C:\Users\xx\pip，新建文件pip.ini。内容同上。
    ```

#### 三、pip升级时报错--- No module named 'pip._internal'

**解决方案：**
- cmd下，输入如下命令（强制安装3.4版本pip）：
    ```
    curl https://bootstrap.pypa.io/get-pip.py -o get-pip.py
    python get-pip.py --force-reinstall
    ```

#### 四、Python3安装PIL出错

**解决方案：**
- 使用以下命令安装即可（若无法安装，请执行疑难杂症三中的命令，先安装对应版本的pip即可。
    ```
    python -m pip install -i http://mirrors.aliyun.com/pypi/simple/ Pillow
    ```

#### 五、使用pytesseract报错：tesseract is not installed or it's not in your path
使用pytesseract时，运行程序报错``tesseract is not installed or it's not in your path``

**解决方法：**
1. 需要安装tesseract，可以在https://digi.bib.uni-mannheim.de/tesseract/ 找对应版本安装
2. 在pytesseract.py文件中找到“tesseract_cmd”，更改为（地址为tesseract安装的目录）：
    ```
    tesseract_cmd = r'D:\Program Files1\Tesseract-OCR\tesseract.exe'
    ```

#### 六、初次使用HTMLTestRunner报错
在Python3环境下初次使用HTMLTestRunner报错。

**解决方法：**
1. 因为HTMLTestRunner.py是python2时所写，python2的一些代码和python3不同，需要更改HTMLTestRunner.py文件中的内容：
2. 631行：``print >>sys.stderr, '\nTime Elapsed: %s' % (self.stopTime-self.startTime)``更换成``print (sys.stderr, '\nTime Elapsed: %s' % (self.stopTime-self.startTime)) ``
3. 642行：``if not rmap.has_key(cls): ``更换成 ``if not cls in rmap:``
4. 766行：``uo = o.decode('latin-1')``可以暂时不更换
5. 772行：``ue = e.decode('latin-1')``更换成``ue = e``
6. 778行：``escape(uo+ue)``更换成``escape(str(uo)+ue)``

