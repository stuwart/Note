## Matplotlib

### 一

Matplotlib有两种绘图方式：

+ 显式创建`figure`和`axes`，在上面调用绘图方法，也被称为OO模式
+ 依赖`pyplot`自动创建`figure`和`axes`，并绘图

1.**OO模式**

**优点**：通过显式创建一个`figure`，并且进行绘图，结构较为清晰，可扩展性强

**缺点**：代码相对较为复杂

2.**pyplot**

**优点**：较为简洁，可以用作交互性绘图

**缺点**：进行大型绘图时不太方便



#### 绘图模板

**OO模式**

```python
# step1 准备数据
x = np.linspace(0, 2, 100)
y = x**2

# step2 设置绘图样式，这一模块的扩展参考第五章进一步学习，这一步不是必须的，样式也可以在绘制图像是进行设置
mpl.rc('lines', linewidth=4, linestyle='-.')

# step3 定义布局， 这一模块的扩展参考第三章进一步学习
fig, ax = plt.subplots()  

# step4 绘制图像， 这一模块的扩展参考第二章进一步学习
ax.plot(x, y, label='linear')  

# step5 添加标签，文字和图例，这一模块的扩展参考第四章进一步学习
ax.set_xlabel('x label') 
ax.set_ylabel('y label') 
ax.set_title("Simple Plot")  
ax.legend() ;
```

**pyplot**

```python
# step1 准备数据
x = np.linsapce(0,1,100)
y = x*x

#step 2 绘制图形，并设置绘图样式
plt.plot(x,x,label='linear')  #图例为linear
plt.plot(x,x**2,label='quadratic') #图例为 quadratic


#step3 显示图例，显示图形
plt.legend()
plt.show()
```



> 第一节学习心得：第一次学习初步了解了Matplotlib的概念以及一些基本元素，还有一些基础的用法，并且学习了通用模板等知识，能够对Matplotlib有一定的大致的了解。



### 二

可能会用到的库：

```python
import numpy as np
import pandas as pd
import re
import matplotlib
import matplotlib.pyplot as plt
from matplotlib.lines import Line2D   
from matplotlib.patches import Circle, Wedge
from matplotlib.collections import PatchCollection
```

#### 三层API

+ `matplotlib.backend_bases.FigureCanvas` ：绘图区，相当于画布
+ `matplotlib.backend_bases.Renderer` ：渲染器，相当于画笔
+ `matplotlib.artist.Artist` ：绘图组件，相当于手



#### Artist分类

+ `primitives` ：基本要素，包含例如曲线`Line2D`，文字`text`等
+ `conrainers` ：容器，用来装要素的地方，包括`figure`，`Axes`,`Axis`等，组成如下：

