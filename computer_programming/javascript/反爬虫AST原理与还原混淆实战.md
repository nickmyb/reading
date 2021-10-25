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

## 6 JS代码安全防护原理

### 6.1 常量的混淆原理

- object.attribute / object['attribute']
- x ^ y ^ y = x

```
// hex替换字符串
function str2Hex(str) {
    for (var ret = [], i = 0, s; i < str.length; i++) {
        s = "\\x" + str.charCodeAt(i).toString(16)
        ret += s
    }
    return ret
}

// unicode替换字符串
function str2u(str) {
    var ret = ''
    for (var i = 0; i < str.length; i++) {
        ret += "\\u" + ("0000" + parseInt(str.charCodeAt(i)).toString(16)).substr(-4)
        return ret
    }
}

// str.charCodeAt(index)
// String.fromCharCode(indexArray)
```

### 6.2 增加JS逆向者的工作量

- ""['constructor']['fromCharCode'] = String.fromCharCode
- jsfuck根据()分段处理

### 6.3 代码执行流程的防护原理

- flatten
- , 返回最后一个表达式的结果

### 6.4 其他代码防护方案

- 格式化检测
- function + '' / function.toString()
    - 只能检测替换的格式化文件,chrome formatted无法检测

## 7 AST抽象语法树的原理与实现

### 7.1 理解AST抽象语法树

### 7.2 词法分析

### 7.3 语法分析

### 7.4 Babel编译步骤

## 8 AST的API详解

### 8.1 AST入门

```
// 1. AST基本结构了解

// 2. ast处理js的基本步骤
const fs = require('fs')
const parser = require("@babel/parser")
const traverse = require("@babel/traverse").default
const t = require("@babel/types")
const generator = require("@babel/generator").default


const origin = fs.readFileSync("./origin.js", {
    encoding: "utf-8"
})
let ast = parser.parse(origin)

// 在这里对AST进行一系列的操作

// generator可以针对部分节点做debug
let processed = generator(ast).code
fs.writeFile('./processed.js', processed, (err)=>{})
```

### 8.2 Babel中的组件

- [babel-handbook](https://github.com/jamiebuilds/babel-handbook)

- parser & generator
- traverse & visitor
    - enter / exit 在父子节点处理顺序上有区别
- types

```
function f1(path) {
    console.log('f1')
}

function f2(path) {
    console.log('f2')
}

const visitor = {
	"FunctionExpression|BinaryExpression": {
        enter: [f1, f2],
        exit(path) {
            console.log("exit")
        }
    }
}
traverse(ast, visitor)


const updateParamNameVisitor = {
    Identifier(path) {
        if (path.node.name === this.paramName) {
            path.node.name = "x"
        }
    }
}
const visitor = {
    FunctionExpression(path) {
        // path.node指向当前node
        const paramName = path.node.params[0].name
        // path.traverse表示从当前path开始遍历
        // path.traverse的第二个参数对象可以在updateParamNameVisitor中用this引用
        path.traverse(updateParamNameVisitor, {
            paramName
        })
    }
}
```

```
t.isIdentifier(path.node, {name: 'n'})
<=>
path.node.type === "Identifier" && path.node.name === "n"
```

```
// types生成node 和 自己构造json对象通过generator生成node也是等效的
let obj = {};
obj.type = 'BinaryExpression';
obj.left = {type: 'NumericLiteral', value: 1000};
obj.operator = '/';
obj.right = {type: 'NumericLiteral', value: 2000};
let code = generator(obj).code;
```

```
// t.valueToNode
let loaclAst = t.valueToNode([1, "2", false, null, undefined, /\w\s/g, {x: '1000', y: 2000}]);
```

```
// types生成ast示例
const fs = require('fs');
const parser = require("@babel/parser");
const traverse = require("@babel/traverse").default;
const t = require("@babel/types");
const generator = require("@babel/generator").default;

const jscode = fs.readFileSync("./demo.js", {
        encoding: "utf-8"
    });
let ast = parser.parse(jscode);

let a = t.identifier('a');
let b = t.identifier('b');
let binExpr2 = t.binaryExpression("+", a, b);
let binExpr3 = t.binaryExpression("*", a, b);
let retSta2 = t.returnStatement(t.binaryExpression("+", binExpr2, t.numericLiteral(1000)));
let retSta3 = t.returnStatement(t.binaryExpression("+", binExpr3, t.numericLiteral(1000)));
let bloSta2 = t.blockStatement([retSta2]);
let bloSta3 = t.blockStatement([retSta3]);
let funcExpr2 = t.functionExpression(null, [a, b], bloSta2);
let funcExpr3 = t.functionExpression(null, [a, b], bloSta3);
let objProp1 = t.objectProperty(t.identifier('name'), t.stringLiteral('xiaojianbang'));
let objProp2 = t.objectProperty(t.identifier('add'), funcExpr2);
let objProp3 = t.objectProperty(t.identifier('mul'), funcExpr3);
let objExpr = t.objectExpression([objProp1, objProp2, objProp3]);
let varDec = t.variableDeclarator(t.identifier('obj'), objExpr);
let loaclAst = t.variableDeclaration('let', [varDec]);
let code = generator(loaclAst).code;
console.log(code);

let code = generator(ast).code;
fs.writeFile('./demoNew.js', code, (err) => {});
```

### 8.3 Path对象详解

- 停止遍历当前节点和子节点 path.stop / skip
- NodePath / Node
    - path.get('left.name')
    - path.left.name
    - path.get('right').isNumericLiteral({
          value: 1000
      })
- generator(path.node).code = path.toString() = path + ''
- 替换
    - 属性
    - path.node.left = t.identifier("x")
    - 节点
    - path.replaceWith(t.valueToNode('x')) / path.replaceWithMultiple([statements]) / path.replaceInline() / path.replaceWithSourceString()
- ast会对替换后的节点继续做解析
- path.remove()
- path.insertBefore() / path.insertAfter()
- path.parentPath.node =  path.parent
- path.findParent / path.find / path.getFunctionParent / path.getStatementParent
- 同级Path通过container来操作
    - container / listKey: 容器名 / key: 容器中索引
    - container不是数组的情况一般不视为container
    - path.container / path.key / path.listKey / path.getSibling(index) / path.unshiftContainer / path.pushContainer

### 8.3 scope详解

- 标识符作用域
    - path.scope.block
    - path.scope.parent.block: 函数的情况, binding.scope.block 不需要.parent
- path.scope.getBinding getOwnBinding
    - kind / constantViolations / referencePaths / references / referenced
    - getOwnBinding 对于函数及其内部的标识符的判定不是很准确, 一般只使用getBinding
    - binding && generator(binding.scope.block).code == path + ''
- constantViolations: 变量修改 referencePaths: 变量引用
- binding.scope.traverse: 一般使用binding.scope.traverse替代path.scope.traverse
- scope.rename & scope.generateUidIdentifier
