# Flex基础详解
## 一、Flex 布局是什么？

> Flex 是 Flexible Box 的缩写，意为"弹性布局"，用来为盒状模型提供最大的灵活性。

1. 任何一个容器都可以指定为 Flex 布局。

   ```css
   .box{
     display: flex;
   }
   ```

2. 行内元素也可以使用 Flex 布局。

   ```css
   .box{
     display: inline-flex;
   }
   ```

3. Webkit 内核的浏览器，必须加上`-webkit`前缀。

   ```css
   .box{
     display: -webkit-flex; /* Safari */
     display: flex;
   }
   ```

## 二、 基本概念

> 采用 Flex 布局的元素，称为 Flex 容器（flex container），简称"容器"。它的所有子元素自动成为容器成员，称为 Flex 项目（flex item），简称"项目"。
>
> ![Alt text](C:\Users\Administrator\Desktop\flex\bg2015071004.png)
>
> 容器默认存在两根轴：水平的主轴（main axis）和垂直的交叉轴（cross axis）。主轴的开始位置（与边框的交叉点）叫做`main start`，结束位置叫做`main end`；交叉轴的开始位置叫做`cross start`，结束位置叫做`cross end`。
>
> 项目默认沿主轴排列。单个项目占据的主轴空间叫做`main size`，占据的交叉轴空间叫做`cross size`。

## 三、容器的属性

> 容器有以下6个属性：
>
>  1. flex-direction（属性决定主轴的方向（即项目的排列方向））
>
>     > ```css
>     > .box {
>     >   flex-direction: row | row-reverse | column | column-reverse;
>     > }
>     > ```
>     >
>     > ![Alt text](C:\Users\Administrator\Desktop\flex\bg2015071005.png)
>     >
>     > > 1. `row`（默认值）：主轴为水平方向，起点在左端。
>     > > 2. `row-reverse`：主轴为水平方向，起点在右端。
>     > > 3. `column`：主轴为垂直方向，起点在上沿。
>     > > 4. `column-reverse`：主轴为垂直方向，起点在下沿
>
>  2. flex-wrap（默认情况下，项目都排在一条线（又称"轴线"）上。`flex-wrap`属性定义，如果一条轴线排不下，如何换行）
>
>     > 默认情况下，项目都排在一条线（又称"轴线"）上。`flex-wrap`属性定义，如果一条轴线排不下，如何换行。
>     >
>     > ![Alt text](C:\Users\Administrator\Desktop\flex\bg2015071006.png)
>     >
>     > ```css
>     > .box{
>     >   flex-wrap: nowrap | wrap | wrap-reverse;
>     > }
>     > ```
>     >
>     > > 1. （1）`nowrap`（默认）：不换行。
>     > >
>     > >    ![Alt text](C:\Users\Administrator\Desktop\\md\flex\bg2015071007.png)
>     > >
>     > > 2. `wrap`：换行，第一行在上方。
>     > >
>     > >    ![Alt text](C:\Users\Administrator\Desktop\\md\flex\bg2015071008.jpg)
>     > >
>     > > 3. `wrap-reverse`：换行，第一行在下方。
>     > >
>     > >    ![Alt text](C:\Users\Administrator\Desktop\md\flex\bg2015071009.jpg)
>
>  3. flex-flow（`flex-flow`属性是`flex-direction`属性和`flex-wrap`属性的简写形式，默认值为`row nowrap`）
>
>     > ```css
>     > .box {
>     >   flex-flow: <flex-direction> || <flex-wrap>;
>     > }
>     > ```
>
>  4. justify-content属性（`justify-content`属性定义了项目在主轴上的对齐方式）
>
>     > ```css
>     > .box {
>     >   justify-content: flex-start | flex-end | center | space-between | space-around;
>     > }
>     > ```
>     >
>     > 1. `flex-start`（默认值）：左对齐
>     > 2. `flex-end`：右对齐
>     > 3. `center`： 居中
>     > 4. `space-between`：两端对齐，项目之间的间隔都相等。
>     > 5. `space-around`：每个项目两侧的间隔相等。所以，项目之间的间隔比项目与边框的间隔大一倍。
>     >
>     > ![Alt text](C:\Users\Administrator\Desktop\flex\bg2015071010.png)
>     >
>     > 
>
>  5. align-items属性（`align-items`属性定义项目在交叉轴上如何对齐）
>
>     ```css
>     .box {
>       align-items: flex-start | flex-end | center | baseline | stretch;
>     }
>     ```
>
>     1. `flex-start`：交叉轴的起点对齐。
>     2. `flex-end`：交叉轴的终点对齐。
>     3. `center`：交叉轴的中点对齐。
>     4. `baseline`: 项目的第一行文字的基线对齐。
>     5. `stretch`（默认值）：如果项目未设置高度或设为auto，将占满整个容器的高度。
>
>     ![Alt text](C:\Users\Administrator\Desktop\flex\bg2015071010.png)
>
>  6. align-content属性（`align-content`属性定义了多根轴线的对齐方式。如果项目只有一根轴线，该属性不起作用。）

## 四、子级的属性



1. `order`（`order`属性定义项目的排列顺序。数值越小，排列越靠前，默认为0）

   ```css
   .item {
     order: <integer>;
   }
   ```

   ![Alt text](C:\Users\Administrator\Desktop\\md\flex\bg2015071013.png)

2. `flex-grow`（属性定义项目的放大比例，默认为`0`，即如果存在剩余空间，也不放大）

   ```css
   .item {
     flex-grow: <number>; /* default 0 */
   }
   ```

   ![Alt text](C:\Users\Administrator\Desktop\md\flex\bg2015071014.png)

