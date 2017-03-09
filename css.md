<!--
@Author: zhouningyi
@Date:   2016-09-22T16:13:13+08:00
@Last modified by:   zhouningyi
@Last modified time: 2016-10-28T18:02:36+08:00
-->



# css笔记

### 规范
[airbnb css规范 CN](https://github.com/Zhangjd/css-style-guide)


### 符号
`>` 作用于元素第二级 但对第三级没啥作用
```css
.a > div { ... }
```


### 小技巧

```css
-webkit-tap-highlight-color: false
```
scroll bar的几种[样式](https://css-tricks.com/custom-scrollbars-in-webkit/)调节, 如:

```css
::-webkit-scrollbar: { width:12px;  background: #f00}
```

让border/padding不计入宽度计算
```css
{
    box-sizing:border-box;
}
```

css3的混合模式:
```css
mix-blend-mode: normal;          //正常
mix-blend-mode: multiply;        //正片叠底
mix-blend-mode: screen;          //滤色
mix-blend-mode: overlay;         //叠加
mix-blend-mode: darken;          //变暗
mix-blend-mode: lighten;         //变亮
mix-blend-mode: color-dodge;     //颜色减淡
mix-blend-mode: color-burn;      //颜色加深
mix-blend-mode: hard-light;      //强光
mix-blend-mode: soft-light;      //柔光
mix-blend-mode: difference;      //差值
mix-blend-mode: exclusion;       //排除
mix-blend-mode: hue;             //色相
mix-blend-mode: saturation;      //饱和度
mix-blend-mode: color;           //颜色
mix-blend-mode: luminosity;      //亮度

mix-blend-mode: initial;         //初始
mix-blend-mode: inherit;         //继承
mix-blend-mode: unset;           //复原
```

### flex布局
[一个完整的Flexbox指南](http://www.w3cplus.com/css3/a-guide-to-flexbox-new.html)
[flex的一些应用](http://www.ruanyifeng.com/blog/2015/07/flex-examples.html)

Flex属性分容器属性和项目属性

#### 容器属性:

```css
/*一个常见的flex布局*/
{
    display: flex;
    flex-direction: row; /*排列方向 row column 以及其reverse */
    flex-wrap: wrap;/*no-wrap是默认的 一行把所有内容写完， wap是一行写到底换行*/
    justify-content: /*flex-start flex-end center space-between space-around;*/
    align-items: /*flex-start flex-end center baseline stretch;*/
    align-content: stretch; /*flex-start flex-end center space-between space-around stretch*/
}
```

其中 `flex-direction` 和 `flex-wrap` 可以合并为 `flex-flow`, 其默认为 `row nowrap`

justify-content主要管x方向的排列， `space-between` 是把元素之间的距离均分掉, 而`space-around`还会给两边留空间


![image](./imgs/justify-content.svg)



align-items主要管y轴的排列, `sctretch` 是尽量接近container的大小

![image](./imgs/align-items.svg)


align-content 如果容器有空间空余，通过此调节

![image](./imgs/align-content.svg)


#### 项目属性:
flex是其他几个属性的缩写
```css
{
    order: 1;/*排列顺序*/
    flex-grow: 0;/*相对的大小*/
    flex-shrink: 1;/*缩放*/
    flex-basis: auto; /*Flex项目在分配Flex容器剩余空间之前的一个默认尺寸*/
}

{
    flex: /*none | [ <'flex-grow'> <'flex-shrink'>? || <'flex-basis'> ]*/
}
```


`align-self`是可以重写container所规定的 align-items


#### 一些例子
```css
/*自适应*/
.navigation {
  display: flex;
  flex-flow: row wrap;
  justify-content: flex-end;
}

/* Medium screens */
@media all and (max-width: 800px) {
  .navigation {
    /* When on medium sized screens, we center it by evenly distributing empty space around items */
    justify-content: space-around;
  }
}

/* Small screens */
@media all and (max-width: 500px) {
  .navigation {
    /* On small screens, we are no longer using row direction but column */
    flex-direction: column;
  }
}

```



### 工具
[使用postcss CN](http://www.ibm.com/developerworks/cn/web/1604-postcss-css/)
