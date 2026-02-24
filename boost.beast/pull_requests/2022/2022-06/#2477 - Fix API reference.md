# #2477 Fix API reference [Merged]

> Username: alandefreitas  
> Created at: 2022-06-30 17:49:19 UTC  
> Updated at: 2022-10-02 14:34:36 UTC  
> Merged at: 2022-10-02 14:34:36 UTC  
> Closed at: 2022-10-02 14:34:36 UTC  
> Url: https://github.com/boostorg/beast/pull/2477  

This PR fixes lots of Doxygen broken links and errors that have probably been accumulating over time.

---

## Comment 1

> Username: alandefreitas  
> Created_at: 2022-06-30 17:50:56 UTC  
> Url: https://github.com/boostorg/beast/pull/2477#issuecomment-1171510744  

This PR fixes lots of Doxygen broken links and errors that have been probably accumulating over the years.  
  
Here is a summary of the errors and how errors in each category were fixed:  
  
<hr/>  
  
Reference to Concept that doesn't exist in doxygen.  
  
```console  
Searching for .../boost/beast/http/message_generator.hpp:56: warning: unable to resolve reference to 'BuffersGenerator' for \\ref command  
```  
  
```console  
Generating XML output for class .../boost/libs/beast/include/boost/beast/core/buffers_generator.hpp:139: warning: unable to resolve reference to 'BuffersGenerator' for \ref command  
```  
  
Doxygen does not support concepts. It seems like some people use `@interface` as a workaround to document concepts, but that requires the toolchain to understand we are doing that.  
  
<hr/>  
  
Doxygen required the fully qualified name at some points.  
  
```console  
Parsing file .../boost/libs/beast/include/boost/beast/core/buffers\_range.hpp...../boost/libs/beast/include/boost/beast/core/buffers\_range.hpp:123: warning: unbalanced grouping commands  
```  
  
```console  
Searching for .../boost/beast/http/fields.hpp:102: warning: unable to resolve reference to 'field::unknown' for \\ref command  
```  
  
```console  
Searching for .../boost/beast/http/fields.hpp:638: warning: @copybrief or @copydoc target 'basic\_fields::equal\_range(boost::beast::http::field) const' not found  
```  
  
```console  
Generating docs for compound boost::beast::http::chunk_.../boost/libs/beast/include/boost/beast/websocket/error.hpp:20: warning: unable to resolve reference to 'beast::websocket::stream' for \\ref command  
```  
  
```console  
.../boost/libs/beast/include/boost/beast/core/async_base.hpp:287: warning: unable to resolve reference to 'async_base' for \ref command  
```  
  
```console  
.../boost/libs/beast/include/boost/beast/core/async_base.hpp:300: warning: unable to resolve reference to 'async_base' for \ref command  
```  
  
```console  
.../boost/libs/beast/include/boost/beast/core/async_base.hpp:335: warning: unable to resolve reference to 'complete' for \ref command  
```  
  
```console  
.../boost/libs/beast/include/boost/beast/core/async_base.hpp:335: warning: unable to resolve reference to 'complete_now' for \ref command  
```  
  
```console  
.../boost/libs/beast/include/boost/beast/core/async_base.hpp:337: warning: unable to resolve reference to 'beast::allocate_stable' for \ref command  
```  
  
```console  
.../boost/libs/beast/include/boost/beast/core/async_base.hpp:344: warning: unable to resolve reference to 'complete_now' for \ref command  
```  
  
```console  
.../boost/libs/beast/include/boost/beast/core/async_base.hpp:376: warning: unable to resolve reference to 'complete' for \ref command  
```  
  
```console  
.../boost/libs/beast/include/boost/beast/core/async_base.hpp:376: warning: unable to resolve reference to 'complete_now' for \ref command  
```  
  
```console  
.../boost/libs/beast/include/boost/beast/core/async_base.hpp:378: warning: unable to resolve reference to 'beast::allocate_stable' for \ref command  
```  
  
```console  
.../boost/libs/beast/include/boost/beast/core/async_base.hpp:194: warning: unable to resolve reference to 'async_base' for \ref command  
```  
  
```console  
.../boost/libs/beast/include/boost/beast/core/async_base.hpp:278: warning: unable to resolve reference to 'async_base' for \ref command  
```  
  
```console  
.../boost/libs/beast/include/boost/beast/http/fields.hpp:427: warning: unable to resolve reference to 'string_view' for \ref command  
```  
  
```console  
.../boost/libs/beast/include/boost/beast/http/fields.hpp:445: warning: unable to resolve reference to 'string_view' for \ref command  
```  
  
```console  
.../boost/libs/beast/include/boost/beast/http/fields.hpp:468: warning: unable to resolve reference to 'string_view' for \ref command  
```  
  
```console  
.../boost/libs/beast/include/boost/beast/http/fields.hpp:484: warning: unable to resolve reference to 'string_view' for \ref command  
```  
  
```console  
.../boost/libs/beast/include/boost/beast/http/fields.hpp:501: warning: unable to resolve reference to 'string_view' for \ref command  
```  
  
```console  
.../boost/libs/beast/include/boost/beast/http/fields.hpp:427: warning: unable to resolve reference to 'string_view' for \ref command  
```  
  
```console  
.../boost/libs/beast/include/boost/beast/http/fields.hpp:445: warning: unable to resolve reference to 'string_view' for \ref command  
```  
  
```console  
.../boost/libs/beast/include/boost/beast/http/fields.hpp:468: warning: unable to resolve reference to 'string_view' for \ref command  
```  
  
```console  
.../boost/libs/beast/include/boost/beast/http/fields.hpp:484: warning: unable to resolve reference to 'string_view' for \ref command  
```  
  
