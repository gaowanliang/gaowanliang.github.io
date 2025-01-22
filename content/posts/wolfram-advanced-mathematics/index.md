---
title: "Wolfram 高等数学输入方法大全"
date: 2021-05-28 10:48:20
tags: ["Wolfram", "数学"]
categories: ["教程"]
---

# 求极限

## 普通极限

`lim sin2x/(e^x-1), x->0`

## 单侧极限

- `lim arctan(1/x) as x->0+`
- `lim exp(1/x) as x->0-`

## x 趋于无穷大：oo

- `lim x^2sin(3/x^2)) , x->oo`
- `lim arctan(x), x->-oo`

# 求导数

## 一阶导数

### derivative 记号

- `derivative of (2x^2+3)sin(x)`
- `derivative (2x^2+3)sin(x)`

### 分式记号

`d/dx (2x^2+3)sin(x)`

### 撇记号

`((2x^2+3)sin(x))'`

## 求一点的导数

- `derivative of (2x^2+3)sin(x) at x=3`
- `d/dx (2x^2+3)sin(x) at x=3`
- `((2x^2+3)sin(x))' at x=3`

## 二阶导数

- `second derivative of sin(2x^2+3)`
- `2nd derivative sin(2x^2+3)`

### 撇记号

- `(sin(2x^2+3))''`

## 三阶导数

`(sin(2x^2+3))'''`

## 10 阶导数

- `10th derivative 1/(1+x)`
- `d^10/dx^10(1/(1+x))`

## 特殊点处的导数

### x=1 处的 2 阶导数

`(exp(x)cos(2x^2))'' at x=1`

### 0 处的 7 阶导数

`7th derivative of 1/(1+x) at 0`

## 参数方程的导数

### $x=2t^2, y=sint$ 的导数 (dy/dx)

`(sin(t))'/(2t^2)'`

### $x=sin2t, y=cost$ 在 $t=\frac{\pi}{6}$ 处的导数

`(cost)'/(sin2t)' at t=pi/6`

## 隐函数的导数

### 方程 $y=1-xe^y$ 的导数(dy/dx)

`-( d/dx( 1-x*exp(y)-y ) )/( d/dy( 1-x*exp(y)-y ) )`

# 函数特征

## 求方程的根

### 解方程求根（包括复根）

`solve x^3+1.1*x^2+0.9*x-1.4=0`

### 只求实根(real root)

`real root x^3+1.1*x^2+0.9*x-1.4=0`

## 求函数的驻点

> 驻点：stationary point

`stationary point of x^3-2x+3`

### 求二元函数的驻点

- `stationary point of x^3-y^3+3*x^2+3*y^2-9*x`
- `maximize x^3-y^3+3*x^2+3*y^2-9*x`

## 求函数的极值

### 极小值：local min

`local min x/(x^2+2)`

### 极大值：local max

`local max x/(x^2+2)`

### 求指定区间内的极大值

`local max 2sin(2x)^2-(5/2)cos(x/2), x=0 to pi`

### 求二元函数的极值

`local max x^3-y^3+3*x^2+3*y^2-9*x`

## 求条件极值

### 求函数 xy 在 x+y=1 上的最大值

`maximize xy on x+y=1`

### 两个约束条件

- `maximize xyz on 2*(x*y + y*z + z*x) =1, x>0,y>0,z>0`
- `maximize sqrt(x^2+y^2+z^2) on z=x^2+y^2 and x+y+z=1`

## 求梯度

> 用 grad 或 del 求梯度

### 二元函数的梯度

`grad x^2+cos(2y)`

### 三元函数的梯度

`del x^2y+cos(xy)+xyz^2`

## 求方向导数

`derivative of x^2+cos(2y) in the direction (2,-3) at (1,2)`

## 求函数的最值

### 求指定区间内的最小值

`global min of 2sin(2x)^2-(5/2)cos(x/2)^2 for 1<=x<=3`

### 求二元函数的最值

`minimize 2(xy+2/x+2/y) for x>0, y>0`

## 求曲线的拐点

> (拐点：inflection point)

`inflection point of x/(x^2+2)`

# 积分

## 求不定积分

> (积分：integrate)

- `integrate x^2+sin(x)+1`
- `int xarctan(x)`
  > int：integrate 的简写

## 求定积分

- `integrate x^2+sin(x)+1 from 0 to 1`
- `int 1/sqrt(1-x^2) , x= 0 ..1/2`

## 求广义积分

> 无穷大用两个 o 表示：oo

- `int xexp(-2x) from 0 to oo`
- `int 1/(x^2+3) , x= -oo .. oo`

## 积分变限函数

### 积分上限函数

`integrate texp(-t) from 0 to x`

### 积分上限函数求导

`d/dx ( int texp(-t) from 0 to x )`

### 求积分变限函数

`F(x)==integrate texp(-t) from ln(x) to x^2`

### 积分变限函数求导

`d/dx (integrate texp(-t) from ln(x) to x^2 )`

