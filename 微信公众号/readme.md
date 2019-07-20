## 一、课程目标
* 掌握微信公众号的开发流程
* 掌握微信公众号的接入方式
* 掌握微信JS-SDK接入方式
* 掌握公众号API的使用
## 二、微信公众平台介绍
## 三、公众号类型
* 订阅号
* 服务号
* 小程序
* 企业号
## 四、注册订阅号
## 五、后台接入微信
#### 1、使用nodejs接入微信
#### 接入指南：https://mp.weixin.qq.com/wiki?t=resource/res_main&id=mp1421135319
#### 2、填写服务器配置

* URL服务器地址 (80/443)
* Token自己指定
* EncodingAESKey消息加解密密钥


#### 3、签名代码
```
var express = require('express');
var crypto = require('crypto');  //引入加密模块
var config = require('./config');//引入配置文件
// var http = require('http');
var request = require('request');

var app = express();


app.get('/', function (req, res) {

    //1.获取微信服务器Get请求的参数 signature、timestamp、nonce、echostr
    var signature = req.query.signature,//微信加密签名
        timestamp = req.query.timestamp,//时间戳
        nonce = req.query.nonce,//随机数
        echostr = req.query.echostr;//随机字符串

    //2.将token、timestamp、nonce三个参数进行字典序排序
    console.log(config.token)
    var array = [config.token, timestamp, nonce];
    array.sort();

    //3.将三个参数字符串拼接成一个字符串进行sha1加密
    var tempStr = array.join('');
    const hashCode = crypto.createHash('sha1'); //创建加密类型 
    var resultCode = hashCode.update(tempStr, 'utf8').digest('hex'); //对传入的字符串进行加密

    //4.开发者获得加密后的字符串可与signature对比，标识该请求来源于微信
    if (resultCode === signature) {
        res.send(echostr);
    } else {
        res.send('mismatch');
    }
});

const getAccessToken = () =>{

    request('https://api.weixin.qq.com/cgi-bin/token?grant_type=client_credential&appid='+config.appId+'&secret='+config.appSecret, function (error, response, body) {
        console.log('error:', error); // Print the error if one occurred
        console.log('statusCode:', response && response.statusCode); // Print the response status code if a response was received
        console.log('body:', body); // Print the HTML for the Google homepage.
    });
}


var server = app.listen(80, function () {
    var host = server.address().address;
    var port = server.address().port;
    console.log('Example app listening at http://%s:%s', host, port);
});
```

#### 4、 登录云服务器

## `ssh root@203.195.141.173`
#### 5、部署项目
 * 把本地项目推送到github
 * 在服务器上配置开发环境（Nodejs、git、。。。。）
 * 在服务器上，从github上clone下来
    * 创建www   `mkdir www`
    * git clone https://github.com/cooleye/wxserver.git
 * 使用pm2启动服务  `pm2 start index.js`
 * 也可以使用FTP工具上传代码（FileZilla） 
 

#### 6、 pm2 简单介绍

    PM2是node进程管理工具，可以利用它来简化很多node应用管理的繁琐任务，如性能监控、自动重启、负载均衡等
    
##### 官网文档：http://pm2.keymetrics.io/docs/usage/pm2-doc-single-page/
##### NPM：https://www.npmjs.com/package/pm2

1、安装 `sudo npm install pm2@latest -g`

2、用法

* 最简单的启用一个应用:  pm2 start app.js
* 停止：pm2 stop app_name|app_id
* 删除：pm2 delete app_name|app_id
* 重启：pm2 restart app_name|app_id
* 停止所有：pm2 stop all
* 查看所有的进程：pm2 list
* 查看所有的进程状态：pm2 status
* 查看某一个进程的信息：pm2 describe app_name|app_id

## 六、申请测试公众号

#### https://mp.weixin.qq.com/debug/cgi-bin/sandboxinfo?action=showinfo&t=sandbox/index

## 七、自定义菜单案例
文档：https://mp.weixin.qq.com/wiki?t=resource/res_main&id=mp1421141013
#### 0、config.json
```
{
    "token":"test",
    "appId":"wx1fb65a4ee2266c01",
    "appSecret":"07d528cf1fed8735e68fdbba76e4080d"
}
```

