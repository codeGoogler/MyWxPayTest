## Android微信支付集成步骤

### 准备工作

在应用集成微信支付之前，我们在[微信开放平台](https://open.weixin.qq.com/cgi-bin/index?t=home/index&lang=zh_CN&token=79b90f1a82ebf8eecfd25e57c43729df42c5f9b5)必须要个开发者账户

1.注册完之后创建一个移动应用，并获取APPid等可以参考：

http://blog.csdn.net/vroymond/article/details/53422744

2.申请开通微信支付能力

- 认证开发者资格

![这里写图片描述](http://img.blog.csdn.net/20161211110806235?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvVlJveW1vbmQ=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)



- 开通微信支付

  ![](http://file.service.qq.com/user-files/uploads/201403/b09ce2af0f9153cf0ec2690514ff5fe6.jpg)

3.开通成功后，获取得到商户号并在[商户平台](https://pay.weixin.qq.com/index.php/core/home/login?return_url=%2Findex.php)配置API密钥（生成预支付订单号需要）

API密钥配置流程：http://help.ecmoban.com/article-2085.html

4.在项目中导入微信提供的jar包

![这里写图片描述](http://img.blog.csdn.net/20161211121236183?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvVlJveW1vbmQ=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

5.在项目包名下创建一个wxapi的包，并创建一个WXPayEntryActivity的类(微信分享以及登录必须要求，该类继承activity并实现IWXAPIEventHandler接口，用于拿到支付的回调结果)，并在清单文件中注册。

![这里写图片描述](http://img.blog.csdn.net/20161211121537577?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvVlJveW1vbmQ=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

### 调起微信支付

步骤：

1.客户端（APP）提交订单信息给服务端，服务端根据微信接口：统一下单接口，生成预支付Id(prepay_id)返回给客户端。

![这里写图片描述](http://img.blog.csdn.net/20161211113307332?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvVlJveW1vbmQ=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)



2.客户端（APP）根据预支付Id（prepay_id）调起微信支付




![这里写图片描述](http://img.blog.csdn.net/20161211113640056?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvVlJveW1vbmQ=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)



#### 如何生成预支付Id（一般在服务端生成）?

根据[统一下单接口](https://pay.weixin.qq.com/wiki/doc/api/app/app.php?chapter=9_1)文档的规则：

服务端需要必须提交的参数字段有以下这些:（POST格式为XML）

- 应用ID		appid                微信开放平台审核通过的应用APPID
- 商户号		mch_id             微信支付分配的商户号
- 随机字符串	nonce_str	   [随机数生成算法](https://pay.weixin.qq.com/wiki/doc/api/app/app.php?chapter=4_3)
- 商品描述		body
- 商户订单号	out_trade_no	   
- 总金额		total_fee	
- 终端IP		spbill_create_ip
- 通知地址	        notify_url
- 交易类型		trade_type	
- 签名			sign			[签名生成算法（重要）](https://pay.weixin.qq.com/wiki/doc/api/app/app.php?chapter=4_3)

详情可看：https://pay.weixin.qq.com/wiki/doc/api/app/app.php?chapter=9_1

sign签名生成：

1.把我们所需要提交的参数（除sign外），拼接成URL键值对的格式（即key1=value1&key2=value2…）

![这里写图片描述](http://img.blog.csdn.net/20161211115122794?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvVlJveW1vbmQ=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

![这里写图片描述](http://img.blog.csdn.net/20161211115234111?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvVlJveW1vbmQ=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

2.得到拼接后的字符串之后拼接在商户平台生成 API密钥

![这里写图片描述](http://img.blog.csdn.net/20161211115457001?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvVlJveW1vbmQ=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

![这里写图片描述](http://img.blog.csdn.net/20161211115750520?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvVlJveW1vbmQ=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

3.拼接完key之后，进行MD5运算，再将得到的字符串所有字符转换为大写，得到sign

![这里写图片描述](http://img.blog.csdn.net/20161211120121208?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvVlJveW1vbmQ=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

#### 提交所有参数 调起统一下单接口 获取预支付Id

![这里写图片描述](http://img.blog.csdn.net/20161211120605500?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvVlJveW1vbmQ=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)



#### APP客户端调起微信支付

根据微信提供的调起[微信支付](https://pay.weixin.qq.com/wiki/doc/api/app/app.php?chapter=9_12&index=2)的规则，APP端需要提交的参数为：

![这里写图片描述](http://img.blog.csdn.net/20161211122055569?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvVlJveW1vbmQ=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

1.sign签名生成

​	sign签名生成步骤跟上面叙述的是一样的（省略）。

2.生成完签名，拼接所有支付参数。（PayReq，IWXAPI是微信提供jar包里的类）

![这里写图片描述](http://img.blog.csdn.net/20161211122534298?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvVlJveW1vbmQ=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

3.调起微信支付

![这里写图片描述](http://img.blog.csdn.net/20161211122747750?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvVlJveW1vbmQ=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

（注意，运行的应用签名必须跟在微信开放平台的签名需要一致，为了方便调试可以让debug使用relase签名，配置步骤可参考：http://www.cnblogs.com/niray/p/5242985.html）



至此，调起微信支付所有步骤完成

源码地址：https://github.com/CTSN/testWxPay（此代码只能做参考，已把应用签名以及APPid等删除掉）

效果图：



![这里写图片描述](http://img.blog.csdn.net/20161211124033508?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvVlJveW1vbmQ=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)



![这里写图片描述](http://img.blog.csdn.net/20161211124116899?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvVlJveW1vbmQ=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)















