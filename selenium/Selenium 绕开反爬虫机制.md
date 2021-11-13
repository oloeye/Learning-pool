## 1、mitproxy 拦截请求 √

本质上就是在响应中利用 mitproxy 将包含的 webdriver 的 js 中的关键字替换成其他的字符
1. 给本机设置代理 ip 127.0.0.1 端口 8001（为了让所有流量走 mitmproxy）具体方法请百度。
2. 启动 mitmproxy。

windows：
`mitmdump -p 8001`

3. 打开 chrome 的开发者工具，查各个.js 文件，是否存在 driver 字样，最终找到需要的.js 文件。

4. 干扰脚本
```python
def response(flow):
    if '/js/yoda.' in flow.request.url:
    for webdriver_key in ['webdriver', '__driver_evaluate', '__webdriver_evaluate', '__selenium_evaluate', '__fxdriver_evaluate', '__driver_unwrapped', '__webdriver_unwrapped', '__selenium_unwrapped', '__fxdriver_unwrapped', '_Selenium_IDE_Recorder', '_selenium', 'calledSelenium', '_WEBDRIVER_ELEM_CACHE', 'ChromeDriverw', 'driver-evaluate', 'webdriver-evaluate', 'selenium-evaluate', 'webdriverCommand', 'webdriver-evaluate-response', '__webdriverFunc', '__webdriver_script_fn', '__$webdriverAsyncExecutor', '__lastWatirAlert', '__lastWatirConfirm', '__lastWatirPrompt', '$chrome_asyncScriptInfo', '$cdc_asdjflasutopfhvcZLmcfl_' ]:
    ctx.log.info('Remove "{}" from {}.'.format(
    webdriver_key, flow.request.url
    ))
    flow.response.text = flow.response.text.replace('"{}"'.format(webdriver_key), '"NO-SUCH-ATTR"')
    flow.response.text = flow.response.text.replace('t.webdriver', 'false')
    flow.response.text = flow.response.text.replace('ChromeDriver', '')
```

5. 退出刚才的 mitmproxy 状态，重新用命令行启动 mitmproxy 干扰脚本 监听 8001 端口的请求与响应。
mitmdump -s DriverPass.py -p 8001

## 2、修改源码

修改 js/call_function.js,129 行。
```js
    var doc = opt_doc || document;
    var key = '$cdc_asdjflasutopfhvcZLmcfl_';
    if (!(key in doc))
    doc[key] = new Cache();
    return doc[key];
}
```
–> 修改后
``` js
function getPageCache(opt_doc) {
    var doc = opt_doc || document;
    var key = ‘$bobo_zhangyx_';
    if (!(key in doc))
    doc[key] = new Cache();
    return doc[key];
}
```
经测试似乎已失效，可能由于版本迭代问题。

## 3、手动打开跑程序√

cmd 运行命令
chrome.exe --remote-debugging-port=9222
打开一个浏览器，然后 py 代码里
chrome_options.add_experimental_option("debuggerAddress", "127.0.0.1:9222")

## 4、换 V63 之前的 chrome 与 driver

(似乎也是个好方法但是没有尝试）