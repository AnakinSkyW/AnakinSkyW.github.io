# 使用多类型的表单控件为网站收集来自访问者的信息

## 1.表单结构

表单控件位于`<form>`元素中，每个`<form>`元素都应该设置`action`特性，通常还有`method`和`id`。`action`特性值是服务器上的URL，用来接收表单中的信息。`method`表示提交方式，可以为`get`或者`post`。`id`表示元素的唯一特性。

```html
<form action="http://example.com/subsriber.php" method="get">
<p>This is where the form controller will appear.</p>
</form>
```

## 2.单行文本框

`<input>`用来创建表单控件，其`type`特性决定他将要用那种控件：  
`type`为`text`时，`<input>`是单行文本框，`name`特性标识表单控件，`size`特性标识文本宽度，`maxlength`特性限制文本区域输入字符数量。  
`type`特性值为`password`时，输入字符将被隐藏起来。  
`type`特性值为`radio`时是单选框，`value`特性为选项指定了被选中的值，`checked`特性用来指定当前页面加载时哪个值被选中。  
`type`特性值为`checkbox`，复选框允许用户多选。  
`type`特性值为`file`，后边附有**Browse**按钮用来打开新窗口选择文件。  
`type`特性值为`submit`，将表单发送到服务器。  
`type`特性值为`image`，同样具有`src`、`width`、`height`特性与`<img>`一致。  
`type`特性值为`hidden`时可隐藏控件  
`type`特性值为`email`会校验输入内容合法性  
`type`特性值为`url`同上校验输入内容为链接  
`type`特性值为`search`标识搜索内容，**placeholder**可显示为文本框中的内容

```html
<form action="http://www.example.com/login.php">
    <p>Username:<input type="text" name="username" size="15" maxlength="30"/></p>
    <p>Password:<input type="password" name="password" size="15" maxlength="30"/></p>
    <input type="radio" name="genre" value="pop"/>Pop
    <input type="radio" name="genre" value="jazz">Jazz
</form>
<form action="http://www.example.com/upload.php" method="post">
    <p>Upload your song in MP3 format:</p>
    <input type="file" name="suer"/><br />
    <input type="submit" value="Upload"/>
```

## 3.文本域(多行文本框)

`<textarea>`元素用来创建多行文本框

```html
<form action="http://www.example.com/comments.php">
    <p>What did you think of this gig?</p>
    <textarea name="comments" cols="20" rows="4">Enter your comments...</textarea>
</form>
```

## 4.下拉列表

`<select>`元素用来创建下拉列表，它包含两个以上的`<option>`元素，同样具有`name`和`value`特性，`selected`特性用来指定当前页面默认被选中的列。使用`size`特性可变为多个显示选项。`multiple`允许用户多选。

```html
<form action="http://www.example.com/profile.php">
    <p>What device do you listen to music on?</p>
    <select name="devices" size="3" multiple="multiple">
        <option value="ipod">iPod</option>
        <option value="radio">Radio</option>
        <option value="computer">Computer</option>
    </select>
</form>
```

## 5.按钮和隐藏控件

`<button>`元素显示为按钮样式

```html
<form action="http://www.example.com/add.php">
    <button> <img src="images/1.jpg" alt="add" width="10" heigth="10"/>add</button>
    <input type="hidden" name="bookmark" value="lyrics"/>
</form>
```

## 6.标签表单控件

使用`<label>`元素表达简单的文本，有两种使用方式：  

1. 将文本说明和表单输入框包围  
2. 与表单控件分开，使用**for**特性来指明`<label>`的**id**进行关联

```html
<label>Age:<input type="text" name="age"/></label>
<br />
<input id="female" type="radio" name="gender" value="f"/>
<label for="female">Female</label>
<input id="male" type="radio" name="gender" value="m"/>
<label for="male">Male</label>
```

## 7.组合表单元素

可将相关表单置于`<fieldset>`元素中，分成一组。`<legend>`元素跟在`<fieldset>`的后边描述控件组的用途。

```html
<fieldset>
    <legend>Contact detail</legend>
    <label>Email:<br />
        <input type="text" name="emial"/>
    </label>
    <br />
    <label>Mobile:<br />
        <input type="text" name="mobile"/>
    </label>
    <br />
    <label>Telephone:<br />
        <input type="text" name="telephone"/>
    </label>
</fieldset>
```

## 8.HTML5:表单验证

**required**特性能够验证表单信息

```html
<input type="text" name="username" required="required"/>
```

## 9.HTML5:日期控件

`<input>`元素`type`特性为**date**时可创建日期的输入控件

```html
<input type="date" name="depart"/>
```

## 10.小结

>* 表单内容位于`<form>`元素中
>* 表单中的内容以**名称/值**的形式发送
>* HTML5引入新的表单元素，使访问者可以轻松填写表单
