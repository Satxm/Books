---
title: Office 将编号转换为文本
published: 2025-11-30 12:19:34
description: 'Office 将编号转换为文本'
image: api
tags: [Word,Vba]
category: 'Word'
draft: false 
lang: ''
---

在日常办公中，我们经常需要将自动编号（如 1. 2. 3.）转换为纯文本格式，以便于后续的排版或格式调整。以下提供针对 Microsoft Word 和 WPS Office 的两种解决方案。


## Microsoft Word 解决方案 (VBA 宏)

```vb
Sub ConvertNumbersToText()
ActiveDocument.Content.ListFormat.ConvertNumbersToText
End Sub
```

## WPS Office 解决方案 (JS 宏)
```js
function ConvertNumbersToText(){
ActiveDocument.Content.ListFormat.ConvertNumbersToText()
}
```
> [!TIP] 提示
> 转换后，编号将不再随内容增减自动更新，变为普通的文本字符。