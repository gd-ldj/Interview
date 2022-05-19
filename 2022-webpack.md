WebPack就是一个模块打包工具
- 其他功能，开启node服务、代码压缩，优化，转义

4、webpack的核心概念

Entry：入口，Webpack 执行构建的第一步将从 Entry 开始，可抽象成输入。告诉webpack要使用哪个模块作为构建项目的起点，默认为./src/index.js
output ：出口，告诉webpack在哪里输出它打包好的代码以及如何命名，默认为./dist
Module：模块，在 Webpack 里一切皆模块，一个模块对应着一个文件。Webpack 会从配置的 Entry 开始递归找出所有依赖的模块。
Chunk：代码块，一个 Chunk 由多个模块组合而成，用于代码合并与分割。
Loader：模块转换器，用于把模块原内容按照需求转换成新内容。
Plugin：扩展插件，在 Webpack 构建流程中的特定时机会广播出对应的事件，插件可以监听这些事件的发生，在特定时机做对应的事情。

5、Webpack的基本功能有哪些？

代码转换：TypeScript 编译成 JavaScript、SCSS 编译成 CSS 等等
文件优化：压缩 JavaScript、CSS、html 代码，压缩合并图片等
代码分割：提取多个页面的公共代码、提取首屏不需要执行部分的代码让其异步加载
模块合并：在采用模块化的项目有很多模块和文件，需要构建功能把模块分类合并成一个文件
自动刷新：监听本地源代码的变化，自动构建，刷新浏览器
代码校验：在代码被提交到仓库前需要检测代码是否符合规范，以及单元测试是否通过
自动发布：更新完代码后，自动构建出线上发布代码并传输给发布系统。

webpack会自动地递归解析入口所需要加载的所有资源文件，然后用不同的Loader来处理不同的文件，用Plugin来扩展webpack功能。

NPM模块需要注意以下问题：

要支持CommonJS模块化规范，所以要求打包后的最后结果也遵守该规则。
Npm模块使用者的环境是不确定的，很有可能并不支持ES6，所以打包的最后结果应该是采用ES5编写的。并且如果ES5是经过转换的，请最好连同SourceMap一同上传。
Npm包大小应该是尽量小（有些仓库会限制包大小）
发布的模块不能将依赖的模块也一同打包，应该让用户选择性的去自行安装。这样可以避免模块应用者再次打包时出现底层模块被重复打包的情况。
UI组件类的模块应该将依赖的其它资源文件，例如.css文件也需要包含在发布的模块里。

11、webpack的构建流程是什么?从读取配置到输出文件这个过程尽量说全。

Webpack 的运行流程是一个串行的过程，从启动到结束会依次执行以下流程：

初始化参数：从配置文件和 Shell 语句中读取与合并参数，得出最终的参数；
开始编译：用上一步得到的参数初始化 Compiler 对象，加载所有配置的插件，执行对象的 run 方法开始执行编译；
确定入口：根据配置中的 entry 找出所有的入口文件；
编译模块：从入口文件出发，调用所有配置的 Loader 对模块进行翻译，再找出该模块依赖的模块，再递归本步骤直到所有入口依赖的文件都经过了本步骤的处理；
完成模块编译：在经过第4步使用 Loader 翻译完所有模块后，得到了每个模块被翻译后的最终内容以及它们之间的依赖关系；
输出资源：根据入口和模块之间的依赖关系，组装成一个个包含多个模块的 Chunk，再把每个 Chunk 转换成一个单独的文件加入到输出列表，这步是可以修改输出内容的最后机会；
输出完成：在确定好输出内容后，根据配置确定输出的路径和文件名，把文件内容写入到文件系统。
在以上过程中，Webpack 会在特定的时间点广播出特定的事件，插件在监听到感兴趣的事件后会执行特定的逻辑，并且插件可以调用 Webpack 提供的 API 改变 Webpack 的运行结果

12、怎么配置单页应用？怎么配置多页应用？

单页应用可以理解为webpack的标准模式，直接在entry中指定单页应用的入口即可，这里不再赘述

多页应用的话，可以使用webpack的 AutoWebPlugin来完成简单自动化的构建，但是前提是项目的目录结构必须遵守他预设的规范。多页应用中要注意的是：

每个页面都有公共的代码，可以将这些代码抽离出来，避免重复的加载。比如，每个页面都引用了同一套css样式表
随着业务的不断扩展，页面可能会不断的追加，所以一定要让入口的配置足够灵活，避免每次添加新页面还需要修改构建配置

13、Loader机制的作用是什么？