```console  
.../boost/libs/beast/include/boost/beast/http/fields.hpp:501: warning: unable to resolve reference to 'string_view' for \ref command  
```  
  
Maybe there's a way to make Doxygen better understand relative references, but I'm not aware of such an option.  
  
<hr/>  
  
Type has changed in a previous commit.  
  
```console  
Searching for .../boost/beast/http/span_body.hpp:24: warning: unable to resolve reference to 'span' for \\ref command  
```  
  
<hr/>  
  
`@param` name mismatch. Whenever this happened, I've updated the `@param` from the function declaration instead of updating the declaration.   
  
```console  
Generating XML output for class boo.../boost/libs/beast/include/boost/beast/_experimental/test/stream.hpp:235: warning: argument 'ioc' of command @param is not found in the argument list of boost::beast::test::basic_stream< Executor >::basic_stream(ExecutionContext &context, typename std::enable_if< std::is_convertible< ExecutionContext &, net::execution_context & >::value >::type *=0)  
```  
  
```console  
.../boost/libs/beast/include/boost/beast/_experimental/test/stream.hpp:235: warning: The following parameter of boost::beast::test::basic_stream::basic_stream(ExecutionContext &context, typename std::enable_if< std::is_convertible< ExecutionContext &, net::execution_context & >::value >::type *=0) is not documented:  
parameter 'context'  
```  
  
```console  
.../boost/libs/beast/include/boost/beast/core/buffers_generator.hpp:138: warning: argument 'handler' of command @param is not found in the argument list of boost::beast::async_write(AsyncWriteStream &stream, BuffersGenerator generator, CompletionToken &&token) -> typename net::async_result< typename std::decay< CompletionToken >::type, void(error_code, std::size_t)>::return_type  
```  
  
```console  
.../boost/libs/beast/include/boost/beast/core/buffers_generator.hpp:138: warning: The following parameter of boost::beast::async_write(AsyncWriteStream &stream, BuffersGenerator generator, CompletionToken &&token) -> typename net::async_result< typename std::decay< CompletionToken >::type, void(error_code, std::size_t)>::return_type is not documented:  
parameter 'token'  
```  
  
<hr/>  
  
Macros that weren't being properly replaced in Doxygen. I've adjusted `doc/Jamfile` to make Doxygen understand these.  
  
```console  
.../boost/libs/beast/include/boost/beast/_experimental/test/stream.hpp:439: warning: argument 'handler' of command @param is not found in the argument list of boost::beast::test::basic_stream< Executor >::async_read_some(MutableBufferSequence const &buffers, ReadHandler &&handler BOOST_ASIO_DEFAULT_COMPLETION_TOKENexecutor_type)  
```  
  
```console  
.../boost/libs/beast/include/boost/beast/_experimental/test/stream.hpp:439: warning: The following parameter of boost::beast::test::basic_stream::async_read_some(MutableBufferSequence const &buffers, ReadHandler &&handler BOOST_ASIO_DEFAULT_COMPLETION_TOKENexecutor_type) is not documented:  
parameter 'BOOST_ASIO_DEFAULT_COMPLETION_TOKEN'  
```  
  
```console  
.../boost/libs/beast/include/boost/beast/_experimental/test/stream.hpp:519: warning: argument 'handler' of command @param is not found in the argument list of boost::beast::test::basic_stream< Executor >::async_write_some(ConstBufferSequence const &buffers, WriteHandler &&handler BOOST_ASIO_DEFAULT_COMPLETION_TOKENexecutor_type)  
```  
  
```console  
.../boost/libs/beast/include/boost/beast/_experimental/test/stream.hpp:519: warning: The following parameter of boost::beast::test::basic_stream::async_write_some(ConstBufferSequence const &buffers, WriteHandler &&handler BOOST_ASIO_DEFAULT_COMPLETION_TOKENexecutor_type) is not documented:  
parameter 'BOOST_ASIO_DEFAULT_COMPLETION_TOKEN'  
```  
  
<hr/>  
  
An extra error we got was  
  
```console  
xslt-xsltproc-dir libs/beast/doc/html/beast_HTML.manifest  
Error: no ID for constraint linkend: "beast.ref.".  
```  
  
I have no idea where this is coming from.

---

## Comment 2

> Username: cppalliance-bot  
> Created_at: 2022-06-30 17:56:38 UTC  
> Url: https://github.com/boostorg/beast/pull/2477#issuecomment-1171515659  

An automated preview of the documentation is available at [https://2477.beastdocs.prtest.cppalliance.org/libs/beast/doc/html/index.html](https://2477.beastdocs.prtest.cppalliance.org/libs/beast/doc/html/index.html)

---

## Comment 3

> Username: vinniefalco  
> Created_at: 2022-06-30 17:59:46 UTC  
> Url: https://github.com/boostorg/beast/pull/2477#issuecomment-1171518485  

@evanlenz any idea about that last one?

---

## Comment 4

> Username: cppalliance-bot  
> Created_at: 2022-06-30 18:17:02 UTC  
> Url: https://github.com/boostorg/beast/pull/2477#issuecomment-1171533075  

![pullrequest](https://2477.beast.tracing.cppalliance.org/pullrequest-b2c95455.png)  
Timeline tracing charts: [https://2477.beast.tracing.cppalliance.org/index.html](https://2477.beast.tracing.cppalliance.org/index.html)

---

## Comment 5

> Username: klemens-morgenstern  
> Created_at: 2022-10-02 14:33:52 UTC  
> Url: https://github.com/boostorg/beast/pull/2477#issuecomment-1264658187  

The 4.8 issue is fixed, merging. Clearly fixing a bunch of things, the last one can be a separate issue.

---
