# nan-api-chinese
Node编写C++扩展的Nan中文API文档，Nan是对于V8的一些API的二次抽象，旨在解决编写Node插件时遇到的跨v8版本的API兼容性问题。

并且其简化了部分的API调用形式，帮助大家更容易开发跨Node版本（0.8, 0.10, 0.12, 1, 4, 5, 6 和 7）的插件。

## API

除了下面的NAN API文档会提到的部分，剩下的问题还可以去咨询下面网站：
* [v8启动指南](https://github.com/v8/v8/wiki/Getting%20Started%20with%20Embedding)
* [v8嵌入应用指南](https://github.com/v8/v8/wiki/Embedder%27s%20Guide)
* [v8 API文档](http://v8docs.nodesource.com/)
* [如何编写Node插件](Node Add-on Documentation)

<!-- 开始编写API说明 -->
## JavaScript-accessible方法
一个模板(v8 template)是JavaScript函数和对象在一段上下文中的蓝图。你可以使用模板将c++的函数和数据结构包裹进JavaScript对象中，这样子就能在js中操作这些c++函数或者数据结构了。想要理解更多详细的信息可以查看v8嵌入指南的[Templates](https://developers.google.com/v8/embed#templates)章节查看。

为了通过v8的模板将我们所需要的c++函数和数据结构暴露给JavaScript调用，我们必须提供一种能让v8引擎理解的形式来编写扩展代码。在NAN模块支持的一系列v8版本中，由引擎提供的原生JavaScript-accessible函数在不同的v8版本中差别非常大。在这种情况下，NAN模块将这些方法声明进行了二次抽象，对于开发者来说，NAN抽象后提供的API将非常接近最新版本的V8 API接口格式，但是对于老版本的API依旧做到了向后的兼容。

* **方法参数类型**
 - <a href="doc/methods.md#api_nan_function_callback_info"><b><code>Nan::FunctionCallbackInfo</code></b></a>
 - <a href="doc/methods.md#api_nan_property_callback_info"><b><code>Nan::PropertyCallbackInfo</code></b></a>
 - <a href="doc/methods.md#api_nan_return_value"><b><code>Nan::ReturnValue</code></b></a>
* **方法声明**
 - <a href="doc/methods.md#api_nan_method"><b>Method declaration</b></a>
 - <a href="doc/methods.md#api_nan_getter"><b>Getter declaration</b></a>
 - <a href="doc/methods.md#api_nan_setter"><b>Setter declaration</b></a>
 - <a href="doc/methods.md#api_nan_property_getter"><b>Property getter declaration</b></a>
 - <a href="doc/methods.md#api_nan_property_setter"><b>Property setter declaration</b></a>
 - <a href="doc/methods.md#api_nan_property_enumerator"><b>Property enumerator declaration</b></a>
 - <a href="doc/methods.md#api_nan_property_deleter"><b>Property deleter declaration</b></a>
 - <a href="doc/methods.md#api_nan_property_query"><b>Property query declaration</b></a>
 - <a href="doc/methods.md#api_nan_index_getter"><b>Index getter declaration</b></a>
 - <a href="doc/methods.md#api_nan_index_setter"><b>Index setter declaration</b></a>
 - <a href="doc/methods.md#api_nan_index_enumerator"><b>Index enumerator declaration</b></a>
 - <a href="doc/methods.md#api_nan_index_deleter"><b>Index deleter declaration</b></a>
 - <a href="doc/methods.md#api_nan_index_query"><b>Index query declaration</b></a>
* Method and template helpers
 - <a href="doc/methods.md#api_nan_set_method"><b><code>Nan::SetMethod()</code></b></a>
 - <a href="doc/methods.md#api_nan_set_prototype_method"><b><code>Nan::SetPrototypeMethod()</code></b></a>
 - <a href="doc/methods.md#api_nan_set_accessor"><b><code>Nan::SetAccessor()</code></b></a>
 - <a href="doc/methods.md#api_nan_set_named_property_handler"><b><code>Nan::SetNamedPropertyHandler()</code></b></a>
 - <a href="doc/methods.md#api_nan_set_indexed_property_handler"><b><code>Nan::SetIndexedPropertyHandler()</code></b></a>
 - <a href="doc/methods.md#api_nan_set_template"><b><code>Nan::SetTemplate()</code></b></a>
 - <a href="doc/methods.md#api_nan_set_prototype_template"><b><code>Nan::SetPrototypeTemplate()</code></b></a>
 - <a href="doc/methods.md#api_nan_set_instance_template"><b><code>Nan::SetInstanceTemplate()</code></b></a>
 - <a href="doc/methods.md#api_nan_set_call_handler"><b><code>Nan::SetCallHandler()</code></b></a>
 - <a href="doc/methods.md#api_nan_set_call_as_function_handler"><b><code>Nan::SetCallAsFunctionHandler()</code></b></a>

### Scopes

A _local handle_ is a pointer to an object. All V8 objects are accessed using handles, they are necessary because of the way the V8 garbage collector works.

A handle scope can be thought of as a container for any number of handles. When you've finished with your handles, instead of deleting each one individually you can simply delete their scope.

The creation of `HandleScope` objects is different across the supported versions of V8. Therefore, NAN provides its own implementations that can be used safely across these.

 - <a href="doc/scopes.md#api_nan_handle_scope"><b><code>Nan::HandleScope</code></b></a>
 - <a href="doc/scopes.md#api_nan_escapable_handle_scope"><b><code>Nan::EscapableHandleScope</code></b></a>

Also see the V8 Embedders Guide section on [Handles and Garbage Collection](https://github.com/v8/v8/wiki/Embedder%27s%20Guide#handles-and-garbage-collection).

### Persistent references

An object reference that is independent of any `HandleScope` is a _persistent_ reference. Where a `Local` handle only lives as long as the `HandleScope` in which it was allocated, a `Persistent` handle remains valid until it is explicitly disposed.

Due to the evolution of the V8 API, it is necessary for NAN to provide a wrapper implementation of the `Persistent` classes to supply compatibility across the V8 versions supported.

 - <a href="doc/persistent.md#api_nan_persistent_base"><b><code>Nan::PersistentBase & v8::PersistentBase</code></b></a>
 - <a href="doc/persistent.md#api_nan_non_copyable_persistent_traits"><b><code>Nan::NonCopyablePersistentTraits & v8::NonCopyablePersistentTraits</code></b></a>
 - <a href="doc/persistent.md#api_nan_copyable_persistent_traits"><b><code>Nan::CopyablePersistentTraits & v8::CopyablePersistentTraits</code></b></a>
 - <a href="doc/persistent.md#api_nan_persistent"><b><code>Nan::Persistent</code></b></a>
 - <a href="doc/persistent.md#api_nan_global"><b><code>Nan::Global</code></b></a>
 - <a href="doc/persistent.md#api_nan_weak_callback_info"><b><code>Nan::WeakCallbackInfo</code></b></a>
 - <a href="doc/persistent.md#api_nan_weak_callback_type"><b><code>Nan::WeakCallbackType</code></b></a>

Also see the V8 Embedders Guide section on [Handles and Garbage Collection](https://developers.google.com/v8/embed#handles).

### New

NAN provides a `Nan::New()` helper for the creation of new JavaScript objects in a way that's compatible across the supported versions of V8.

 - <a href="doc/new.md#api_nan_new"><b><code>Nan::New()</code></b></a>
 - <a href="doc/new.md#api_nan_undefined"><b><code>Nan::Undefined()</code></b></a>
 - <a href="doc/new.md#api_nan_null"><b><code>Nan::Null()</code></b></a>
 - <a href="doc/new.md#api_nan_true"><b><code>Nan::True()</code></b></a>
 - <a href="doc/new.md#api_nan_false"><b><code>Nan::False()</code></b></a>
 - <a href="doc/new.md#api_nan_empty_string"><b><code>Nan::EmptyString()</code></b></a>


### Converters

NAN contains functions that convert `v8::Value`s to other `v8::Value` types and native types. Since type conversion is not guaranteed to succeed, they return `Nan::Maybe` types. These converters can be used in place of `value->ToX()` and `value->XValue()` (where `X` is one of the types, e.g. `Boolean`) in a way that provides a consistent interface across V8 versions. Newer versions of V8 use the new `v8::Maybe` and `v8::MaybeLocal` types for these conversions, older versions don't have this functionality so it is provided by NAN.

 - <a href="doc/converters.md#api_nan_to"><b><code>Nan::To()</code></b></a>

### Maybe Types

The `Nan::MaybeLocal` and `Nan::Maybe` types are monads that encapsulate `v8::Local` handles that _may be empty_.

* **Maybe Types**
  - <a href="doc/maybe_types.md#api_nan_maybe_local"><b><code>Nan::MaybeLocal</code></b></a>
  - <a href="doc/maybe_types.md#api_nan_maybe"><b><code>Nan::Maybe</code></b></a>
  - <a href="doc/maybe_types.md#api_nan_nothing"><b><code>Nan::Nothing</code></b></a>
  - <a href="doc/maybe_types.md#api_nan_just"><b><code>Nan::Just</code></b></a>
* **Maybe Helpers**
  - <a href="doc/maybe_types.md#api_nan_call"><b><code>Nan::Call()</code></b></a>
  - <a href="doc/maybe_types.md#api_nan_to_detail_string"><b><code>Nan::ToDetailString()</code></b></a>
  - <a href="doc/maybe_types.md#api_nan_to_array_index"><b><code>Nan::ToArrayIndex()</code></b></a>
  - <a href="doc/maybe_types.md#api_nan_equals"><b><code>Nan::Equals()</code></b></a>
  - <a href="doc/maybe_types.md#api_nan_new_instance"><b><code>Nan::NewInstance()</code></b></a>
  - <a href="doc/maybe_types.md#api_nan_get_function"><b><code>Nan::GetFunction()</code></b></a>
  - <a href="doc/maybe_types.md#api_nan_set"><b><code>Nan::Set()</code></b></a>
  - <a href="doc/maybe_types.md#api_nan_force_set"><b><code>Nan::ForceSet()</code></b></a>
  - <a href="doc/maybe_types.md#api_nan_get"><b><code>Nan::Get()</code></b></a>
  - <a href="doc/maybe_types.md#api_nan_get_property_attribute"><b><code>Nan::GetPropertyAttributes()</code></b></a>
  - <a href="doc/maybe_types.md#api_nan_has"><b><code>Nan::Has()</code></b></a>
  - <a href="doc/maybe_types.md#api_nan_delete"><b><code>Nan::Delete()</code></b></a>
  - <a href="doc/maybe_types.md#api_nan_get_property_names"><b><code>Nan::GetPropertyNames()</code></b></a>
  - <a href="doc/maybe_types.md#api_nan_get_own_property_names"><b><code>Nan::GetOwnPropertyNames()</code></b></a>
  - <a href="doc/maybe_types.md#api_nan_set_prototype"><b><code>Nan::SetPrototype()</code></b></a>
  - <a href="doc/maybe_types.md#api_nan_object_proto_to_string"><b><code>Nan::ObjectProtoToString()</code></b></a>
  - <a href="doc/maybe_types.md#api_nan_has_own_property"><b><code>Nan::HasOwnProperty()</code></b></a>
  - <a href="doc/maybe_types.md#api_nan_has_real_named_property"><b><code>Nan::HasRealNamedProperty()</code></b></a>
  - <a href="doc/maybe_types.md#api_nan_has_real_indexed_property"><b><code>Nan::HasRealIndexedProperty()</code></b></a>
  - <a href="doc/maybe_types.md#api_nan_has_real_named_callback_property"><b><code>Nan::HasRealNamedCallbackProperty()</code></b></a>
  - <a href="doc/maybe_types.md#api_nan_get_real_named_property_in_prototype_chain"><b><code>Nan::GetRealNamedPropertyInPrototypeChain()</code></b></a>
  - <a href="doc/maybe_types.md#api_nan_get_real_named_property"><b><code>Nan::GetRealNamedProperty()</code></b></a>
  - <a href="doc/maybe_types.md#api_nan_call_as_function"><b><code>Nan::CallAsFunction()</code></b></a>
  - <a href="doc/maybe_types.md#api_nan_call_as_constructor"><b><code>Nan::CallAsConstructor()</code></b></a>
  - <a href="doc/maybe_types.md#api_nan_get_source_line"><b><code>Nan::GetSourceLine()</code></b></a>
  - <a href="doc/maybe_types.md#api_nan_get_line_number"><b><code>Nan::GetLineNumber()</code></b></a>
  - <a href="doc/maybe_types.md#api_nan_get_start_column"><b><code>Nan::GetStartColumn()</code></b></a>
  - <a href="doc/maybe_types.md#api_nan_get_end_column"><b><code>Nan::GetEndColumn()</code></b></a>
  - <a href="doc/maybe_types.md#api_nan_clone_element_at"><b><code>Nan::CloneElementAt()</code></b></a>
  - <a href="doc/maybe_types.md#api_nan_has_private"><b><code>Nan::HasPrivate()</code></b></a>
  - <a href="doc/maybe_types.md#api_nan_get_private"><b><code>Nan::GetPrivate()</code></b></a>
  - <a href="doc/maybe_types.md#api_nan_set_private"><b><code>Nan::SetPrivate()</code></b></a>
  - <a href="doc/maybe_types.md#api_nan_delete_private"><b><code>Nan::DeletePrivate()</code></b></a>
  - <a href="doc/maybe_types.md#api_nan_make_maybe"><b><code>Nan::MakeMaybe()</code></b></a>

### Script

NAN provides a `v8::Script` helpers as the API has changed over the supported versions of V8.

 - <a href="doc/script.md#api_nan_compile_script"><b><code>Nan::CompileScript()</code></b></a>
 - <a href="doc/script.md#api_nan_run_script"><b><code>Nan::RunScript()</code></b></a>


### Errors

NAN includes helpers for creating, throwing and catching Errors as much of this functionality varies across the supported versions of V8 and must be abstracted.

Note that an Error object is simply a specialized form of `v8::Value`.

Also consult the V8 Embedders Guide section on [Exceptions](https://developers.google.com/v8/embed#exceptions) for more information.

 - <a href="doc/errors.md#api_nan_error"><b><code>Nan::Error()</code></b></a>
 - <a href="doc/errors.md#api_nan_range_error"><b><code>Nan::RangeError()</code></b></a>
 - <a href="doc/errors.md#api_nan_reference_error"><b><code>Nan::ReferenceError()</code></b></a>
 - <a href="doc/errors.md#api_nan_syntax_error"><b><code>Nan::SyntaxError()</code></b></a>
 - <a href="doc/errors.md#api_nan_type_error"><b><code>Nan::TypeError()</code></b></a>
 - <a href="doc/errors.md#api_nan_throw_error"><b><code>Nan::ThrowError()</code></b></a>
 - <a href="doc/errors.md#api_nan_throw_range_error"><b><code>Nan::ThrowRangeError()</code></b></a>
 - <a href="doc/errors.md#api_nan_throw_reference_error"><b><code>Nan::ThrowReferenceError()</code></b></a>
 - <a href="doc/errors.md#api_nan_throw_syntax_error"><b><code>Nan::ThrowSyntaxError()</code></b></a>
 - <a href="doc/errors.md#api_nan_throw_type_error"><b><code>Nan::ThrowTypeError()</code></b></a>
 - <a href="doc/errors.md#api_nan_fatal_exception"><b><code>Nan::FatalException()</code></b></a>
 - <a href="doc/errors.md#api_nan_errno_exception"><b><code>Nan::ErrnoException()</code></b></a>
 - <a href="doc/errors.md#api_nan_try_catch"><b><code>Nan::TryCatch</code></b></a>


### Buffers

NAN's `node::Buffer` helpers exist as the API has changed across supported Node versions. Use these methods to ensure compatibility.

 - <a href="doc/buffers.md#api_nan_new_buffer"><b><code>Nan::NewBuffer()</code></b></a>
 - <a href="doc/buffers.md#api_nan_copy_buffer"><b><code>Nan::CopyBuffer()</code></b></a>
 - <a href="doc/buffers.md#api_nan_free_callback"><b><code>Nan::FreeCallback()</code></b></a>

### Nan::Callback

`Nan::Callback` makes it easier to use `v8::Function` handles as callbacks. A class that wraps a `v8::Function` handle, protecting it from garbage collection and making it particularly useful for storage and use across asynchronous execution.

 - <a href="doc/callback.md#api_nan_callback"><b><code>Nan::Callback</code></b></a>

### Asynchronous work helpers

`Nan::AsyncWorker` and `Nan::AsyncProgressWorker` are helper classes that make working with asynchronous code easier.

 - <a href="doc/asyncworker.md#api_nan_async_worker"><b><code>Nan::AsyncWorker</code></b></a>
 - <a href="doc/asyncworker.md#api_nan_async_progress_worker"><b><code>Nan::AsyncProgressWorkerBase &amp; Nan::AsyncProgressWorker</code></b></a>
 - <a href="doc/asyncworker.md#api_nan_async_queue_worker"><b><code>Nan::AsyncQueueWorker</code></b></a>

### Strings & Bytes

Miscellaneous string & byte encoding and decoding functionality provided for compatibility across supported versions of V8 and Node. Implemented by NAN to ensure that all encoding types are supported, even for older versions of Node where they are missing.

 - <a href="doc/string_bytes.md#api_nan_encoding"><b><code>Nan::Encoding</code></b></a>
 - <a href="doc/string_bytes.md#api_nan_encode"><b><code>Nan::Encode()</code></b></a>
 - <a href="doc/string_bytes.md#api_nan_decode_bytes"><b><code>Nan::DecodeBytes()</code></b></a>
 - <a href="doc/string_bytes.md#api_nan_decode_write"><b><code>Nan::DecodeWrite()</code></b></a>


### Object Wrappers

The `ObjectWrap` class can be used to make wrapped C++ objects and a factory of wrapped objects.

 - <a href="doc/object_wrappers.md#api_nan_object_wrap"><b><code>Nan::ObjectWrap</code></b></a>


### V8 internals

The hooks to access V8 internals—including GC and statistics—are different across the supported versions of V8, therefore NAN provides its own hooks that call the appropriate V8 methods.

 - <a href="doc/v8_internals.md#api_nan_gc_callback"><b><code>NAN_GC_CALLBACK()</code></b></a>
 - <a href="doc/v8_internals.md#api_nan_add_gc_epilogue_callback"><b><code>Nan::AddGCEpilogueCallback()</code></b></a>
 - <a href="doc/v8_internals.md#api_nan_remove_gc_epilogue_callback"><b><code>Nan::RemoveGCEpilogueCallback()</code></b></a>
 - <a href="doc/v8_internals.md#api_nan_add_gc_prologue_callback"><b><code>Nan::AddGCPrologueCallback()</code></b></a>
 - <a href="doc/v8_internals.md#api_nan_remove_gc_prologue_callback"><b><code>Nan::RemoveGCPrologueCallback()</code></b></a>
 - <a href="doc/v8_internals.md#api_nan_get_heap_statistics"><b><code>Nan::GetHeapStatistics()</code></b></a>
 - <a href="doc/v8_internals.md#api_nan_set_counter_function"><b><code>Nan::SetCounterFunction()</code></b></a>
 - <a href="doc/v8_internals.md#api_nan_set_create_histogram_function"><b><code>Nan::SetCreateHistogramFunction()</code></b></a>
 - <a href="doc/v8_internals.md#api_nan_set_add_histogram_sample_function"><b><code>Nan::SetAddHistogramSampleFunction()</code></b></a>
 - <a href="doc/v8_internals.md#api_nan_idle_notification"><b><code>Nan::IdleNotification()</code></b></a>
 - <a href="doc/v8_internals.md#api_nan_low_memory_notification"><b><code>Nan::LowMemoryNotification()</code></b></a>
 - <a href="doc/v8_internals.md#api_nan_context_disposed_notification"><b><code>Nan::ContextDisposedNotification()</code></b></a>
 - <a href="doc/v8_internals.md#api_nan_get_internal_field_pointer"><b><code>Nan::GetInternalFieldPointer()</code></b></a>
 - <a href="doc/v8_internals.md#api_nan_set_internal_field_pointer"><b><code>Nan::SetInternalFieldPointer()</code></b></a>
 - <a href="doc/v8_internals.md#api_nan_adjust_external_memory"><b><code>Nan::AdjustExternalMemory()</code></b></a>


### Miscellaneous V8 Helpers

 - <a href="doc/v8_misc.md#api_nan_utf8_string"><b><code>Nan::Utf8String</code></b></a>
 - <a href="doc/v8_misc.md#api_nan_get_current_context"><b><code>Nan::GetCurrentContext()</code></b></a>
 - <a href="doc/v8_misc.md#api_nan_set_isolate_data"><b><code>Nan::SetIsolateData()</code></b></a>
 - <a href="doc/v8_misc.md#api_nan_get_isolate_data"><b><code>Nan::GetIsolateData()</code></b></a>
 - <a href="doc/v8_misc.md#api_nan_typedarray_contents"><b><code>Nan::TypedArrayContents</code></b></a>


### Miscellaneous Node Helpers

 - <a href="doc/node_misc.md#api_nan_make_callback"><b><code>Nan::MakeCallback()</code></b></a>
 - <a href="doc/node_misc.md#api_nan_module_init"><b><code>NAN_MODULE_INIT()</code></b></a>
 - <a href="doc/node_misc.md#api_nan_export"><b><code>Nan::Export()</code></b></a>

<!-- END API -->