webpack默认只能打包js文件，配置里的module.rules数组配置了一组规则，告诉 Webpack 在遇到哪些文件时使用哪些 Loader 去加载和转换打包成js。

注意：

use属性的值需要是一个由 Loader 名称组成的数组，Loader 的执行顺序是由后到前的；每一个 Loader 都可以通过 URL querystring 的方式传入参数，例如css-loader?minimize中的minimize告诉css-loader要开启 CSS 压缩。

14、常用loader

css-loader读取 合并CSS 文件style-loader把 CSS 内容注入到 JavaScript 里sass-loader 解析sass文件（安装sass-loader，node-sass）postcss-loader自动添加浏览器兼容前缀（postcss.config配置）url-loader将文件转换为base64 URI。vue-loader处理vue文件。

15、Plugin（插件）的作用是什么？

Plugin 是用来扩展 Webpack 功能的，通过在构建流程里注入钩子实现，它给 Webpack 带来了很大的灵活性。

Webpack 是通过plugins属性来配置需要使用的插件列表的。plugins属性是一个数组，里面的每一项都是插件的一个实例，在实例化一个组件时可以通过构造函数传入这个组件支持的配置属性。

19、sourceMap

是一个映射关系，将打包后的文件隐射到源代码，用于定位报错位置

配置方式:

例如：devtool：‘source-map’加不同前缀意义：

inline:不生成映射关系文件，打包进main.js
cheap: 1.只精确到行，不精确到列，打包速度快 2.只管业务代码，不管第三方模块
module：不仅管业务代码，而且管第三方代码
eval:执行效率最快，性能最好
最佳实践：开发环境：cheap-module-eval-source-map线上环境：cheap-mudole-source-map

20、HMR热模块更新

借助webpack.HotModuleReplacementPlugin()，devServer开启hot

1.polyfill 以及 runtime 区别

babel-polyfill 的原理是当运行环境中并没有实现的一些方法，babel-polyfill会做兼容。

babel-runtime 它是将es6编译成es5去执行。我们使用es6的语法来编写，最终会通过babel-runtime编译成es5.也就是说，不管浏览器是否支持ES6，只要是ES6的语法，它都会进行转码成ES5.所以就有很多冗余的代码。

babel-polyfill 它是通过向全局对象和内置对象的prototype上添加方法来实现的。比如运行环境中不支持Array.prototype.find 方法，引入polyfill, 我们就可以使用es6方法来编写了，但是缺点就是会造成全局空间污染。

babel-runtime: 它不会污染全局对象和内置对象的原型，比如说我们需要Promise，我们只需要import Promise from 'babel-runtime/core-js/promise'即可，这样不仅避免污染全局对象，而且可以减少不必要的代码。


23、什么是模热更新？有什么优点？

模块热更新是webpack的一个功能，它可以使得代码修改之后，不用刷新浏览器就可以更新。

在应用过程中替换添加删出模块，无需重新加载整个页面，是高级版的自动刷新浏览器。

优点：只更新变更内容，以节省宝贵的开发时间。调整样式更加快速，几乎相当于在浏览器中更改样式

24、lazy loading（模块懒加载）

借助import()语法异步引入组件，实现文件懒加载：prefetch,preloadingwebpack提倡多写异步代码，提升代码利用率，从而提升页面性能先加载主业务文件，prefetch利用网络空闲时间，异步加载组件

import(/* webpackPrefetch: true / ‘LoginModal’);

preload和主业务文件一起加载，异步加载组件

import(/ webpackPreload: true */ ‘ChartingLibrary’);

25、什么是长缓存？在webpack中如何做到长缓存优化？

浏览器在用户访问页面的时候，为了加快加载速度，会对用户访问的静态资源进行存储，但是每一次代码升级或者更新，都需要浏览器去下载新的代码，最方便和最简单的更新方式就是引入新的文件名称。

在webpack中，可以在output给出输出的文件制定chunkhash，并且分离经常更新的代码和框架代码，通过NameModulesPlugin或者HashedModulesPlugin使再次打包文件名不变。


二、说说webpack的构建流程
webpack 的运行流程是一个串行的过程，它的工作流程就是将各个插件串联起来。在运行过程中会广播事件，插件只需要监听它所关心的事件，就能加入到这条webpack机制中，去改变Webpack的运作。

从启动到结束会依次经历三大流程： - 初始化阶段：从配置文件和 Shell 语句中读取与合并参数，并初始化需要使用的插件和配置插件等执行环境所需要的参数。 - 编译构建阶段：从 Entry 发出，针对每个 Module 串行调用对应的 Loader 去翻译文件内容，再找到该 Module 依赖的 Module，递归地进行编译处理。 - 输出阶段：对编译后的 Module 组合成 Chunk，把 Chunk 转换成文件，输出到文件系统。

