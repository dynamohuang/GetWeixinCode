# GetWeixinCode

解决微信OAuth2.0网页授权回调域名只能设置一个的问题


#关于这个fork的意义
比较master，添加了对回调地址的检验 防止其他非法调用

只需在白名单变量allow_host数组中加入待接入的域名皆可

#吐槽
唉 公众号开发说多了就是坑，还有现在网页授权回调域名要备案的域名了 用这种方式授权的话只需将署get-weixin-code.html部署到备案的域名服务器上，业务服务部署到其他的ip或者未备案域名都妥妥的


## 使用方法

1. 部署get-weixin-code.html至你的微信授权回调域名的目录下，例如http://wx.abc.com/get-weixin-code.html
2. 在其他页面的使用方式如下，类似于直接通过微信回调的方式，只是将回调地址改成了get-weixin-code.html的地址，另外省去了response_type参数（因为它只能为code）以及#wechat_redirect的hash，它们会在get-weixin-code.html里面去加上
    ```javascript

    location.href = 'http://wx.abc.com/get-weixin-code.html?appid=XXX&scope=XXX&state=XXX&redirect_uri=' + encodeURIComponent(location.href);

    ```
3. get-weixin-code.html页面从微信那里拿到code之后会重新跳转回调用的页面，并且在url后面带上code

## 其他说明

- 通过多一次的跳转，解决了微信限制回调域名只能设置一个的问题
- 牺牲了一点用户体验，换来了项目部署的美感，不需要将各种项目都部署到一个域名下
- 如果你有这样的需求，可以使用本项目
- 欢迎提交pull request
- **很多朋友问我怎么支持第三方微信平台，这个需要对不同的第三方平台的授权方式有所了解，熟悉他们的授权方式，请求参数等。如果他们是通过在网站入口处的URL上进行授权的，那么可以使用本项目，将入口的URL改成上述的方式，如果他们是在流程中的某些页面去获取授权，那么是没法改变他们的获取地址的，所以本项目就不适用了**
- 


## 关于php中 https://github.com/overtrue/wechat 的接入
只需 

		if (!$this->input->get('state') && !$this->input->get('code')) {
			header('Location:http://tech.hongbinchunji.com/GetWeixinCode/get-weixin-code.html?appid='.WECHAT_APP_ID.'&scope=snsapi_base&state=STATE&redirect_uri='.'http://'.$_SERVER['SERVER_NAME'].$_SERVER['REQUEST_URI'] );
			exit;
		}


		$auth = new \Overtrue\Wechat\Auth(WECHAT_APP_ID, WECHAT_APP_SECRET);
		$ret  = $auth->user();

		var_dump($ret);

这个就可以咯，两种scope都支持

