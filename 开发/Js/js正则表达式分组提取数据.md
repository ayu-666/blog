# js正则表达式分组提取数据

> 日期: 2022/03/17

本文介绍如何使用js来提取文章中*特征相同*的一组信息

## js正则表达式

js正则表达式中
- `()`：子表达式，代表将一类规则围成一组
- `\w`：匹配英文字母
- `\d`：匹配阿拉伯数字
- `\w+`：字母出现1次或多次
- `\w*`：字母出现0次或多次
- `\w{1}`：字母出现1次
- `\w{1,9}`:字母出现1到9次
- `/正则表达式/i`：不区分大小写
- `/正则表达式/g`：全局匹配
- 需要转义的保留字符有：`|` `\` `/` `?` `!` `.` `+` `*` `^` `$` `(` `)` `[` `]` `{` `}`

## js exec方法

`正则表达式对象.exec("字符串")` 匹配失败返回null，匹配成功则返回一个数组

返回数组元素排序如下：
```
[
    0:"与正则表达式完全匹配的字符串",
    1:"与第1个子表达式匹配的字符串",以此类推...
]
```

> 扩充：
> 
> exec完成一次匹配后， 它将自动把正则表达式对象的lastIndex移动到已匹配字符串的最后一个字符的下一个位置，以便于匹配下一段文本
>
> 当exec找不到匹配的文本时，它将把自动把正则表达式对象的lastIndex重置为0，并返回null给调用者

### 示例：提取被abc包裹的数字**
```js
// 创建正则表达式对象
let r = /abc(\d+)abc/gi
// 循环执行匹配
while ((temp = r.exec("abc123456abc; abc654321abc;")) != null) {
    console.log(temp[1])
}
/*
  输出：
  123456
  654321
*/
```

### 示例：提取markdown中所有图片地址

- 格式1：`![xxx](/path/filename.jpg)`
- 格式2：`![xxx](http://xxx.com/path/filename.jpg)`
- 格式3：
  ```markdown
    ![xx](base64Img1)
    [base64Img1]:data:image/png;base64,iVBORw0KGgoA...
  ```
- 格式4：
  ```markdown
    ![xx](data:image/png;base64,iVBORw0KGgoA...)
  ```

markdown图片链接地址提取规律：
- 包围在括号 `(` `)`中；正则表达式为`\(      \)`
- 链接可能出现 `/ : \ . 字母 数字`；正则表达式为`[\/:\\\.\w\d]+`
- 链接以 `.png .jpg .gif`结尾；正则表达式为`\.(png|jpg|gif)`

因为我们要将链接单独分为一组，所以需要将链接的匹配规则用括号包裹成子表达式

联合起来就是：`\(     ( [\/:\\\.\w\d]+  \.(png|jpg|gif) )     \)`

```js
let str = `
图片：
![xx](/abc/img/a.png)
![xxx](http://a.com/sdk/img/d.jpg)
`
let r = /\(([\/:\\\.\w\d]+\.(png|jpg|gif))\)/gi
let resultArr = []
while((temp = r.exec(str)) != null) { //最后返回null结束
    resultArr.push(temp[1])
}
console.log(resultArr)
```