五、Loader和Plugin的区别，以及如何自定义Loader和Plugin
5.1 区别
Loader本质就是一个函数，在该函数中对接收到的内容进行转换，返回转换后的结果。因为 Webpack 只认识 JavaScript，所以 Loader 就成了翻译官，对其他类型的资源进行转译的预处理工作。

Plugin就是插件，基于事件流框架Tapable，插件可以扩展 Webpack 的功能，在 Webpack 运行的生命周期中会广播出许多事件，Plugin 可以监听这些事件，在合适的时机通过 Webpack 提供的 API 改变输出结果。

Loader 运行在打包文件之前，Loader在 module.rules 中配置，作为模块的解析规则，类型为数组。每一项都是一个 Object，内部包含了 test(类型文件)、loader、options (参数)等属性。
Plugins 在整个编译周期都起作用，Plugin在 plugins 中单独配置，类型为数组，每一项是一个 Plugin 的实例，参数都通过构造函数传入。
5.2 自定义Loader
我们知道，Loader本质上来说就是一个函数，函数中的 this 作为上下文会被 webpack 填充，因此我们不能将 loader设为一个箭头函数。该函数接受一个参数，为 webpack 传递给 loader 的文件源内容。

同时，函数中 this 是由 webpack 提供的对象，能够获取当前 loader 所需要的各种信息。函数中有异步操作或同步操作，异步操作通过 this.callback 返回，返回值要求为 string 或者 Buffer，如下。

// 导出一个函数，source为webpack传递给loader的文件源内容
module.exports = function(source) {
    const content = doSomeThing2JsString(source);

    // 如果 loader 配置了 options 对象，那么this.query将指向 options
    const options = this.query;

    // 可以用作解析其他模块路径的上下文
    console.log('this.context');

    /*
     * this.callback 参数：
     * error：Error | null，当 loader 出错时向外抛出一个 error
     * content：String | Buffer，经过 loader 编译后需要导出的内容
     * sourceMap：为方便调试生成的编译后内容的 source map
     * ast：本次编译生成的 AST 静态语法树，之后执行的 loader 可以直接使用这个 AST，进而省去重复生成 AST 的过程
     */
    this.callback(null, content); // 异步
    return content; // 同步
}
5.3 自定义Plugin
Webpack的Plugin是基于事件流框架Tapable，由于webpack基于发布订阅模式，在运行的生命周期中会广播出许多事件，插件通过监听这些事件，就可以在特定的阶段执行自己的插件任务。

同时，Webpack编译会创建两个核心对象：compiler和compilation。

compiler：包含了 Webpack 环境的所有的配置信息，包括 options，loader 和 plugin，和 webpack 整个生命周期相关的钩子.
compilation：作为 Plugin 内置事件回调函数的参数，包含了当前的模块资源、编译生成资源、变化的文件以及被跟踪依赖的状态信息。当检测到一个文件变化，一次新的 Compilation 将被创建。
如果需要自定义Plugin，也需要遵循一定的规范： - 插件必须是一个函数或者是一个包含 apply 方法的对象，这样才能访问compiler实例 - 传给每个插件的 compiler 和 compilation 对象都是同一个引用，因此不建议修改 - 异步的事件需要在插件处理完任务时调用回调函数通知 Webpack 进入下一个流程，不然会卡住

下面是一个自定Plugin的模板：

class MyPlugin {
    // Webpack 会调用 MyPlugin 实例的 apply 方法给插件实例传入 compiler 对象
  apply (compiler) {
    // 找到合适的事件钩子，实现自己的插件功能
    compiler.hooks.emit.tap('MyPlugin', compilation => {
        // compilation: 当前打包构建流程的上下文
        console.log(compilation);

        // do something...
    })
  }
}
在 emit 事件被触发后，代表源文件的转换和组装已经完成，可以读取到最终将输出的资源、代码块、模块及其依赖，并且可以修改输出资源的内容。

6.1 热更新
Webpack的热更新又称热替换（Hot Module Replacement），缩写为HMR。这个机制可以做到不用刷新浏览器而将新变更的模块替换掉旧的模块。

HMR的核心就是客户端从服务端拉去更新后的文件，准确的说是 chunk diff (chunk 需要更新的部分)，实际上 WDS 与浏览器之间维护了一个Websocket，当本地资源发生变化时，WDS 会向浏览器推送更新，并带上构建时的 hash，让客户端与上一次资源进行对比。客户端对比出差异后会向 WDS 发起Ajax请求来获取更改内容(文件列表、hash)，这样客户端就可以再借助这些信息继续向 WDS 发起jsonp请求获取该chunk的增量更新。