#### 1、获取token
```
function getToKen(appId, appSecret) {

    return new Promise(function (resolve, reject) {

        var url = 'https://api.weixin.qq.com/cgi-bin/token?grant_type=client_credential&appid=' + appId + '&secret=' + appSecret;
        request({
            uri: url
        }, function (err, res, data) {
            var result = JSON.parse(data);
            resolve(result.access_token);  //把获取的access_token返回去
        });

    });

}
```
自定义菜单：
```
//menu为创建自定义菜单的具体内容，也就是post到微信服务器的body
var menu = {
    "button": [{
        "name": "我的账号",
        "sub_button": [{
            "type": "click",
            "name": "test1",
            "key": "V1001_MY_ACCOUNT"
        }, {
            "type": "click",
            "name": "test2",
            "key": "V1002_BID_PROJECTS"
        }, {
            "type": "click",
            "name": "test3",
            "key": "V1003_RETURN_PLAN"
        }, {
            "type": "click",
            "name": "test4",
            "key": "V1004_TRANS_DETAIL"
        }, {
            "type": "click",
            "name": "test5",
            "key": "V1005_REGISTER_BIND"
        }
        ]
    }, {
        "type": "view",
        "name": "百度一下",
        "url": "http://www.baidu.com/"
    }
    ]
};
```
创建菜单：
```
function createmenu() {
    var post_str = new Buffer(JSON.stringify(menu));   //先将menu转成JSON数据格式，在赋给post_srt数组

    //调用getToken函数，getToken函数执行完，接下来才执行then函数中的匿名函数,其中，access_token为返回来的参数。
    getToKen(appId, appSecret).then(function (access_token) {
        var post_options = {
            host: 'api.weixin.qq.com',
            port: '443',
            path: '/cgi-bin/menu/create?access_token=' + access_token,
            method: 'POST',
            headers: {
                'Content-Type': 'application/x-www-form-urlencoded',
                'Content-Length': post_str.length
            }
        };

        var post_req = https.request(post_options, function (response) {
            var responseText = [];
            var size = 0;
            response.setEncoding('utf8');
            response.on('data', function (data) {
                responseText.push(data);
                size += data.length;
            });
            response.on('end', function () {
                console.log("responseText=", responseText);
            });
        });

        post_req.write(post_str);// 把menu数据post到微信服务器，剩下的微信自动帮我们搞定了。
        post_req.end();
    })
}
```
删除自定义菜单：
```
//删除自定义菜单
function deletemenu() {

    //调用getToken函数，getToken函数执行完，接下来才执行then函数中的匿名函数,其中，access_token为返回来的参数。
    getToKen(appId, appSecret).then(function (access_token) {
        console.log(access_token)
        https.get("https://api.weixin.qq.com/cgi-bin/menu/delete?access_token="+access_token, function (response) {
                console.log(response)
        });
    })
}
```

## 八、微信JSSDK
#### 1、JSSDK 文档：
https://mp.weixin.qq.com/wiki?t=resource/res_main&id=mp1421141115

