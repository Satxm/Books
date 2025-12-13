---
title: Office 将编号转换为文本
published: 2025-11-30 12:19:34
description: 'Office 将编号转换为文本'
image: ''
tags: []
category: ''
draft: false 
lang: ''
---
# 将编号转换为文本

## Office VBA 宏
```vb
Sub ConvertNumbersToText()
ActiveDocument.Content.ListFormat.ConvertNumbersToText
End Sub
```

## WPS JS
```js
function ConvertNumbersToText(){
ActiveDocument.Content.ListFormat.ConvertNumbersToText()
}
```