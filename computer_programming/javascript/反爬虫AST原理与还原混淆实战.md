*《反爬虫AST原理与还原混淆实战》*
=============================

## 1 搭建开发环境

- [node](https://github.com/nodesource/distributions/blob/master/README.md)
- npm install
    - $ npm --registry https://registry.npmmirror.com install @babel/parser @babel/traverse @babel/types @babel/generator
- [astexplorer](https://astexplorer.net/)

## 2 Web网站的调试与抓包分析

### 2.1 Chrome开发者工具

#### 2.1.1 Elements面板

- H键可以隐藏节点展示的内容但点击仍然有效
- `CTRL + F`可以通过XPATH定位
- breakpoint

#### 2.1.2 Console面板

- $0 $1 $ $$
- document.querySelector / document.querySelectorAll
- monitorEvents

#### 2.1.3 Sources面板

- 各种类型的breakpoints

#### 2.1.4 Network面板

#### 2.1.5 Application面板

### 2.2 JS逆向调试技巧

- 搜索 `CMD + OPTION + F`
- XHR
- console插桩
- 断点时输出函数跳转代码行
- 复制console输出
    - copy()
    - JSON.stringify()
    - Object.toString()
    - CryptoJS.enc.Utf8.stringfy()

### 2.3 本地覆盖

### 2.4 [Ajax-hook](https://github.com/wendux/Ajax-hook)

### 2.5 网易易盾滑块验证码调试分析

- 调用栈 + breakpoints

## 3 爬虫与反爬虫

## 4 常规反爬虫技术

## 5 混淆JS手动逆向方法

### 5.1 混淆脚本分析

#### 5.1.1 定位加密入口

- 调用栈

#### 5.1.2 混淆特征分析

- 打乱数组 push & shift
- 反编码base64 atob
- 控制流平坦化 switch case
- 位数不固定的结果可能只利用其中一位作为比对使用
- 手动还原混淆是真的很累
