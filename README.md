# xhs-apisign
小红书APP API签名算法

小红书APP API接口使用url中的sign参数和header中shield参数来校验请求的有效性，我们随便看一个API请求：
```
GET https://www.xiaohongshu.com/api/sns/v1/system_service/config?launchtimes=1&platform=android&deviceId=e0805561-6579-3ec6-b717-d64adf33c3ef&device_fingerprint=20190519202808a5dac07354d5e3b71a01abb98b0d6dc6016852749a37a304&device_fingerprint1=20190519202808a5dac07354d5e3b71a01abb98b0d6dc6016852749a37a304&versionName=5.45.0&channel=xiaohongshu&sid=session.1558744693398053223445&lang=zh-Hans&t=1558749910&sign=c4513e220ab27a8561191440d912f457 HTTP/1.1
device_id: e0805561-6579-3ec6-b717-d64adf33c3ef
Authorization: session.1558744693398053223445
User-Agent: Dalvik/2.1.0 (Linux; U; Android 5.1.1; 2014813 MIUI/7.11.16) Resolution/720*1280 Version/5.45.0 Build/5450095 Device/(Xiaomi;2014813) NetType/WiFi
shield: aa1fb61921361897c61894b5444a2d0715c75caa473dd8991187f78a22cdcfa0
Host: www.xiaohongshu.com
Connection: Keep-Alive
Accept-Encoding: gzip
```
sign参数是用url中的参数计算出来的，大概流程如下：

 1. 把url中的参数按key=value的形式按字母顺序拼接到一起
 2. 对1的结果进行url encode
 3. 把2的结果转成byte array
 4. 从url中获取deviceId，并转成byte array
 5. 对3和4的结果每个字节进行相关的异或运算
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190525102703612.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dhbmdteGU=,size_16,color_FFFFFF,t_70)
 6. 把5的结果的md5和deviceId拼接到一起
 7. 计算6的结果的md5

shield参数是由带上sign参数的url计算出来的，是通过拦截请求，在header中插入shield字段，这部分是在`process`方法中实现，`process`是一个native方法，在`libshield.so中定义`。
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190525103321294.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dhbmdteGU=,size_16,color_FFFFFF,t_70)
小红书API的签名参数计算流程就这样，对细节感兴趣的朋友可以[联系我](http://47.105.95.219)。