## 二重积分

`int x^2y+x, y=2..4, x=1..3`

### 先 y, 后 x

`int xy , x=1..2, y=1..x`

### 先 x, 后 y

`int xy , y=-1..2, x=y^2..y+2`

## 三重积分

> 积分次序:z,y,x

`int x , x=0..1, y=0..(1-x)/2, z=0..1-x-2y`

# 面积、体积

## 曲线与$x$轴之间的面积

### 曲线$y=2-x^2$与$x$轴之间的面积

`area between 2-x^2 and x-axis`

## 曲线下方的面积

### 曲线$y=3-x^2$与$x$轴之间的面积

`area under 3-x^2`

## 求两曲线之间的面积

### 曲线$y=x$与$y=x^2$之间的面积

`area between x and x^2`

## 求两曲线的交点

### 曲线$y=x^2$与$y=x^3$的交点

`solve x^2 = x^3`

## 求两曲线之间的面积

### 曲线$y=sinx$与$y=cos2x$ $(0<x<\pi)$ 之间的面积

`area between sin(x) and cos(2x) from x=0 to pi`

## 旋转体的体积

### 曲线$y=sinx (0<x<2)$ 与$x$轴之间的区域绕$x$轴旋转

`V=pi*Integrate (sin(x)^2, x=0..2)`

### 曲线$y=x$与$y=sinx (0<x<pi)$之间的区域绕$x$轴旋转

V=pi\*Integrate (x^2-sin(x)^2, x=0..pi)

## 求曲线的弧长

### 曲线 $y=x^2 (1<x<3)$的弧长

`int sqrt(1+ ( (x^2)' )^2), x=1..3`

### 参数曲线 $x=sin(t^3), y=t(-1<t<1)$ 的弧长

`int sqrt( (sin(t^3))' ^2+(t)'^2 ), t=-1..1`

# 向量

## 向量的点积

`(1,2,4) . (-2,3,6)`

## 向量的叉积

> 叉积：cross

`(1,2,4) cross (-2,3,6)`

## 向量函数的导数 (速度)

`(t^2, 4t-3, 2t^2-6t)'`

## 向量函数的二阶导数 (加速度)

`(t^2, e^t, 2t^3)''`

# 偏导数

## 求偏导数

### 对 x 求偏导数

`d/dx sin(x^2+2y)`

### 对 y 求偏导数

`d/dy sin(x^2+2y)`

### 在某一点的偏导数

`d/dx sin(x^2+2y) at (1,2)`

## 高阶偏导数

### 对 x 的二阶偏导数

`d/dx d/dx x^3*y^2 - 3*x*y^3 - x*y + 1`

## 混合偏导数

`d/dy d/dx x^3*y^2 - 3*x*y^3 - x*y + 1`

## 隐函数的偏导数

### 方程$e^z=xyz$的偏导数 (dz/dx)

`-( d/dx( exp(z)-xyz ) )/( d/dz (exp(z)-xyz) )`

# 多项式

## 级数求和

### 等比级数求和

`sum 3*(2/5)^n, n=1..oo`

### p-级数求和

> 用 sum 求和

`sum 1/n^2, n=1..oo`

### 交错级数求和

`sum (-1)^(n+1)/n^2, n=1..oo`

## 幂级数的和函数

`sum (-1)^(n+1)*x^(2n-1)/(2n-1),n=1...oo`

## 函数的泰勒公式

### xsinx 在 x=0 处的 5 阶泰勒公式

> series：级数

`series xsin(x) to order 5`

### e^x 在 x=1 处的 5 阶泰勒公式

> taylor：泰勒

`taylor e^x at x=1 to order 5`

# 微分方程

`y’=2xy`

## 求特解

`y’=e^(2x-y), y(0)=0`

## 二阶微分方程的特解

`(1+x^2)y’’=2xy’, y(0)=1, y’(0)=3`

## 二阶微分方程的通解

`y’’-5y’+6y=xe^(2x)`

## 二阶微分方程的特解

`y’’+y+sin(2x)=0, y(pi)=1, y’(pi)=1`

# 作图

## 一元函数图形

> 作图：plot
> `plot y=x^3-x^2-x+1, x=-2..2`

## 两条曲线

`plot xsinx,arctanx`

## 隐函数的图形

`plot x^3+y^3=6xy`

### 指定范围

`plot x^3+y^3=6xy, x=-4..4, y=-4..4`

### 两条曲线

`plot x^2+y^2=2x, x=y^3+1`

## 参数曲线

> parametric plot : 参数方程作图

`parametric plot (t(1-sint),tcost) from t=-10 to 10`

## 参数方程

`x=t(1-sint), y=tcost`

## 极坐标曲线

> polar plot: 极坐标作图

`polar plot 1+cost, t=0..2pi`

## 曲面作图

- `plot x^2+y^2, -2<x<2, -2<y<2`
- `plot sqrt(2-x^2-3y^2)`
