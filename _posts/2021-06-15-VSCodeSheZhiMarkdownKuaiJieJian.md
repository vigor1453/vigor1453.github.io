---
layout: post
title: "VSCode设置Markdown快捷键"
date: 2021-06-15
tags: ["markdown"]
comments: true
author: oneman233
excerpt: "更方便的snippet"
---

Markdown书写公式时是需要使用美元符号的，而这个符号只能在英文输入法下才能输入，所以之前用VSCode写Markdown的时候一直苦于输入法的来回切换，直到我发现了VSCode可以自定义输入补全

主要参考了[这篇文章](https://bbs.huaweicloud.com/blogs/208290)，我的补全配置如下：

```json
{
	"LATEX": {
		"prefix": "latex",
		"body": [
			"$$1$"
		],
		"description": "latex in one row"
	},
	"LATEX_BLOCK": {
		"prefix": "latex_block",
		"body": [
			"$$",
			"$1",
			"$$"
		],
		"description": "latex block"
	},
	"XOR": {
		"prefix": "xor",
		"body": [
			"^"
		],
		"description": "xor"
	}
}
```

除了行内公式和公式块之外，我还定义了 `xor` 符号，它也只能在英文输入法下才能输入

补全的配置可以参考 `json` 文件中的注释，在你输入 `prefix` 里的内容后VSCode就会自动帮你补全

但是配置好后补全没有生效，查询发现了[这个解决方案](https://github.com/Microsoft/vscode/issues/28048)

你需要设置 `"editor.quickSuggestions"` 属性为 `true` 才能使补全生效，VSCode默认该属性为 `false`