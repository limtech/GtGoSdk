# GeeTest Golang SDK
极验验证的Golang SDK

本项目是面向服务器端的，具体使用可以参考极验的 `文档 [](http://www.geetest.com/install/sections/idx-server-sdk.html)`，客户端相关开发请参考极验的`前端文档。 [](http://www.geetest.com/install/)`.

### 开发环境

 - Golang (推荐1.6.2以上版本）


### 快速开始

下面使用示例代码的均以Beego框架为例。

1. 获取代码
通过go get获取代码:

```go
    go get github.com/limtech/go-geetest
```

2. 初始化验证

在调用GeetestLib前请自行在app.conf设定公钥和私钥,用户id为可选项，默认为空字符串：

```go
const (
	GT_PRIVATE_KEY string = "你的私钥"
	GT_CAPTCHA_ID  string = "你的公钥"
)
```

3. 根据自己的私钥初始化验证

```go
    userId := ""
	gt := geetest.GeetestLib(GT_PRIVATE_KEY, GT_CAPTCHA_ID)
	status := gt.PreProcess(userId)
	log.Println(status)
	rs := gt.GetResponseStr()
	log.Println(rs)
	// rs := gt.GetResponseMap()
	// log.Println(rs)
```

4. 二次验证

```go
// use gin
func GeetestVerify(c *gin.Context) {
	type GeetestVerifyParams struct {
		Challenge string `json:"challenge" binding:"required"`
		Validate  string `json:"validate" binding:"required"`
		Seccode   string `json:"seccode" binding:"required"`
		Status    int    `json:"status" binding:"required"`
	}

	params := GeetestVerifyParams{}
	if err := c.BindJSON(&params); err != nil {
		log.Println(err)
		return
	}

	gt := geetest.GeetestLib(GT_PRIVATE_KEY, GT_CAPTCHA_ID)
	var rs bool
	if params.Status == 0 {
		rs = gt.FailbackValidate(params.Challenge, params.Validate, params.Seccode)
	} else {
		rs = gt.SuccessValidate(params.Challenge, params.Validate, params.Seccode, "")
	}

	log.Println(rs)
}
```

### 更新日志
 - 3.2.1
   + 增加GetResponseMap()接口，适用于无session环境下获得map对象

 - 3.2.0
   + 参照 `gt-python-sdk [](https://github.com/GeeTeam/gt-python-sdk/)` 3.2.0版实现极验接口
