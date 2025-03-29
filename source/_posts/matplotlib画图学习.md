---
layout: post
title: matplotlib画图学习笔记
date: 2018-01-13 16:56:38
categories: 学习笔记
tags:
    - Python
    - matplotlib
    - 画图
---

## 写在前面
最近在做机器学习相关的比赛，在数据探索性分析阶段，由于要画图分析数据规律，于是学习了下matplotlib画图，主要是看莫烦python的视频学习的，[优酷播单地址](http://v.youku.com/v_show/id_XMTcxNTk3NTQ2NA==.html?spm=a2hzp.8253876.0.0&f=28097045)，也欢迎大家前去观看学习，下面是我在学习的时候在jupter notebook上跟着做的笔记。    


```python
import matplotlib.pyplot as plt 
import numpy as np
```


```python
x = np.linspace(-1, 1, 50)
# y = 2*x + 1
y = x**2
plt.plot(x, y)
plt.show()
```


![png](https://cutoutsy-blog-1253675385.cos.ap-chengdu.myqcloud.com/output_1_0.png)

<!-- more -->

## figure 图像


```python
x = np.linspace(-3, 3, 50)
y1 = 2*x + 1
y2 = x**2
plt.figure()
plt.plot(x, y1)

plt.figure(figsize=(8,5))
plt.plot(x, y2)
plt.plot(x, y1, color='red', linewidth=2.0, linestyle='--')

plt.show()
```


![png](https://cutoutsy-blog-1253675385.cos.ap-chengdu.myqcloud.com/output_3_0.png)



![png](https://cutoutsy-blog-1253675385.cos.ap-chengdu.myqcloud.com/output_3_1.png)


## 坐标轴设置


```python
x = np.linspace(-3, 3, 50)
y1 = 2*x + 1
y2 = x**2

plt.figure(figsize=(8,5))
plt.plot(x, y2)
plt.plot(x, y1, color='red', linewidth=1.0, linestyle='--')
# 设置x,y轴取值范围
plt.xlim((-1, 2))
plt.ylim((-2, 3))
plt.xlabel('I am X')
plt.ylabel('I am Y')

new_ticks = np.linspace(-1, 2, 5)
print new_ticks
plt.xticks(new_ticks)

plt.yticks([-2, -1.8, -1, 1.22, 3],
          [r'$really\ bad$', r'$bad\alpha$', r'$nomal$', r'$good$', r'$really\ good$'])

plt.show()
```

    [-1.   -0.25  0.5   1.25  2.  ]



![png](https://cutoutsy-blog-1253675385.cos.ap-chengdu.myqcloud.com/output_5_1.png)



```python
x = np.linspace(-3, 3, 50)
y1 = 2*x + 1
y2 = x**2

plt.figure(figsize=(8,5))
plt.plot(x, y2)
plt.plot(x, y1, color='red', linewidth=1.0, linestyle='--')
# 设置x,y轴取值范围
plt.xlim((-1, 2))
plt.ylim((-2, 3))
plt.xlabel('I am X')
plt.ylabel('I am Y')

new_ticks = np.linspace(-1, 2, 5)
print new_ticks
plt.xticks(new_ticks)

plt.yticks([-2, -1.8, -1, 1.22, 3],
          [r'$really\ bad$', r'$bad\alpha$', r'$nomal$', r'$good$', r'$really\ good$'])

# gca = 'get current axis
ax = plt.gca()
ax.spines['right'].set_color('none')
ax.spines['top'].set_color('none')
ax.xaxis.set_ticks_position('bottom')
ax.yaxis.set_ticks_position('left')
ax.spines['bottom'].set_position(('data', 0))
ax.spines['left'].set_position(('data', 0))

plt.show()
```

    [-1.   -0.25  0.5   1.25  2.  ]



![png](https://cutoutsy-blog-1253675385.cos.ap-chengdu.myqcloud.com/output_6_1.png)


## Legend 图例


```python
x = np.linspace(-3, 3, 50)
y1 = 2*x + 1
y2 = x**2

plt.figure(figsize=(8,5))
# 设置x,y轴取值范围
plt.xlim((-1, 2))
plt.ylim((-2, 3))
plt.xlabel('I am X')
plt.ylabel('I am Y')

new_ticks = np.linspace(-1, 2, 5)
print new_ticks
plt.xticks(new_ticks)

plt.yticks([-2, -1.8, -1, 1.22, 3],
          [r'$really\ bad$', r'$bad\alpha$', r'$nomal$', r'$good$', r'$really\ good$'])
l1, = plt.plot(x, y2, label='up')
l2, = plt.plot(x, y1, color='red', linewidth=1.0, linestyle='--', label='down')

# plt.legend(handles=[l1, l2], labels=['aaa', 'bbb'], loc='upper right')

plt.legend(handles=[l1,], labels=['aaa',], loc='best')

plt.show()
```

    [-1.   -0.25  0.5   1.25  2.  ]



![png](https://cutoutsy-blog-1253675385.cos.ap-chengdu.myqcloud.com/output_8_1.png)


## Annotation 标注


```python
x = np.linspace(-3, 3, 50)
y = 2*x + 1

plt.figure(num=1, figsize=(8,5))
plt.plot(x, y,)

ax = plt.gca()
ax.spines['right'].set_color('none')
ax.spines['top'].set_color('none')
ax.xaxis.set_ticks_position('bottom')
ax.yaxis.set_ticks_position('left')
ax.spines['bottom'].set_position(('data', 0))
ax.spines['left'].set_position(('data', 0))

x0 = 1
y0 = 2*x0 + 1
# 指定点
plt.scatter(x0, y0, s=50, color='b')
plt.plot([x0, x0],[y0, 0], 'k--', lw=2.5)

# method1
plt.annotate(r'$2x+1=%s$'%y0, xy=(x0, y0), xycoords='data', xytext=(+30, -30), textcoords='offset points', fontsize=16, 
             arrowprops=dict(arrowstyle='->', connectionstyle='arc3, rad=.2'))
# method2
plt.text(-3.7, 3, r'$This\ is\ the\ some\ text.\ \mu\ \sigma_i\ \alpha_t$',
        fontdict={'size':16, 'color':'r'})
plt.show()
```


![png](https://cutoutsy-blog-1253675385.cos.ap-chengdu.myqcloud.com/output_10_0.png)


## tick 能见度


```python
x = np.linspace(-3, 3, 50)
y = 0.1*x

plt.figure()
plt.plot(x, y, linewidth=10)
plt.ylim(-2, 2)

ax = plt.gca()
ax.spines['right'].set_color('none')
ax.spines['top'].set_color('none')
ax.xaxis.set_ticks_position('bottom')
ax.yaxis.set_ticks_position('left')
ax.spines['bottom'].set_position(('data', 0))
ax.spines['left'].set_position(('data', 0))

for label in ax.get_xticklabels() + ax.get_yticklabels():
    label.set_fontsize(12)
    label.set_bbox(dict(facecolor='red', edgecolor='None', alpha=0.7))

plt.show()
```


![png](https://cutoutsy-blog-1253675385.cos.ap-chengdu.myqcloud.com/output_12_0.png)


## 散点图


```python
n = 1024
X = np.random.normal(0, 1, n)
Y = np.random.normal(0, 1, n)
T = np.arctan2(Y, X) # for color value

plt.scatter(X, Y, s=75, c=T, alpha=0.5)

plt.xlim(-1.5, 1.5)
plt.ylim(-1.5, 1.5)

plt.xticks(())
plt.yticks(())

plt.show()
```


![png](https://cutoutsy-blog-1253675385.cos.ap-chengdu.myqcloud.com/output_14_0.png)


## 柱状图


```python
n = 12
X = np.arange(n)
Y1 = (1 - X/float(n)) * np.random.uniform(0.5, 1.0, n)
Y2 = (1 - X/float(n)) * np.random.uniform(0.5, 1.0, n)

plt.bar(X, +Y1, facecolor='#9999ff', edgecolor='white')
plt.bar(X, -Y2, facecolor='#ff9999', edgecolor='white')

for x, y in zip(X, Y1):
    # ha: horizontal alignment
    plt.text(x, y + 0.05, '%.2f'% y, ha='center', va='bottom')

for x, y in zip(X, Y2):
    plt.text(x, -y - 0.05, '-%.2f'% y, ha='center', va='top')
    
plt.xlim(-.5, n)
plt.xticks(())
plt.ylim(-1.25, 1.25)
plt.yticks(())

plt.show()
```


![png](https://cutoutsy-blog-1253675385.cos.ap-chengdu.myqcloud.com/output_16_0.png)


## contours 等高线图


```python
def f(x, y):
    return (1 - x/2 + x**5 + y**3) * np.exp(-x**2-y**2)

n = 256
x = np.linspace(-3, 3, n)
y = np.linspace(-3, 3, n)

X, Y = np.meshgrid(x, y)

# 填充颜色
plt.contourf(X, Y, f(X, Y), 8, alpha=0.75, cmap=plt.cm.hot)

C = plt.contour(X, Y, f(X, Y), 8, colors='black')

plt.clabel(C, inline=True, fontsize=10)

plt.xticks(())
plt.yticks(())

plt.show()
```


![png](https://cutoutsy-blog-1253675385.cos.ap-chengdu.myqcloud.com/output_18_0.png)


## image 图片


```python
a = np.array([0.313660827978, 0.365348418405, 0.423733120134,
              0.365348418405, 0.439599930621, 0.525083754405,
              0.423733120134, 0.525083754405, 0.651536351379]).reshape(3,3)

plt.imshow(a, interpolation='nearest', cmap='bone', origin='lower')
plt.colorbar(shrink=0.9)
plt.xticks(())
plt.yticks(())
plt.show()
```


![png](https://cutoutsy-blog-1253675385.cos.ap-chengdu.myqcloud.com/output_20_0.png)


## 3D 数据


```python
from mpl_toolkits.mplot3d import Axes3D

fig = plt.figure()
ax = Axes3D(fig)

X = np.arange(-4, 4, 0.25)
Y = np.arange(-4, 4, 0.25)
X, Y = np.meshgrid(X, Y)
R = np.sqrt(X ** 2 + Y ** 2)

Z = np.sin(R)

ax.plot_surface(X, Y, Z, rstride=1, cstride=1, cmap=plt.get_cmap('rainbow'))
ax.contourf(X, Y, Z, zdir='z', offset=-2, cmap='rainbow')
ax.set_zlim(-2, 2)
plt.show()
```


![png](https://cutoutsy-blog-1253675385.cos.ap-chengdu.myqcloud.com/output_22_0.png)


## subplot 多合一显示


```python
plt.figure()

plt.subplot(2, 2, 1)
plt.plot([0, 1], [0, 1])

plt.subplot(2, 2, 2)
plt.plot([0, 1], [0, 1])

plt.subplot(2, 2, 3)
plt.plot([0, 1], [0, 1])

plt.subplot(2, 2, 4)
plt.plot([0, 1], [0, 1])

plt.show()
```


![png](https://cutoutsy-blog-1253675385.cos.ap-chengdu.myqcloud.com/output_24_0.png)


## 图中图


```python
fig = plt.figure()
x = [1, 2, 3, 4, 5, 6, 7]
y = [1, 3, 4, 2, 5, 8, 6]

left, bottom, width, height = 0.1,0.1,0.8,0.8
ax1 = fig.add_axes([left, bottom, width, height])

ax1.plot(x, y, 'r')
ax1.set_xlabel('x')
ax1.set_ylabel('y')
ax1.set_title('title')

left, bottom, width, height = 0.2,0.6,0.25,0.25
ax2 = fig.add_axes([left, bottom, width, height])

ax2.plot(x, y, 'b')
ax2.set_xlabel('x')
ax2.set_ylabel('y')
ax2.set_title('title inside 1')

plt.axes([.6, 0.2, 0.25, 0.25])
plt.plot(y[::-1], x, 'g')
plt.xlabel('x')
plt.ylabel('y')
plt.title('title inside 2')

plt.show()
```


![png](https://cutoutsy-blog-1253675385.cos.ap-chengdu.myqcloud.com/output_26_0.png)


## 次坐标轴


```python
x = np.arange(0, 10, 0.1)
y1 = 0.05 * x ** 2
y2 = -1 * y1

fig, ax1 = plt.subplots()
ax2 = ax1.twinx()
ax1.plot(x, y1, 'g-')
ax2.plot(x, y2, 'b--')

ax1.set_xlabel('X data')
ax1.set_ylabel('Y1', color='g')
ax2.set_ylabel('Y2', color='b')

plt.show()
```


![png](https://cutoutsy-blog-1253675385.cos.ap-chengdu.myqcloud.com/output_28_0.png)


## animation 动画


```python
from matplotlib import animation

fig, ax = plt.subplots()

x = np.arange(0, 2 * np.pi, 0.01)
line, = ax.plot(x, np.sin(x))

def animate(i):
    line.set_ydata(np.sin(x + i/100))
    return line,

def init():
    line.set_ydata(np.sin(x))
    return line,

ani = animation.FuncAnimation(fig=fig, func=animate, frames=100, init_func=init, interval=20, blit=False)

plt.show()
```


![png](https://cutoutsy-blog-1253675385.cos.ap-chengdu.myqcloud.com/output_30_0.png)


