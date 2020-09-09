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
