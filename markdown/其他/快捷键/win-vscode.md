## 导航和编辑
### 快速打开( Ctrl+P )
- 输入`:` 行数 回车快速跳转
- 输入`@` 查找字符回车跳转
- Ctrl+T只是`#`命令的快捷方式(这个应该是搜索类的快捷键)
- Alt+F12 查看符号的定义
- 转到定义功能 F12
- 智能选择
  - 要扩展选择，请使用Shift+Alt+Right。
  - 要缩小选区，请使用Shift+Alt+Left。


- 修改快捷键
  - 命令面板 ctrl+ shift+ p ,搜索快捷键然后可以修改快捷键 
- 按 tab 键可以调到下一个占位符
- 多项选择（多光标）

  - Alt+Click  macOS 上为Cmd +Click
  - Ctrl+Alt+Down或Ctrl+Alt+Up在下方或上方插入光标。
- 搜索

  - Ctrl+F 当前文本搜索
  - Ctrl+Shift+F跨文件搜索
- 打开终端

  - 使用带有反引号字符的Ctrl+`键盘快捷键。
  - Ctrl+Shift+C键盘快捷键打开外部终端


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


## snippets

 https://code.visualstudio.com/docs/editor/userdefinedsnippet



**参考文档**

https://blog.csdn.net/maokelong95/article/details/54379046/

变量

使用`$name`或`${name:default}`，您可以插入变量的值。当未设置变量时，将插入其**默认值**或空字符串。当变量未知（即未定义其名称）时，将插入该变量的名称并将其转换为占位符。

可以使用以下变量：

- `TM_SELECTED_TEXT` 当前选中的文本或空字符串
- `TM_CURRENT_LINE` 当前行的内容(两个这个 后面的会把前面的这个变量值在拼接一次)
- `TM_CURRENT_WORD` 光标下单词的内容或空字符串 (就是触发的前缀)
- `TM_LINE_INDEX` 基于零索引的行号
- `TM_LINE_NUMBER` 基于一个索引的行号
- `TM_FILENAME` 当前文档的文件名
- `TM_FILENAME_BASE` 当前文档的文件名，不带扩展名
- `TM_DIRECTORY` 当前文档的目录
- `TM_FILEPATH` 当前文档的完整文件路径
- `RELATIVE_FILEPATH` 当前文档的相对（相对于打开的工作区或文件夹）文件路径
- `CLIPBOARD` 剪贴板的内容
- `WORKSPACE_NAME` 打开的工作区或文件夹的名称
- `WORKSPACE_FOLDER` 打开的工作区或文件夹的路径

## 给代码片段配置快捷键

文件-首选项->代码片段

![1637225306386](../../../pic/markdown/1637225306386.png)

```

	"newobj": {
		"prefix": "newobj",
		"body": [
			"${1} ${2} = new ${1}();",
			"${0}"
		]
	  }
```



文件->首选项->代码快捷方式->右键->添加键绑定

![1637225456110](../../../pic/markdown/1637225456110.png)





![1637225368650](../../../pic/markdown/1637225368650.png)

```
,
    {
        "key": "ctrl+shift+oem_7",
        "command": "editor.action.insertSnippet",
        "when": "editorTextFocus",
        "args": {
          "langId": "java",
          "name": "newobj"
        }
      }
```

