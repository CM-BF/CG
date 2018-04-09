# 爬取bilibili中Dynamic

标签（空格分隔）： GET json scrapy fiddler

---

>使用scrapy框架，fiddler抓包工具，分析bilibili动态加载json内容，爬取并且解析出内容。

**分析后得出的有用部分的url，与headers如下图：**
![image_1cakqahqn1v8l1p85khj1iaqdq99.png-28.3kB][1]

如上图构造出request发送请求。
接着分析json内容：
![image_1cakqidjv185bgsp3fif4k1ier1p.png-15.7kB][2]
![image_1cakqjcmq1r693b4mgt1ah4klf26.png-14.5kB][3]
分析结束后进行json解析提取内容，最终得到内容：
```python
def parse(self, response):
    filename = 'dynamic.txt'
    with codecs.open(filename, 'w', 'utf-8') as f:
        for x in range(50):
            f.write('type:' + json.loads(response.body)['data']['archives'][x]['tname'] + '\n')
            f.write('owner:' + json.loads(response.body)['data']['archives'][x]['owner']['name'] + '\n')
            f.write(json.loads(response.body)['data']['archives'][x]['desc'] + '\n')
            f.write(json.loads(response.body)['data']['archives'][x]['dynamic'] + '\n')
            f.write(json.loads(response.body)['data']['archives'][x]['title'] + '\n\n--------------------------------------------\n')
    self.log('Saved file %s' % filename)
```
提取出内容，结束。
![image_1cakr8gaakmo1a7ql53p7ltkf4j.png-34.4kB][4]


  [1]: http://static.zybuluo.com/Citrine/rywnxrbd2u4g8bqalakt7xnf/image_1cakqahqn1v8l1p85khj1iaqdq99.png
  [2]: http://static.zybuluo.com/Citrine/e66t371nbkus7ppbzgs4mmni/image_1cakqidjv185bgsp3fif4k1ier1p.png
  [3]: http://static.zybuluo.com/Citrine/kczkdk3sdnl1ra8zb362w1x4/image_1cakqjcmq1r693b4mgt1ah4klf26.png
  [4]: http://static.zybuluo.com/Citrine/17v8nc3t0fcgut9n2aq54ksk/image_1cakr8gaakmo1a7ql53p7ltkf4j.png