九、提高Webpack的构建速度
随着功能和业务代码越来越多，相应的 Webpack 的构建时间也会越来越久，构建的效率也会越来越低，那如何提升Webpack 构建速度，是前端工程化的重要一环。常用的手段有如下一些： - 优化 loader 配置 - 合理使用 resolve.extensions - 优化 resolve.modules - 优化 resolve.alias - 使用 DLLPlugin 插件 - 使用 cache-loader - terser 启动多线程 - 合理使用 sourceMap

10.3 Vite
Vite是Vue的作者尤雨溪开发的Web开发构建工具，它是一个基于浏览器原生ES模块导入的开发服务器，在开发环境下，利用浏览器去解析import，在服务器端按需编译返回，完全跳过了打包这个概念，服务器随启随用。同时不仅对Vue文件提供了支持，还支持热更新，而且热更新的速度不会随着模块增多而变慢。

Vite具有以下特点： - 快速的冷启动 - 即时热模块更新（HMR，Hot Module Replacement） - 真正按需编译

Vite由两部分组成： - 一个开发服务器，它基于 原生 ES 模块 提供了丰富的内建功能，如速度快到惊人的 [模块热更新HMR。 - 一套构建指令，它使用 Rollup打包你的代码，并且它是预配置的，可以输出用于生产环境的优化过的静态资源。

Vite在开发阶段可以直接启动开发服务器，不需要进行打包操作，也就意味着不需要分析模块的依赖、不需要编译，因此启动速度非常快。当浏览器请求某个模块的时候，根据需要对模块的内容进行编译，大大缩短了编译时间

在热模块HMR方面，当修改一个模块的时候，仅需让浏览器重新请求该模块即可，无须像Webpack那样需要把该模块的相关依赖模块全部编译一次，因此效率也更高


2、module、chunk、bundle分别是什么意思，有何区别

答案：

module -- 各个源码文件，webpack 中一切皆模块
chunk -- 多模块合并成的，如 entry import() splitChunk
bundle -- 最终的输出文件


1、前端代码为何要进行构建和打包
答案：

代码方面：

体积更小（Tree-Shaking、压缩、合并），加载更快
编译高级语言或语法（TS,ES6+,模块化，scss）
兼容性和错误检查（Polyfilll, postcss, eslint）
　　研发流程方面：

统一、高效的开发环境
统一的构建流程和产出标准
集成公司构建规范（体测、上线等）

8、webpack 常见性能优化

答案：

webpack 优化构建速度（可用于生产）
优化 babel-loader
IgnorePlugin
noParse
happyPack
ParallelUgligyPlugin
webpack 优化构建速度（不可用于生产）
自动刷新
热更新
DllPlugin
webpack 优化产出代码
小图片 base64 编码
bundle 加 hash
懒加载
提取公共代码
使用 CDN 加速
IgnorePlugin
使用 Production
Scope Hosting



# 什么是 webpack
webpack 是个静态模块打包工具。在 webpack 看来，项目里所有资源皆模块，利用资源依赖关系，把各模块之间关联起来。
简单讲就: webpack 对有依赖关系的多个模块文件进行打包处理后，生成浏览器可以直接 高效运行的资源。
通过 入口文件 开始，利用 递归 找到直接依赖或间接依赖的所有模块，并在内部构建一个能映射出项目所需的所有模块的 依赖图，并进行 webpack 打包生成一个或多个 bundle 文件。

打包是用的 webpack 命令即：npm webpack
从 webpack 4.x 开始，把 webpack 拆成 webpack 和 webpack-cli 两部分，分工如下：
webpack： 负责 Js 的打包工作
webpack-cli：解析 webpack 命令，命令内部使用 webpack 的功能
并且 webpack 能解析打包各种模块规范的 Js 代码，包括：ES6、Commonjs、AND/Requirejs 以及 CMD/Seajs。

优点

专注于处理模块化的项目，能做到开箱即用，一步到位
可通过 plugin 扩展，完整好用又不失灵活
使用场景不局限于 web 开发
社区庞大活跃，经常引入紧跟时代发展的新特性，能为大多数场景找到已有的开源扩展
良好的开发体验
缺点

只能用于采用模块化开发的项目

# 什么是 loader ? 什么是 plugin ?
loader：模块转换器，webpack 将一切文件视为模块，但 webpack 只能解析 JavaScript 文件，而 loader 作用是让 webpack 拥有了加载 和 解析非 JavaScript 文件的能力。

plugin：在 webpack 构建流程中的特定时机注入扩展逻辑，让它具有更多的灵活性。在 webpack 运行的生命周期中会广播出许多事件，plugin 可以监听这些事件，在合适的时机通过 webpack 提供的 API 改变输出结果。

用法的区别：

Loader 在 module.rules 中配置，也就是说他作为模块的解析规则而存在。 类型为数组，每一项都是一个Object，里面描述了对于什么类型的文件（test），使用什么加载(loader)和使用的参数（options）
Plugin 在 plugins 中单独配置。 类型为数组，每一项是一个 plugin 的实例，参数都通过构造函数传入。

# 有哪些常见的 Loader ？他们是解决什么问题的？
file-loader：把文件输出到一个文件夹中，在代码中通过相对 URL 去引用输出的文件
url-loader：和 file-loader 类似，但是能在文件很小的情况下以 base64 的方式把文件内容注入到代码中去
source-map-loader：加载额外的 Source Map 文件，以方便断点调试
image-loader：加载并且压缩图片文件
babel-loader：把 ES6 转换成 ES5
css-loader：加载 CSS，支持模块化、压缩、文件导入等特性
style-loader：把 CSS 代码注入到 JavaScript 中，通过 DOM 操作去加载 CSS。
eslint-loader：通过 ESLint 检查 JavaScript 代码
svg-inline-loader：将压缩后的 SVG 内容注入代码中
json-loader : 加载 JSON 文件（默认包含）
ts-loader : 将 TypeScript 转换成 JavaScript
awesome-typescript-loader：将 TypeScript 转换成 JavaScript，性能优于 ts-loader
sass-loader：将 CSS 代码注入 JavaScript 中，通过 DOM 操作去加载 CSS
postcss-loader：扩展 CSS 语法，使用下一代 CSS，可以配合 autoprefixer 插件自动补齐 CSS3 前缀
tslint-loader：通过 TSLint检查 TypeScript 代码
vue-loader：加载 Vue.js 单文件组件

# 有哪些常见的 Plugin？他们是解决什么问题的？
define-plugin：定义环境变量
commons-chunk-plugin：提取公共代码
terser-webpack-plugin : 支持压缩 ES6 (Webpack4)
ignore-plugin：忽略部分文件
html-webpack-plugin：简化 HTML 文件创建 (依赖于 html-loader)
web-webpack-plugin：可方便地为单页应用输出 HTML，比 html-webpack-plugin 好用
mini-css-extract-plugin : 分离样式文件，CSS 提取为独立文件，支持按需加载
serviceworker-webpack-plugin：为网页应用增加离线缓存功能
clean-webpack-plugin : 删除打包文件
happypack：实现多线程加速编译

# 如何利用 webpack 来优化前端性能？
用 webpack 优化前端性能是指优化 webpack 的输出结果，让打包的最终结果在浏览器运行快速高效。

1.压缩代码。删除多余的代码、注释、简化代码的写法等等方式。
用 UglifyJsPlugin和ParallelUglifyPlugin 压缩JS文件
用 mini-css-extract-plugin 压缩 CSS

利用 CDN 加速。在构建过程中，将引用的静态资源路径修改为CDN上对应的路径。可以利用 webpack 对于output 参数和各 loader 的 publicPath 参数来修改资源路径
删除死代码。JS 用 Tree Shaking，CSS 需要使用 Purify-CSS
提取公共代码。用 CommonsChunkPlugin 插件
# 分别介绍 bundle，chunk，module 是什么
bundle：是由 webpack 打包出来的文件，
chunk：代码块，一个 chunk 由多个模块组合而成，用于代码的合并和分割。
module：是开发中的单个模块，在 webpack 的世界，一切皆模块，一个模块对应一个文件，webpack 会从配置的 entry 中递归开始找出所有依赖的模块。


# 如何提高 webpack 的构建速度？
多入口情况下，使用 CommonsChunkPlugin 来提取公共代码
通过externals配置来提取常用库
利用 DllPlugin 和 DllReferencePlugin 预编译资源模块 通过 DllPlugin 来对那些我们引用但是绝对不会修改的npm包来进行预编译，再通过 DllReferencePlugin 将预编译的模块加载进来。
使用 Happypack 实现多线程加速编译
使用 webpack-uglify-parallel 来提升 uglifyPlugin 的压缩速度。 原理上 webpack-uglify-parallel 采用了多核并行压缩来提升压缩速度
使用 Tree-shaking 和 Scope Hoisting 来剔除多余代码


vite 
开启本地服务，不打包，使用es moudale方式，根据依赖按需加载moudle
