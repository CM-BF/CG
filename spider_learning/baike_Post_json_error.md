#scrapy构造Post获取百科词条json信息，及error

标签（空格分隔）： scrapy POST json 陷阱
作者：Citrine
---

###分析信息
获取url：`https://baike.baidu.com/wikitag/api/getlemmas`
获取headers：
```
Host: baike.baidu.com
Connection: keep-alive
Content-Length: 91
Accept: application/json, text/javascript, */*; q=0.01
Origin: https://baike.baidu.com
X-Requested-With: XMLHttpRequest
User-Agent: Mozilla/5.0 (Windows NT 6.1; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/63.0.3239.132 Safari/537.36
Content-Type: application/x-www-form-urlencoded; charset=UTF-8
Referer: https://baike.baidu.com/wikitag/taglist?tagId=76607
Accept-Encoding: gzip, deflate, br
Accept-Language: zh-CN,zh;q=0.9
```
获取Request body：
```
limit=24&timeout=3000&filterTags=%5B%5D&tagId=76607&fromLemma=false&contentLength=40&page=4
```
其中有用的body只有limit，tagId，page。
而headers，全部可以省略。。。
###代码
```python
class QuotesSpider(scrapy.Spider):
    name = "baike"

    def start_requests(self):
        url = 'https://baike.baidu.com/wikitag/api/getlemmas'
        for page in range(51):
            data = {'limit': '100',
                    'tagId': '76607',
                    'page': str(page)}
            yield scrapy.FormRequest(url=url, callback=self.parse, dont_filter=True, formdata=data)

    def parse(self, response):
        print('I am here!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!')
        filename = 'scibaike.txt'
        if(int(json.loads(response.body)['page']) == 0):
            with codecs.open(filename, 'w', 'utf-8') as f:
                for x in range(100):
                    f.write('Title: ' + json.loads(response.body)['lemmaList'][x]['lemmaTitle'] + '\n')
                    f.write('Desc: ' + json.loads(response.body)['lemmaList'][x]['lemmaDesc'] + '\n---------------------------------------\n')
            self.log('Saved file %s' % filename)
        else:
            with codecs.open(filename, 'a', 'utf-8') as f:
                for x in range(100):
                    f.write('Title: ' + json.loads(response.body)['lemmaList'][x]['lemmaTitle'] + '\n')
                    f.write('Desc: ' + json.loads(response.body)['lemmaList'][x]['lemmaDesc'] + '\n---------------------------------------\n')
            self.log('Saved file %s' % filename)
```
###结果
```
Title: 双系统
Desc: 双系统在安装的时候，两个系统是分别装在不同的分区内，后安装的系统不会覆盖前一个系...
---------------------------------------
Title: 电路
Desc: 电路：由金属导线和电气、电子部件组成的导电回路，称为电路。在电路输入端加上电源使...
---------------------------------------
Title: 存储卡
Desc: SD卡存储卡，是用于手机、数码相机、便携式电脑、MP3和其他数码产品上的独立存储...
---------------------------------------
Title: 电力线通信
Desc: 电力线通信（Power Line Communication，英文简称PLC）技...
---------------------------------------
Title: CIH病毒
Desc: CIH病毒是一种能够破坏计算机系统硬件的恶性病毒。这个病毒产自台湾，原集嘉通讯公...
---------------------------------------
Title: 波长
Desc: 波长（wavelength）是指波在一个振动周期内传播的距离。也就是沿着波的传播...
---------------------------------------
Title: 音频
Desc: 音频是个专业术语，音频一词已用作一般性描述音频范围内和声音有关的设备及其作用。人...
```
爬的速度在12秒100个左右，速度还是挺慢的233333

###陷阱
第一点`def start_requests(self):`一开始打成了`def start_request(self):`
不仔细看还看不出，少了一个s，但是scrapy默认执行的函数是必须加s的，所以即使只是错个函数名就会造成spider的执行为空。
第二点：with as 后需要空行减格，以结束with，关闭文件


