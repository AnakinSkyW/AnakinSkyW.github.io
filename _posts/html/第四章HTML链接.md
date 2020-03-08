# 链接允许从网页跳转到另一个网页

## 1.编写链接

链接是由```<a>```元素建立。使用href特性来指定要链接的页面

```html
<a href="http://cn.bing.com">IMDB</a>
```

## 2.指向其他网站的链接

在```<a>```元素中如果链接指向另一个网站，那么**href**特性的值必须是另一个网站完整的Web地址

```html
<p>Movie Reviews:
    <ul>
        <li><a href="http://www.empireonline.com">Empire</a></li>
        <li><a href="http://www.metacritic.com">Metacritic</a></li>
    </ul>
</p>
```

## 3.指向同一网站中其他页面的链接

如果网站的所有页面在同一文件夹中，那么**href**特性的值就是页面的文件名

```html
<p>
    <ul>
        <li><a href="index.html">Home</a></li>
        <li><a href="About.html">About</a></li>
    </ul>
</p>
```

## 4.目录结构

对于较大的网站而言，管理代码时，合理的方式是将不同类别的页面保存在不同的文件夹中。

## 5.相对URL

相对URL可对于为网站内部的页面之间建立链接。

```html
<p>
    <ul>
        <li><a href="reviews.html">Reviews</a></li>
        <li><a href="music/listings.html">Listings</a></li>
        <li><a href="../index.html">Home</a></li>
    </ul>
</p>
```

## 6.EMAIL链接

用```<a>```元素建立链接。以**href**特性值以```mailto:```开始

```html
<a href="mialto:anakinsky0808@gmail.com">Email</a>
```

## 7.新窗口打开链接

新窗口打开链接，需要用到```<a>```标签中的**target**特性并设置为`_blank`

```html
<a href="http://cn.bing.comm" target="_blank">Internet Movie Database</a>
```

## 8.链接到当前页面的特定位置

链接到使用**id**特性的元素需要使用```<a>```元素，不同的是它的**href**特性值以`#`开头，后面跟着元素**id**

```html
<h1 id="top">Film-Making Terms</h1>
<a href="#arc_shot">Arc Shot</a><br />
<h2 id="arc_shot">Arc Shot</h2>
<a href="#top">Top</a>
```

## 9.链接到其他页面的某个特定位置

同**8**一样，可以在目标页面地址后边使用相同的语法

```html
<a href="http://cn.bing.com#bottom">Bottom</a>
```

## 10.小结

>* 链接是有`<a>`元素创建的
>* 通过**href**特性来指明链接的页面
>* 可使用相对链接链接内部页面
>* 可以创建电子邮件地址的链接
>* 可以通过**id**特性作为链接目标
