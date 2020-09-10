# plop中文文档

一个微型的生成器框架，用于生成符合团队规范的模版文件。

## 开始

### 什么是 PLOP?

我习惯称它为 “微型生成器框架。 这是因为它提供了一种快速生成代码或其他文本文件的便捷方式，而又有着很小的体积。我们总是在代码中创建不同的结构和模式，如路由、控制器、组件、工具类等。这些模式会随着时间的变化不断地被改变和优化，所以当你想要去创建一个新的已经存在的模版时，你很难在你的项目/代码中找到对于当前需要创建的模板的最佳实践，这就是 plop 大展身手的舞台了。使用 plop, 你能够随时在项目中更新某种特定代码模式的最佳实践，你只需要在命令行中键入 plop 就能快速地运行代码。这不仅能避免从整个代码库中寻找最佳模板然后复制的过程，而且又能正确高效地创建文件。

如果你看过 plop 的源代码，它其实就是基于 [inquirer](https://github.com/SBoudrias/Inquirer.js/)对话框 和 [hanldebar](https://github.com/handlebars-lang/handlebars.js)模版 的简单融合。

> 此文档还在完善中。如果你有好的想法，我将非常乐于倾听。

### 安装

#### 1. 局部安装

```shell
$ npm install --save-dev plop
```

#### 2. 全局安装(可选，但为了避免权限问题引起的错误，推荐安装)

```
$ npm install -g plop
```

#### 3. 在项目根文件夹创建 plopfile.js

```js
module.exports = function (plop) {
	// create your generators here
	plop.setGenerator('basics', {
		description: 'this is a skeleton plopfile',
		prompts: [], // array of inquirer prompts
		actions: []  // array of actions
	});
};
```

### 你的第一个 plop 文件

一个 plop 文件就是一个简单的 node 模块，它导出一个函数，这个函数接收一个 plop 对象作为第一个参数。

```js
module.exports = function (plop) {};
```

一个 plop 对象暴露一个包含 `setGenerator(name, config)` 函数的 plop api。`setGenerator` 用于创建一个生成器(`generator`)。当 plop 在当前文件夹或子文件夹的终端中运行时，这些 `generator` 将会以列表的形式被展示在终端中。

让我们看看一个简单的 generator 长什么样子吧：

```js
module.exports = function (plop) {
	// controller generator
	plop.setGenerator('controller', {
		description: 'application controller logic',
		prompts: [{
			type: 'input',
			name: 'name',
			message: 'controller name please'
		}],
		actions: [{
			type: 'add',
			path: 'src/{{name}}.js',
			templateFile: 'plop-templates/controller.hbs'
		}]
	});
};
```

按照如上代码，我们创建了一个名为 `controller` 的 `generator`，它会在被执行时发出一个询问，并创建一个文件。你可以尽情地扩展它，让它发出任何数量的询问，创建任何数量的文件。此外，我们还可以使用其他的 actions 来更改我们的代码库。

### 使用 Prompts

Plop 使用 `Inquirer` 这个库来捕获用户输入，你可以在 inquirer 官网上找到所有的 prompts 类型。

#### CLI 用法

只要你安装了 Plop 并且创建了一个 generator, 你就可以在终端中运行 plop 了。 当你单独运行 plop 命令时，你将会看到一个可供选择的 generator 列表，你也可以直接运行 `plop [generatorName]` 直接触发 generator。如果你没有全局安装 plop，那么你需要创建一个 npm script 来帮助你运行 plop。

```json
// package.json
{
    ...,
    "scripts": {
        "plop": "plop"
    },
    ...
}
```

#### 绕过 Prompts

当你深入地了解一个项目(和它的 generators)时，你可能想要为 prompts 提供一些预置的答案。例如，如果我有一个 generator 用于生成组件，它有一个 prompt, 我可以通过 `plop component "some component name"` 运行 generator, 它会立马执行，就好像我在 prompt 中键入了 "some component name"。如果这个 generator 有第二个 prompt, 同样，第二个被键入的值就会被填入第二个 prompt 中。

所有类似于 `confirm` 或 `list` 等的 prompts 应该都是语义化的。例如所有用于确认的 prmopt 中，输入 "Y", "yes", "t" 或者 "true" 的结果都应该是 true。你可以通过他们的 value, index, key, name 来从列表中选择 item, Checkbox 类型的 prompt 可以通过接收以符号分隔的多个值以支持多行选择。

> 如果你想要提供可绕过的第二个输入而不是第一个，你可以使用下划线 `_` 用于跳过不想绕过的项（例如 `plop component _ "input for second prompt"`）。

Plop 为标准的 inquirer prompts 内置了绕过逻辑，但是你也可以通过其他方法为特殊的 prompt 提供特殊的自定义逻辑来处理用户输入。

如果你曾经发布过第三方的 inquirer prompt 插件, 而且愿意为 plop 用户提供可开箱即用的绕过功能，你可以在这里看到相关的内容。

#### 绕过 Prompts（通过名称的方式）

你也可以通过名称来绕过 prompt, 方式是使用 `--`, 然后为每一个你想要绕过的 prompt 提供参数。以下是示例：

##### 绕过的示例

```shell
## Bypassing both prompt 1 and 2
$ plop component "my component" react
$ plop component -- --name "my component" --type react

## Bypassing only prompt 2 (will be prompted for name)
$ plop component _ react
$ plop component -- --type react
```

#### 强制运行 generator

在默认状态下，当发生某些异常时，Plop actions会通过触发失败的方式来保证你的文件安全，最典型的示例是禁止 `add` action 去覆盖一个已经存在的文件。Plop actions individually(这里暂时不知道怎么翻译) 支持 `force` 属性，你也可以在终端中使用 `--force` 标识。使用 `--force` 标识代表你想要强制执行所有的 action。

### 为什么要使用 Generator?

因为当你想要从你的代码独立创建你的模版文件时，你往往会付出很多不必要的时间和精力。

因为它平均可以帮你的团队（或你自己）从创建每一个route, component, controller, helper, test, view等等文件时省下5-15分钟。

因为上下文切换是昂贵的，而且节省时间远不是自动化工作流的唯一优点。

## Plopfile Api

Plopfile api 是 `plop` 对象下暴露出的一系列方法。通常来说, `setGenerator` 方法可以帮你完成大部分的工作，但这一部分的文档帮你列出了其他可能帮到你的方法。

### TypeScript 声明

plop 内置了 TypeScript 声明。不管你是不是通过 TypeScript 书写 Plop 文件，许多编辑器都可以通过内置的 TypeScript 声明提供语法提示。

```js
// plopfile.ts
import {NodePlopAPI} from 'plop';

export default function (plop: NodePlopAPI) {
  // plop generator code
};
```

```js
// plopfile.js
module.exports = function (
	/** @type {import('plop').NodePlopAPI} */
	plop
) {
	// plop generator code
};
```

### 常用方法

下面是一些你在创建 plop 文件时会经常用到的方法。其他常用于内部的方法在 other methods 部分列出。

| 方法 | 参数 | 返回值 | 描述 |
| --- | ---  | ---  | ---  |
| setGenerator | String, GeneratorConfig | GeneratorConfig | 创建一个 generator |
| setHelper | String, Function |  | 创建 handlebars helper |
| setPartial | String, String || setup a handlebars partial |
| setActionType | String, CustomAction | | 注册一个自定义的 action 类型 |
| setPrompt | String, InquirerPrompt || 使用 inquirer 注册一个自定义的 prompt 类型 ||
| load | Array[String], Object, Object || 从其他的plopfile或npm模块加载 generators, helpers 或 partials |

#### setHelper

`setHelper` 等同于 handlebars 中的 `registerHelper` 方法。所以如果你很熟悉 [handlebars helpers](https://handlebarsjs.com/guide/expressions.html#helpers) 的话，你就已经知道它是如何工作的了。

```js
module.exports = function (plop) {
	plop.setHelper('upperCase', function (text) {
		return text.toUpperCase();
	});

	// or in es6/es2015
	plop.setHelper('upperCase', (txt) => txt.toUpperCase());
};
```

#### setPartial

`setPartial` 等同于 handlebars 中的 `registerPartial` 方法。所以如果你很熟悉 [handlebars partials](https://handlebarsjs.com/guide/expressions.html#partials) 的话，你就已经知道它是如何工作的了。

```js
module.exports = function (plop) {
	plop.setPartial('myTitlePartial', '<h1>{{titleCase name}}</h1>');
	// used in template as {{> myTitlePartial }}
};
```
 
#### setActionType

`setActionType` 方法允许你创建你的能在 plopfile 中使用的自定义actions(类似于 `add` 或 `modify`)。下面这些是可高度复用的自定义 action 函数。


















