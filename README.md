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



 
















