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

##### 函数形式的自定义Action

|参数|类型|描述|
|---|---|---|
|answers|Object|generator prompts的答案|
|config|[ActionConfig](ActionConfig)|generator action的配置对象|
|plop|[PlopfileApi](PlopfileApi)|用于action所在的plop文件的plop api|

```js
module.exports = function (plop) {

	plop.setActionType('doTheThing', function (answers, config, plop) {
		// do something
		doSomething(config.configProp);
		// if something went wrong
		throw 'error message';
		// otherwise
		return 'success status message';
	});

	// or do async things inside of an action
	plop.setActionType('doTheAsyncThing', function (answers, config, plop) {
		// do something
		return new Promise((resolve, reject) => {
			if (success) {
				resolve('success status message');
			} else {
				reject('error message');
			}
		});
	});

	// use the custom action
	plop.setGenerator('test', {
		prompts: [],
		actions: [{
			type: 'doTheThing',
			configProp: 'available from the config param'
		}, {
			type: 'doTheAsyncThing',
			speed: 'slow'
		}]
	});
};
```

#### setPrompt

[Inquirer](Inquirer) 提供了许多可开箱即用的 prompts，但它仍然允许开发者自行开发 prompt 插件。如果你想要使用一个 prompt 插件，你可以使用 `setPrompt` 去注册它。访问 [Inquirer documentation for registering prompts](https://github.com/SBoudrias/Inquirer.js#inquirerregisterpromptname-prompt) 查看详情, 你也可以在 [plop community driven list of custom prompts](https://github.com/plopjs/plop/blob/master/inquirer-prompts.md) 查看到相关的内容。

```js
const promptDirectory = require('inquirer-directory');
module.exports = function (plop) {
	plop.setPrompt('directory', promptDirectory);
	plop.setGenerator('test', {
		prompts: [{
			type: 'directory',
			...
		}]
	});
};
```

#### setGenerator

config 对象需要包含 `prompts` 和 `actions` (`description`可选)。 prompts 数组会被传递给 [inquirer](https://github.com/SBoudrias/Inquirer.js/#objects). `actions` 数组是一个待执行的 actions 列表。

##### `GeneratorConfig` 接口

|  属性  |  数据类型  |  默认值  |  描述  |
|---|---|---|---|
|  description  |  [String]  |    |  给 generator 提供一个简短的描述  |
|  prompts  |   Array[[InquirerQuestion](https://github.com/SBoudrias/Inquirer.js/#question)]  |    |  询问用户的问题列表  |
|  actions |  Array[[ActionConfig](https://github.com/plopjs/plop#interface-actionconfig)]  |    |  用于执行的 action 数组  |

> 如果你的 actions 数组是动态变化的，请查看 [使用动态变化的actions列表](https://github.com/plopjs/plop#using-a-dynamic-actions-array)。

##### `ActionConfig` 接口

以下的属性列表是 plop 内部处理的标准属性。其他属性需要根据 action 类型动态传入。查看 [built-in actions](https://github.com/plopjs/plop#built-in-actions) 了解详情。

|  属性  |  数据类型  |  默认值  |   描述   |
|   ---  |   ---   |   ---   |   ---   |
|  type  |   String   |     |   action 的类型(包括 [add](https://github.com/plopjs/plop#add), [modify](https://github.com/plopjs/plop#modify), [addMany](https://github.com/plopjs/plop#addmany) |
|  force  |   Boolean  |    `false`     |  [强制执行](https://github.com/plopjs/plop#running-a-generator-forcefully)(不同的 action 类型会对应不同的逻辑)  |
|  data  |  Object/Function  |  `{}`  |  指定 action 被执行时需要混入prompts答案的数据  |
|  abortOnFail  |  Boolean  |  `true`  |  当一个 action 由于某种原因执行失败时停止执行之后的所有 action  |
|  skip  |  Function  |    |  一个用于指定当前 action是否需要被执行的函数  |

> 任何 `ActionConfig` 上的 `data` 属性都可以是一个函数，这个函数返回一个对象或者函数，这个被返回的函数应该返回一个 Promise，这个 Promise 应该 resolve 一个对象。

> 任何 `ActionConfig` 上的 `skip` 函数都是可选的，如果这个 action 需要被跳过，那么 skip 函数需要返回一个字符串，这个字符串就是这个 action 需要被跳过的原因。

> Action 除了可以是一个对象之外，也可以是[一个函数](https://github.com/plopjs/plop#custom-action-function)。

### 其他方法

| 方法 | 参数 | 返回值 | 描述 |
| ---  |   ---  |   ---   |---|
| getHelper | String | Function | 获取 helper 工具函数 |
| getHelperList |  |  Array[String]  | 获取 helper 工具函数的名称列表 |
| getPartial | String | String | 通过名称获取一个 handlebars partial |
| getPartialList |  | Array[String] | 获取 partial 名称列表 |
| getActionType | String | [CustomAction](https://github.com/plopjs/plop#functionsignature-custom-action) | 通过名称获取一个 action 类型 |
| getActionTypeList | Array[String] |  | 获取 actionType 的名称列表 |
| setWelcomeMessage | String |    |  自定义运行 plop 时询问选择generator的欢迎语  |
| getGenerator | String | [GeneratorConfig](https://github.com/plopjs/plop#interface-generatorconfig) | 通过名称获取[GeneratorConfig](https://github.com/plopjs/plop#interface-generatorconfig) |
| getGeneratorList |  | Array[Object] | 获取一个包含 genertor 名称和描述信息的对象数组 |
| setPlopfilePath | String |  | 设置用于plop内部定位资源文件(如模版文件)的 `plopfilePath` 值 |
| getPlopfilePath | String |  | 返回正在使用的 plopfile 的绝对路径 |
| getDestBasePath |  | String | 返回用于创建文件的base路径 |
| setDefaultInclude | Object | Object | 用于其他 plopfile 通过 `plop.load()` 方法调用当前 plopfile 时设置当前 plopfile 的默认配置 |
| getDefaultInclude | String | Object | 获取其他 plopfile 通过 `plop.load()` 方法调用当前 plopfile 时使用的当前 plopfile 的默认配置 |
| renderString | String, Object | String | 使用第二个参数作为data, 通过handlebars模版生成器执行第一个参数，返回渲渲染结果字符串模版 |

### `Built-In Actions`

在 [GeneratorConfig](https://github.com/plopjs/plop#interface-generatorconfig) 中，有几种可供使用的 built-in action。你可以执行 action 类型和 action 使用的模版(所有的路径都基于 plopfile 的路径)。

> `Add`, `AddMany`, `Modify` action 有一个可选的 `transform` 方法，这个方法可以用于在写入到磁盘之前对模版结果做一些转换操作。`transform` 函数接收模版结果或文件内容(字符串形式) 和 action data 作为参数，它必须返回一个字符串或一个 resolve 字符串的 Promise。

#### Add

你已经猜到了，`add` action 可以用于往你的项目中新增一个文件。path 属性是一个 handlebars 模版, 它被用于根据名称创建一个文件。文件的内容取决于 `template` 或 `templateFile` 属性。

| 属性 | 数据类型 | 默认值 | 说明 |
| --- | --- | --- | --- |
| path | String |  | 一个 handlebars 模版，它是需要被创建的文件路径 |
| template | String |  | 一个用于创建新文件的 handlebars 模版 |
| templateFile |  String  |  |  一个包含模版内容的文件路径 |
| skipIfExists | Boolean | false | 如果这个属性存在，则跳过文件 |
| transform | Function |  | 一个可选的 Function, 用于在写入文件到磁盘之前对模版结果做转换操作 |
| skip | Function |  | 继承自[ActionConfig](https://github.com/plopjs/plop#interface-actionconfig) |
| force | Function | `false` | 继承自[ActionConfig](https://github.com/plopjs/plop#interface-actionconfig) |
| data | Object | `{}` | 继承自[ActionConfig](https://github.com/plopjs/plop#interface-actionconfig) |
| abortOnFail | Boolean | `true` | 继承自[ActionConfig](https://github.com/plopjs/plop#interface-actionconfig) |

#### AddMany

通过 `AddMany` 方法，你可以在你的项目中使用单个 action 创建多个文件。`destination` 属性是一个 handlebars 模版，它被用于指定生成文件之后的存放路径。`base` 属性用于判断创建文件时模版路径的哪一部分需要被省略。如果你想要新增的文件有独特的文件名称，glob 可以在文件/文件夹名中使用 handlebars 语法(**这里有一段没有翻译出来，需要斟酌**)。

| 属性 | 数据类型 | 默认值 | 说明 |
| --- |  ---  | --- |  ---  |
| destination | String |  | 一个 handlebars 模版，用于存放新建文件的目录 |
| base | String |  | 添加新文件到 destination 时，path 中需要被移除的部分 |
| templateFiles | [Glob](https://github.com/sindresorhus/globby#globbing-patterns) | 添加多个文件的 glob 表达式 |
| stripExtensions | [String] | ['hbs'] | 当 `templateFiles` 文件被添加到 `destination` 时文件名中需要去除的扩展名 |
| globOptions | [Object](https://github.com/sindresorhus/globby#options) |  | 改变匹配模版文件添加方式的 glob 选项 |
| verbose | Boolean | `true` | 是否打印所有新建的文件path |
| transform | Function |  | 模版结果被写入磁盘之前的[一个可选的转换函数](https://github.com/plopjs/plop#built-in-actions) |
| skip | Function |  | 继承自[ActionConfig](https://github.com/plopjs/plop#interface-actionconfig) |
| skipIfExists | Boolean | `false` | 继承自[Add](https://github.com/plopjs/plop#add)(如果文件已存在则跳过) |
| force | Boolean | `false` | 继承自[ActionConfig](https://github.com/plopjs/plop#interface-actionconfig)(如果文件存在则会覆盖原有的文件) |
| data | Object | `{}` | 继承自[ActionConfig](https://github.com/plopjs/plop#interface-actionconfig) |
| abortOnFail | Boolean | `false` | 继承自[ActionConfig](https://github.com/plopjs/plop#interface-actionconfig) |

#### Modify

`modify` action 有两种使用方式。你可以使用 `pattern` 属性来查找/替换 `path` 字段指明的文件中的文本，也可以使用 `transform` 方法来转换文件内容。`pattern` 和 `transform` 可以同时使用(`transform`方法会在最后执行)。更多关于 `modify` 方法的使用和细节可以查看 [example文件夹](https://github.com/plopjs/plop/tree/master/example)。

| 属性 | 数据类型 | 默认值 | 说明 |
| --- |  ---  | --- |  ---  |
| path | String |  | handlebars 模版，需要被修改的文件路径 |
| pattern | RegExp | end‑of‑file | 用于匹配需要替换的文本的正则表达式 |
| template | String |  | pattern 匹配到的需要替换的handlebars 模版，capture groups are available as $1, $2, etc(缺失)。 |
| templateFile | String |  | 一个包含了 `template` 的文件路径 |
| transform | Function |  | 模版结果被写入磁盘之前的[一个可选的转换函数](https://github.com/plopjs/plop#built-in-actions) |
| skip | Function |  | 继承自[ActionConfig](https://github.com/plopjs/plop#interface-actionconfig) |
| data | Object | `{}` | 继承自[ActionConfig](https://github.com/plopjs/plop#interface-actionconfig) |
| abortOnFail | Boolean | `false` | 继承自[ActionConfig](https://github.com/plopjs/plop#interface-actionconfig) |

#### Append

`append` action



















































