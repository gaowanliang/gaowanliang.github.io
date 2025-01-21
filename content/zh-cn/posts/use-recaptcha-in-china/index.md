---
title: "在中国国内使用Google reCAPTCHA 2/3 验证码的方法"
date: 2020-02-13 18:36:51
tags: ["reCAPTCHA", "Google", "验证码"]
categories: ["教程"]
---

我看版本 2 的有人说了，我简单的说一下版本 3 的使用和集成

国内使用 reCAPTCHA 只需要将 www.google.com 替换成 www.recaptcha.net，即可在国内使用 recaptcha 的服务。
如 https://www.google.com/recaptcha/api.js 替换成 https://www.recaptcha.net/recaptcha/api.js，https://www.google.com/recaptcha/api/siteverify 替换成 https://www.recaptcha.net/recaptcha/api/siteverify。

浏览器界面请使用以下方法使用

```js
<script src="https://www.recaptcha.net/recaptcha/api.js?render=客户端代码"></script>
<script>
    //验证reCAPTCHA是否载入
    grecaptcha.ready(function () {
        console.log("reCAPTCHA载入成功")
    });
    //生成特征验证码
    grecaptcha.execute('客户端代码', {action: 'homepage'}).then(function(token) {

    });
</script>
```

这里给一个比较完整的例子，以 JavaScript 和 python(flask)为例

## 客户端代码 JavaScript

```js
<script src="https://www.recaptcha.net/recaptcha/api.js?render=客户端代码"></script>
<script src="//cdn.jsdelivr.net/combine/gh/jquery/jquery/dist/jquery.min.js"></script>
<script>
    grecaptcha.ready(function () {
        console.log("reCAPTCHA载入成功")
    });
    $('#submit').click(function () {
        var d = {};
        var t = $('form').serializeArray();
        $.each(t, function () {
            d[this.name] = this.value;
        });
        console.log(d)
        grecaptcha.execute('客户端代码', { action: 'homepage' }).then(function (token) {
            d["recaptcha"] = token
            $.ajax({
                type: 'post',
                data: d,
                url: 'verify',
                cache: false,
                dataType: 'text',
                success: function (data) {
                    if (data == "1") {
                        $("#ok").text("通过验证，您不是机器人")
                    } else {
                        $("#ok").text("未通过验证，请重新测试")
                    }
                    console.log(data)
                }
            })
        })
    });
</script>
```

## 服务器端代码 Python(Flask)

```python

@app.route('/verify', methods=['POST'])
def verify():
    if request.method == 'POST':
        verification = request.form['recaptcha']
        datas = {
            'secret': "服务器端代码",
            'response': verification
        }
        k = requests.post(
            "https://recaptcha.net/recaptcha/api/siteverify", data=datas)
        print(k.text)
        kk = eval(k.text.replace('"', "'").replace(
            'true', "1").replace('false', "2"))
        if kk["success"] == 1:
            return "1"
        else:
            return "0"
```

同时可使用下面的命令对右下角的标志进行隐藏

```html
<style>
  .grecaptcha-badge {
    visibility: hidden !important;
  }
</style>

<!-- 但是谷歌强烈建议在网站上保留下面的标识 -->

<small
  >This site is protected by reCAPTCHA and the Google
  <a href="https://policies.google.com/privacy">Privacy Policy</a> and
  <a href="https://policies.google.com/terms">Terms of Service</a> apply.
</small>

<!-- 自行翻译的中文版 -->

<small
  >本网站受RECAPTCHA和谷歌<a href="https://policies.google.com/privacy"
    >隐私政策</a
  >及<a href="https://policies.google.com/terms">服务条款</a>的保护。
</small>
```

## 完整例子下载地址

https://www.lanzous.com/i9doidc
