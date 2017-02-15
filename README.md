# nan-api-chinese
Node编写C++扩展的Nan中文API文档，Nan是对于V8的一些API的二次抽象，旨在解决编写Node插件时遇到的跨v8版本的API兼容性问题。

并且其简化了部分的API调用形式，帮助大家更容易开发跨Node版本（0.8, 0.10, 0.12, 1, 4, 5, 6 和 7）的插件。

# API

除了下面的NAN API文档会提到的部分，剩下的问题还可以去咨询下面网站：
* [v8启动指南](https://github.com/v8/v8/wiki/Getting%20Started%20with%20Embedding)
* [v8嵌入应用指南](https://github.com/v8/v8/wiki/Embedder%27s%20Guide)
* [v8 API文档](http://v8docs.nodesource.com/)
* [如何编写Node插件](Node Add-on Documentation)

# 给JavaScript使用的方法
一个模板(v8 template)是JavaScript函数和对象在一段上下文中的蓝图。你可以使用模板将c++的函数和数据结构包裹进JavaScript对象中，这样子就能在js中操作这些c++函数或者数据结构了。想要理解更多详细的信息可以查看v8嵌入指南的[Templates](https://developers.google.com/v8/embed#templates)章节查看。

为了通过v8的模板将我们所需要的c++函数和数据结构暴露给JavaScript调用，我们必须提供一种能让v8引擎理解的形式来编写扩展代码。在NAN模块支持的一系列v8版本中，由引擎提供的原生JavaScript-accessible函数在不同的v8版本中差别非常大。在这种情况下，NAN模块将这些方法声明进行了二次抽象，对于开发者来说，NAN抽象后提供的API将非常接近最新版本的V8 API接口格式，但是对于老版本的API依旧做到了向后的兼容。

* **方法参数类型**
 * [Nan::FunctionCallbackInfo]()
 * [Nan::PropertyCallbackInfo]()
 * [Nan::ReturnValue]()
* **方法声明** 