3. `flex-shrink`（属性定义了项目的缩小比例，默认为1，即如果空间不足，该项目将缩小）

   ```css
   .item {
     flex-shrink: <number>; /* default 1 */
   }
   ```

   ![Alt text](C:\Users\Administrator\Desktop\md\flex\bg2015071015.jpg)

   > 注意：**如果所有项目的`flex-shrink`属性都为1，当空间不足时，都将等比例缩小。如果一个项目的`flex-shrink`属性为0，其他项目都为1，则空间不足时，前者不缩小。负值对该属性无效。**

4. `flex-basis`（属性定义了在分配多余空间之前，项目占据的主轴空间（main size）。浏览器根据这个属性，计算主轴是否有多余空间。它的默认值为`auto`，即项目的本来大小。）

   > ```css
   > .item {
   >   flex-basis: <length> | auto; /* default auto */
   > }
   > ```
   >
   > **注意：可以设为跟`width`或`height`属性一样的值（比如350px），则项目将占据固定空间。**

5. `flex`（属性是`flex-grow`, `flex-shrink` 和 `flex-basis`的简写，默认值为`0 1 auto`。后两个属性可选）

   ```css
   .item {
     flex: none | [ <'flex-grow'> <'flex-shrink'>? || <'flex-basis'> ]
   }
   ```

   该属性有两个快捷值：`auto` (`1 1 auto`) 和 none (`0 0 auto`)。

6. `align-self`（属性允许单个项目有与其他项目不一样的对齐方式，可覆盖`align-items`属性。默认值为`auto`，表示继承父元素的`align-items`属性，如果没有父元素，则等同于`stretch`）

   ```css
   .item {
     align-self: auto | flex-start | flex-end | center | baseline | stretch;
   }
   ```

   ![Alt text](C:\Users\Administrator\Desktop\md\flex\bg2015071016.png)
# 实战详解

   

##  一颗骰子的布局

### 只有左上角1个点的情况

![Alt text](C:\Users\Administrator\Desktop\md\flex\bg2015071301.png)

```css
.box {
  display: flex;
}
```

### 居中对齐

![Alt text](C:\Users\Administrator\Desktop\md\flex\bg2015071302.png)

```css
.box {
  display: flex;
  justify-content: center;
}
```

### 居右对齐

![Alt text](C:\Users\Administrator\Desktop\md\flex\bg2015071303.png)

```css
.box {
  display: flex;
  justify-content: flex-end;
}
```

### 交叉轴左侧居中对齐

![Alt text](C:\Users\Administrator\Desktop\md\flex\bg2015071304.png)

```css
.box {
  display: flex;
  align-items: center;
}
```

### 交叉轴居中对齐

![Alt text](C:\Users\Administrator\Desktop\md\flex\bg2015071305.png)

```css
.box {
  display: flex;
  justify-content: center;
  align-items: center;
}
```

### 底部居中对齐

![Alt text](C:\Users\Administrator\Desktop\md\flex\bg2015071306.png)

```css
.box {
  display: flex;
  justify-content: center;
  align-items: flex-end;
}
```

### 底部右侧对齐

![Alt text](C:\Users\Administrator\Desktop\md\flex\bg2015071307.png)

```css
.box {
  display: flex;
  justify-content: flex-end;
  align-items: flex-end;
}
```

## 两颗骰子的布局

![Alt text](C:\Users\Administrator\Desktop\md\flex\bg2015071308.png)

```css
.box {
  display: flex;
  justify-content: space-between;
}
```

![Alt text](C:\Users\Administrator\Desktop\md\flex\bg2015071309.png)

```css
.box {
  display: flex;
  flex-direction: column;
  justify-content: space-between;
}
```

![Alt text](C:\Users\Administrator\Desktop\md\flex\bg2015071310.png)

```css
.box {
  display: flex;
  flex-direction: column;
  justify-content: space-between;
  align-items: center;
}
```

![Alt text](C:\Users\Administrator\Desktop\md\flex\bg2015071311.png)

```css
.box {
  display: flex;
  flex-direction: column;
  justify-content: space-between;
  align-items: flex-end;
}
```

![Alt text](C:\Users\Administrator\Desktop\md\flex\bg2015071312.png)

```css
.box {
  display: flex;
}

.item:nth-child(2) {
  align-self: center;
}
```



![Alt text](C:\Users\Administrator\Desktop\md\flex\bg2015071313 (1).png)

```css
.box {
  display: flex;
  justify-content: space-between;
}

.item:nth-child(2) {
  align-self: flex-end;
}
```

## 三颗骰子的布局

![Alt text](C:\Users\Administrator\Desktop\md\flex\bg2015071314.png)

```css
.box {
  display: flex;
}

.item:nth-child(2) {
  align-self: center;
}

.item:nth-child(3) {
  align-self: flex-end;
}
```

## 四颗骰子的布局

![Alt text](C:\Users\Administrator\Desktop\md\flex\bg2015071315.png)

```css
.box {
  display: flex;
  flex-wrap: wrap;
  justify-content: flex-end;
  align-content: space-between;
}
```

## 网格布局

GFM [file:///F:/home/flex%E5%B8%83%E5%B1%80/Flex%20%E5%B8%83%E5%B1%80%E6%95%99%E7%A8%8B%EF%BC%9A%E5%AE%9E%E4%BE%8B%E7%AF%87%20-%20%E9%98%AE%E4%B8%80%E5%B3%B0%E7%9A%84%E7%BD%91%E7%BB%9C%E6%97%A5%E5%BF%97.html](file:///F:/home/flex布局/Flex 布局教程：实例篇 - 阮一峰的网络日志.html)