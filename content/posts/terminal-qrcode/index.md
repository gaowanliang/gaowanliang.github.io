---
title: Python 命令行输出二维码
date: 2021-03-03 15:52:21
tags: ["Python", "二维码"]
categories: ["工具"]
---

为了方便信息处理，有的时候需要在 ssh 终端等纯命令行的情况下输出一个二维码的图片，虽然 Linux 可以使用`qrencode`模块，但是这个东西始终不适合软件集成，所以我通过多方查找和修改，做出了下面的代码，非常简单，可以直接集成到 python 中使用。

```python
def textQR(text):
    qr = qrcode.QRCode(
        version=1,
        error_correction=qrcode.constants.ERROR_CORRECT_L,
        box_size=1,
        border=1
    )
    qr.add_data(text)
    qr.make(fit=True)
    img = qr.make_image()
    img2 = np.array(img.convert('L'))
    d = {255: '@@', 0: '  '}
    rows, cols = img2.shape
    for i in range(rows):
        for j in range(cols):
            print(d[img2[i, j]], end='')
        print('')
    return ''
```

下面可以看到展示效果

![](https://s4.ax1x.com/2021/03/03/6AXPte.png)

缺点：如果输入的内容过长，会导致生成的二维码过大，导致一个屏幕难以盛放。所以我只写了通过文本生成二维码的方法，如果有需要可以自行研究如何将图片转换成二维码，也是比较简单的。