#### 2、接入步骤
1、准备好已经备案的服务器、域名，这里使用了新浪SAE  
2、注册新浪云，可以使用微博登录  
3、创建云应用   
4、本地创建项目，使用git初始化仓库  
5、参考 [NodeJS应用部署指南](https://www.sinacloud.com/doc/sae/docker/nodejs-getting-started.html)             
6、关联新浪远程仓库，推送代码，部署项目

#### 3、绑定js接口安全域名
项目部署成功后，就可以在浏览器访问项目，例如：http://1919.applinzi.com
把 http://1919.applinzi.com 配置在微信公众号的js接口安全域名处。

#### 4、引入jssdk
在使用jssdk的页面引入：
```
<script src="http://res.wx.qq.com/open/js/jweixin-1.4.0.js"></script>
```

#### 5、后端生成签名
index.js:
```
var appid = "wx1fb65a4ee2266c01";
var secret = "07d528cf1fed8735e68fdbba76e4080d";

var request = require('request'),
    cache = require('memory-cache');

var express = require('express');

var sign = require('./sign.js');

var app = express();
app.use('/wx', express.static('static'));


//获得签名
app.get('/createsign', function (req, res) {
    var url = decodeURIComponent(req.query.url);
    var  jsapi_ticket;
    //判断是否有缓存ticket
    if (cache.get('ticket')) {
        jsapi_ticket = cache.get('ticket');
        var ret = sign(jsapi_ticket,url);
        res.send(ret)
    } else {
        //生成token，这里需要用到appid和secret，这里注意替换
        request('https://api.weixin.qq.com/cgi-bin/token?grant_type=client_credential&appid='+appid+'&secret='+secret, function (error, response, body) {
            if (!error && response.statusCode == 200) {
                var tokenMap = JSON.parse(body);
                //根据token生成ticket
                request('https://api.weixin.qq.com/cgi-bin/ticket/getticket?access_token=' + tokenMap.access_token + '&type=jsapi', function (error, resp, json) {
                    if (!error && response.statusCode == 200) {
                        var ticketMap = JSON.parse(json);
                        //设置过期时间
                        cache.put('ticket', ticketMap.ticket, (1000 * 60 * 60 * 24));  //加入缓存
                        jsapi_ticket = ticketMap.ticket;
                        var ret = sign(jsapi_ticket,url);
                        res.send(ret)
                        //将信息返回
                    }
                })
            }
        })
    }
});

app.listen(process.env.PORT || 5050)
```
#### 6、通过config接口注入权限验证配置
```
<script>

    $.get("http://1919.applinzi.com/createsign",
    {url:encodeURIComponent(location.href.split('#')[0])},
    function (res) {
        console.log(res)
        wx.config({
            debug: true, // 开启调试模式,调用的所有api的返回值会在客户端alert出来，若要查看传入的参数，可以在pc端打开，参数信息会通过log打出，仅在pc端时才会打印。
            appId: "wx1fb65a4ee2266c01", // 必填，公众号的唯一标识
            timestamp: res.timestamp, // 必填，生成签名的时间戳
            nonceStr: res.nonceStr, // 必填，生成签名的随机串
            signature: res.signature,// 必填，签名，见附录1
            jsApiList: ['chooseImage',
                'previewImage',
                'uploadImage',
                'downloadImage',
                'updateTimelineShareData',
                'scanQRCode'
            ] // 必填，需要使用的JS接口列表，所有JS接口列表见附录2
        });
    })

</script>
```
#### 7、判断当前客户端版本是否支持指定JS接口
```
wx.ready(function () {
        // alert('ready')
        // 1 判断当前版本是否支持指定 JS 接口，支持批量判断
        document.querySelector('#checkJsApi').onclick = function () {
            wx.checkJsApi({
                jsApiList: [
                    'chooseImage',
                    'previewImage',
                    'uploadImage',
                    'downloadImage',
                    'updateTimelineShareData',
                    'scanQRCode'
                ],
                success: function (res) {
                    alert(JSON.stringify(res));
                }
            });
        };
})
```

#### 8、常用接口
1、拍照或选择照片
```
document.querySelector('#chooseImage').onclick = function () {
            wx.chooseImage({
                success: function (res) {
                    images.localId = res.localIds;
                    alert('已选择 ' + res.localIds.length + ' 张图片');
                }
            });
        };
```
2、图片预览
```
document.querySelector('#previewImage').onclick = function () {
            wx.previewImage({
                current: 'http://img5.douban.com/view/photo/photo/public/p1353993776.jpg',
                urls: [
                    'http://img3.douban.com/view/photo/photo/public/p2152117150.jpg',
                    'http://img5.douban.com/view/photo/photo/public/p1353993776.jpg',
                    'http://img3.douban.com/view/photo/photo/public/p2152134700.jpg'
                ]
            });
        };
```
3、上传照片
```
document.querySelector('#uploadImage').onclick = function () {
            if (images.localId.length == 0) {
                alert('请先使用 chooseImage 接口选择图片');
                return;
            }
            var i = 0, length = images.localId.length;
            images.serverId = [];
            function upload() {
                wx.uploadImage({
                    localId: images.localId[i],
                    success: function (res) {
                        i++;
                        //alert('已上传：' + i + '/' + length);
                        images.serverId.push(res.serverId);
                        if (i < length) {
                            upload();
                        }
                    },
                    fail: function (res) {
                        alert(JSON.stringify(res));
                    }
                });
            }
            upload();
        };
```
4、下载照片
```
document.querySelector('#downloadImage').onclick = function () {
            if (images.serverId.length === 0) {
                alert('请先使用 uploadImage 上传图片');
                return;
            }
            var i = 0, length = images.serverId.length;
            images.localId = [];
            function download() {
                wx.downloadImage({
                    serverId: images.serverId[i],
                    success: function (res) {
                        i++;
                        alert('已下载：' + i + '/' + length);
                        images.localId.push(res.localId);
                        if (i < length) {
                            download();
                        }
                    }
                });
            }
            download();
        };
```
5、分享到朋友圈
```
document.querySelector('#sharebtn').onclick = function () {
            wx.updateTimelineShareData({ 
                title: '你不知道的前端', // 分享标题
                link: 'http://1919.applinzi.com/wx/demo.html', // 分享链接，该链接域名或路径必须与当前页面对应的公众号JS安全域名一致
                imgUrl: 'http://1919.applinzi.com/wx/fox.jpg', // 分享图标
                success: function () {
                // 设置成功
                console.log('share success')
                alert('share success')
            }
            })
        };
```
6、扫一扫
```
document.querySelector('#scan').onclick = function () {
            wx.scanQRCode({
                needResult: 0, // 默认为0，扫描结果由微信处理，1则直接返回扫描结果，
                scanType: ["qrCode","barCode"], // 可以指定扫二维码还是一维码，默认二者都有
                success: function (res) {
                var result = res.resultStr; // 当needResult 为 1 时，扫码返回的结果
                }
            });
        };
```