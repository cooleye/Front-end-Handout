## 爬虫1.0 版本

axios + cheerio

cheerio: 
```
// nodejs 版的jQuery
const cheerio = require('cheerio');   
const axios = require('axios');
const fs = require('fs');

// 访问目标网站
axios.get('http://jandan.net/')
.then((result) => {
    const $ = cheerio.load(result.data); 
    parse($)
}).catch((err) => {
    console.log(err) 
});

// 解析网站内容
function parse($){
    let str = [];
    $(".list-post").each((i,item)=>{
       let src =  $(item).find('.thumbs a img').attr("src");
       let title = $(item).find('.indexs h2 a').text()
       let obj = {src,title}
       str.push(obj)
    })

    let data = {
        data: str
    }

    // 把数据保存在文件中
    fs.writeFileSync(__dirname + '/data.txt',JSON.stringify(data))
}

```

## 爬虫 2.0 版本

#### Puppeteer 是什么？
Puppeteer 是 Google Chrome 团队官方的无界面（Headless）Chrome 工具。

##### 文档： https://zhaoqize.github.io/puppeteer-api-zh_CN/#/
##### Github： https://github.com/GoogleChrome/puppeteer

#### Puppeteer 可以做什么？
Puppeteer 可以做的事情有很多，包括但不限于：

* 利用网页生成 PDF、图片
* 可以从网站抓取内容
* 自动化表单提交、UI 测试、键盘输入等
* 帮你创建一个最新的自动化测试环境（chrome），可以直接在此运行测试用例
* 捕获站点的时间线，以便追踪你的网站，帮助分析网站性能问题


#### Puppeteer 有什么优势？
相对于真实浏览器而言，少了加载 css，js 以及渲染页面的工作。无头浏览器要比真实浏览器快的多。
可以在无界面的服务器或 CI 上运行，减少了外界的干扰，更稳定。
在一台机器上可以模拟运行多个无头浏览器，方便进行并发运行

#### 安装 Puppeteer
```
npm i --save puppeteer
```

#### 简单示例
```
const puppeteer = require("puppeteer");

const getScreenShot = async () => {
  const browser = await puppeteer.launch({ headless: false });
  const page = await browser.newPage();
  await page.goto("https://baidu.com");
  await page.screenshot({ path: "baidu.png" });

  await browser.close();
};

getScreenShot();
```
这段代码的意思就是以 headless（无头）模式打开浏览器，然后打开一个新标签页，跳转到百度网址， 并且进行屏幕截图，保存为 baidu.png 为名的图片，最后关闭浏览器。

#### 浏览器操作

```
const browser = await puppeteer.launch(); // 打开浏览器
const page = await browser.newPage(); // 打开新tab页
await page.goto("https://image.baidu.com"); // 跳转到百度图片

console.log("go to https://image.baidu.com"); 

await page.focus("#kw"); // 把焦点定位到搜索input框
await page.keyboard.sendCharacter("重庆"); // 输入关键字
await page.click(".s_search"); // 点击搜索按钮
console.log("go to search list"); // 提示跳转到搜索列表页
```

#### 爬虫示例

```

// 引入puppeteer 浏览器
const puppeteer = require('puppeteer');
const $ = require("jquery");
const fs = require('fs');

const url = 'http://jandan.net/';

// 自执行函数
(async () => {
    const browser = await puppeteer.launch(); // 打开浏览器
    const page = await browser.newPage(); // 打开新tab页
    await page.goto(url); // 跳转到煎蛋网站
    
    console.log('跳转到:',url);

    await autoScroll(page); // 向下滚动加载图片

    const data = await page.evaluate(() => {
    
        let srcs = []
        // 获取图片地址
        $(".list-post .thumbs a img").each( (i,item)=>{
            srcs.push($(item).attr("src"))
        })

        // 获取标题数组
        let texts = []
        $(".list-post .indexs h2 a").each( (i,item)=>{
            texts.push($(item).text())
        })

        return {
            srcs:   srcs,
            title: texts
        }
        
    }); 
    // 获取所有img的src
    console.log(`get ${data}`);

    let json = data.srcs.map( (src,index)=>{
        return {src,text:data.title[index]}
    })

    // 写入到json中
    fs.writeFileSync(__dirname+"/data.txt",JSON.stringify({json}))
    // 关闭浏览器
    await browser.close();

})();

  
// 滚动浏览器
  const autoScroll = async page => {
    console.log("scrolling this page to the footer...");
    await page.evaluate(async () => {
      await new Promise((resolve, reject) => {
        let totalHeight = 0;
        let distance = 100;
        let timer = setInterval(() => {
          let scrollHeight = document.body.scrollHeight;
          window.scrollBy(0, distance);
          totalHeight += distance;
  
          if (totalHeight >= scrollHeight) {
            clearInterval(timer);
            resolve();
          }
        }, 200);
      });
    });
  };
  
```