[![vj1dXV.png](https://s1.ax1x.com/2022/09/13/vj1dXV.png)](https://imgse.com/i/vj1dXV)



#### Primitives

##### 2DLines 

曲线绘制，通过`matplotlib.lines.Line2D`完成。

**常用参数**：

+ **xdata**:需要绘制的line中点的在x轴上的取值，若忽略，则默认为range(1,len(ydata)+1)
+ **ydata**:需要绘制的line中点的在y轴上的取值
+ **linewidth**:线条的宽度
+ **linestyle**:线型
+ **color**:线条的颜色
+ **marker**:点的标记，详细可参考[markers API](https://matplotlib.org/api/markers_api.html#module-matplotlib.markers)
+ **markersize**:标记的size

**设置属性**

有三种方法：

+ 直接在`plot()`函数中设置

```python
# 1.直接在plot()中设置
x = range(0,10)  #获得从0到9的整数数组
y = [1,2,3,4,5]
ply.plot(x,y, color = 'b'); # 设置线条颜色为蓝色 
```



+ 获得线条对象，设置对象

```python
# 2. 设置对象
x = range(0,10)
y = [1,2,3,4,5]
line , = plt.plot(x,y,'-')  #将Line对象赋值给line,类似于参数解构
line.set_antialiased(False); #关闭抗锯齿功能
```



+ 获得线条属性，使用`setp()`函数设置

```python
# 3. 设置属性
x = range(0,10)
y = [1,2,3,4,5]
lines = plt.plot(x,y)
plt.setp(lines,coloe = 'r', linewidth=12);
```



**绘制Lines**

1.直线**line**

+ `plot`方法
+ `line2D`对象

2.**errorbar**误差折线图

主要参数：

- **x**：需要绘制的line中点的在x轴上的取值
- **y**：需要绘制的line中点的在y轴上的取值
- **yerr**：指定y轴水平的误差
- **xerr**：指定x轴水平的误差
- **fmt**：指定折线图中某个点的颜色，形状，线条风格，例如‘co--’
- **ecolor**：指定error bar的颜色
- **elinewidth**：指定error bar的线条宽度

绘制方法：

```python
fig = plt.figure()
x = np.arange(10)
y = 2.5*np.sin(x/20+np.pi)
yerr = np.linspace(0.05,0.2,100)
plt.errorbar(x,y*y,yerr=yerr,fmt='o-', ecolor='r', elinewidth=2);
```



##### patches

二维图形类，主要学习 **矩形，多边形和楔形**

**Recrangle--矩形**

xy控制锚点，width、height控制宽高

1.`hist`直方图

主要参数：

- **x**: 数据集，最终的直方图将对数据集进行统计
- **bins**: 统计的区间分布
- **range**: tuple, 显示的区间，range在没有给出bins时生效
- **density**: bool，默认为false，显示的是频数统计结果，为True则显示频率统计结果，这里需要注意，频率统计结果=区间数目/(总数*区间宽度)，和normed效果一致，官方推荐使用density
- **histtype**: 可选{'bar', 'barstacked', 'step', 'stepfilled'}之一，默认为bar，推荐使用默认配置，step使用的是梯状，stepfilled则会对梯状内部进行填充，效果与bar类似
- **align**: 可选{'left', 'mid', 'right'}之一，默认为'mid'，控制柱状图的水平分布，left或者right，会有部分空白区域，推荐使用默认
- **log**: bool，默认False,即y坐标轴是否选择指数刻度
- **stacked**: bool，默认为False，是否为堆积状图

```python
# Rectangle矩形类绘制直方图
df = pd.DataFrame(columns = ['data'])
df.loc[:,'data'] = x
df['fenzu'] = pd.cut(df['data'], bins=bins, right = False,include_lowest=True)

df_cnt = df['fenzu'].value_counts().reset_index()
df_cnt.loc[:,'mini'] = df_cnt['index'].astype(str).map(lambda x:re.findall('\[(.*)\,',x)[0]).astype(int)
df_cnt.loc[:,'maxi'] = df_cnt['index'].astype(str).map(lambda x:re.findall('\,(.*)\)',x)[0]).astype(int)
df_cnt.loc[:,'width'] = df_cnt['maxi']- df_cnt['mini']
df_cnt.sort_values('mini',ascending = True,inplace = True)
df_cnt.reset_index(inplace = True,drop = True)

#用Rectangle把hist绘制出来

fig = plt.figure()
ax1 = fig.add_subplot(111)

for i in df_cnt.index:
    rect =  plt.Rectangle((df_cnt.loc[i,'mini'],0),df_cnt.loc[i,'width'],df_cnt.loc[i,'fenzu'])
    ax1.add_patch(rect)

ax1.set_xlim(0, 100)
ax1.set_ylim(0, 16);
```



2.`bar`条形图

主要参数

- **left**：x轴的位置序列，一般采用range函数产生一个序列，但是有时候可以是字符串
- **height**：y轴的数值序列，也就是柱形图的高度，一般就是我们需要展示的数据；
- **alpha**：透明度，值越小越透明
- **width**：为柱形图的宽度，一般这是为0.8即可；
- **color或facecolor**：柱形图填充的颜色；
- **edgecolor**：图形边缘颜色
- **label**：解释每个图像代表的含义，这个参数是为legend()函数做铺垫的，表示该次bar的标签

**绘制**：

```python
# Rectangle矩形类绘制柱状图
fig = plt.figure()
ax1 = fig.add_subplot(111)

for i in range(1,17):
    rect =  plt.Rectangle((i+0.25,0),0.5,i)  #参数分别指((x,y),width,height)
    ax1.add_patch(rect)  #相当于添加图层
ax1.set_xlim(0, 16)
ax1.set_ylim(0, 16);
```



**Polygon--多边形**

构造函数：

```python
class matplotlib.patches.Polygon(xy, closed=True, **kwargs)
```

主要参数：

+ xy：是一个Nx2的`array`，是多边形的顶点
+ **closed**：True代表将起点与终点重合而显示关闭多边形

绘制：

```python
# 用fill来绘制图形
x = np.linspace(0, 5 * np.pi, 1000) 
y1 = np.sin(x)
y2 = np.sin(2 * x) 
plt.fill(x, y1, color = "g", alpha = 0.3);
```



**Wedge--楔形**

构造函数：

```python
class matplotlib.patches.Wedge(center, r, theta1, theta2, width=None, **kwargs)
```

绘图是以`(x,y)`为中心，半径为`r`，从`θ1`扫到`θ2`。

主要参数：

- **x**：楔型的形状，一维数组。
- **explode**：如果不是等于None，则是一个len(x)数组，它指定用于偏移每个楔形块的半径的分数。
- **labels**：用于指定每个楔型块的标记，取值是列表或为None。
- **colors**：饼图循环使用的颜色序列。如果取值为None，将使用当前活动循环中的颜色。
- **startangle**：饼状图开始的绘制的角度。

```python
# pie绘制饼状图
labels = 'Frogs', 'Hogs', 'Dogs', 'Logs'
sizes = [15, 30, 45, 10] 
explode = (0, 0.1, 0, 0) 
fig1, ax1 = plt.subplots() 
ax1.pie(sizes, explode=explode, labels=labels, autopct='%1.1f%%', shadow=True, startangle=90) 
ax1.axis('equal'); # Equal aspect ratio ensures that pie is drawn as a circle.

# wedge类绘制
fig = plt.figure(figsize=(5,5))
ax1 = fig.add_subplot(111)
theta1 = 0
sizes = [15, 30, 45, 10] 
patches = []
patches += [
    Wedge((0.5, 0.5), .4, 0, 54),           
    Wedge((0.5, 0.5), .4, 54, 162),  
    Wedge((0.5, 0.5), .4, 162, 324),           
    Wedge((0.5, 0.5), .4, 324, 360),  
]
colors = 100 * np.random.rand(len(patches))
p = PatchCollection(patches, alpha=0.8)
p.set_array(colors)
ax1.add_collection(p);
```



##### collections

用来绘制一组对象的集合，比较常用的是散点图，其构造方法是：

```python
Axes.scatter(self, x, y, s=None, c=None, marker=None, cmap=None, norm=None, vmin=None, vmax=None, alpha=None, linewidths=None, verts=, edgecolors=None, *, plotnonfinite=False, data=None, **kwargs)
```

主要参数：

- **x**：数据点x轴的位置
- **y**：数据点y轴的位置
- **s**：尺寸大小
- **c**：可以是单个颜色格式的字符串，也可以是一系列颜色
- **marker**: 标记的类型

绘制：

```python
# 用scatter绘制散点图
x = [0,2,4,6,8,10] 
y = [10]*len(x) 
s = [20*2**n for n in range(len(x))] 
plt.scatter(x,y,s=s) ;
```



##### images

用于绘制图像，常用`imshow`根据数组绘制图像，其构造函数：

```python
matplotlib.pyplot.imshow(X, cmap=None, norm=None, aspect=None, interpolation=None, alpha=None, vmin=None, vmax=None, origin=None, extent=None, shape=, filternorm=1, filterrad=4.0, imlim=, resample=None, url=None, *, data=None, **kwargs）
```

绘制：

```python
methods = [None, 'none', 'nearest', 'bilinear', 'bicubic', 'spline16',
           'spline36', 'hanning', 'hamming', 'hermite', 'kaiser', 'quadric',
           'catrom', 'gaussian', 'bessel', 'mitchell', 'sinc', 'lanczos']


grid = np.random.rand(4, 4)

fig, axs = plt.subplots(nrows=3, ncols=6, figsize=(9, 6),
                        subplot_kw={'xticks': [], 'yticks': []})

for ax, interp_method in zip(axs.flat, methods):
    ax.imshow(grid, interpolation=interp_method, cmap='viridis')
    ax.set_title(str(interp_method))

plt.tight_layout();
```



#### Object Container

容器是包容元素的地方，其自身也有许多属性。

##### Figure

`Figure`是最顶层的`container`容器，包含了图表的所有元素。

添加元素到`Figure.axes`列表中

```python
Figure.add_subplot()
Figure.add_axes()
```

删除元素：

```python
Figure.delaxes();
```

迭代/访问元素：

```python
for ax in fig.axes:
    ax.grid(True)  #添加网格线
```

**常见属性**

+ `Figure.patch`属性：Figure的背景矩形
+ `Figure.axes`属性：一个Axes实例的列表（包括Subplot)
+ `Figure.images`属性：一个FigureImages patch列表
+ `Figure.lines`属性：一个Line2D实例的列表（很少使用）
+ `Figure.legends`属性：一个Figure Legend实例列表（不同于Axes.legends)
+ `Figure.texts`属性：一个Figure Text实例列表



##### Axes

本身包含`patch`属性，相对笛卡尔坐标系是一个矩形，相对极坐标是一个圆

```python
fig = plt.figure()
ax = fig.add_subplot(111)
rect = ax.patch  # axes的patch是一个Rectangle实例
rect.set_facecolor('green')
```

subplot：

相当于子图，存在于某个区域的示例

最重要的container：

+ `ax.xaxis`：XAxis对象的实例，用于处理x轴tick以及label的绘制
+ `ax.yaxis`：YAxis对象的实例，用于处理y轴tick以及label的绘制

常见属性：

+ `artists`: Artist实例列表
+ `patch`: Axes所在的qqqqqqqqqqqqqqqq矩形实例
+ `collections`: Collection实例
+ `images`: Axes图像
+ `legends`: Legend 实例
+ `lines`: Line2D 实例
+ `patches`: Patch 实例
+ `texts`: Text 实例
+ `xaxis`: matplotlib.axis.XAxis 实例
+ `yaxis`: matplotlib.axis.YAxis 实例



##### **Axis**

包含坐标轴的信息。

```python
# 不用print，直接显示结果
from IPython.core.interactiveshell import InteractiveShell
InteractiveShell.ast_node_interactivity = "all"

fig, ax = plt.subplots()
x = range(0,5)
y = [2,5,7,8,10]
plt.plot(x, y, '-')

axis = ax.xaxis # axis为X轴对象
axis.get_ticklocs()     # 获取刻度线位置
axis.get_ticklabels()   # 获取刻度label列表(一个Text实例的列表）。 可以通过minor=True|False关键字参数控制输出minor还是major的tick label。
axis.get_ticklines()    # 获取刻度线列表(一个Line2D实例的列表）。 可以通过minor=True|False关键字参数控制输出minor还是major的tick line。
axis.get_data_interval()# 获取轴刻度间隔
axis.get_view_interval()# 获取轴视角（位置）的间隔
```



```python
fig = plt.figure() # 创建一个新图表
rect = fig.patch   # 矩形实例并将其设为黄色
rect.set_facecolor('lightgoldenrodyellow')

ax1 = fig.add_axes([0.1, 0.3, 0.4, 0.4]) # 创一个axes对象，从(0.1,0.3)的位置开始，宽和高都为0.4，
rect = ax1.patch   # ax1的矩形设为灰色
rect.set_facecolor('lightslategray')


for label in ax1.xaxis.get_ticklabels(): 
    # 调用x轴刻度标签实例，是一个text实例
    label.set_color('red') # 颜色
    label.set_rotation(45) # 旋转角度
    label.set_fontsize(16) # 字体大小

for line in ax1.yaxis.get_ticklines():
    # 调用y轴刻度线条实例, 是一个Line2D实例
    line.set_markeredgecolor('green')    # 颜色
    line.set_markersize(25)    # marker大小
    line.set_markeredgewidth(2)# marker粗细
```



##### Tick

包含`tick`,`grid line`以及对应的`label`
常见属性：

+ `Tick.tick1line`：Line2D实例
+ `Tick.tick2line`：Line2D实例
+ `Tick.gridline`：Line2D实例
+ `Tick.label1`：Text实例
+ `Tick.label2`：Text实例





#### 思考题

**primitives**和 **container**的区别和联系是什么，分别用于控制可视化图表中的哪些要素?

区别：**primitives**是元素（图形），而**container** 是装元素的容器（画布）





> 第二节学习心得：第二章节主要介绍了元素和容器，介绍了基本的绘图方法和类型，内容较为完备，但是知识比较多，需要多加运用才能进行一个比较好的掌握。































