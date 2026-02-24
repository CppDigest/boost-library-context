# #799 - Standalone Asio compatibility [Closed]

> Username: vimpunk  
> Created at: 2017-09-28 09:29:10 UTC  
> Updated at: 2022-12-13 15:11:44 UTC  
> Closed at: 2017-10-03 11:49:06 UTC  
> Url: https://github.com/boostorg/beast/issues/799  

First and foremost, thank you for Beast, it's easy to use and so far seems to work as expected!  
  
This is not so much an issue as a feature request, namely, whether support for Asio with ASIO_STANDALONE defined can be expected? I've seen boostorg/beast#278, and I'm not asking to eliminate Boost dependencies, but simply to make using Beast with a standalone Asio instance possible.  
  
The problem appears to be `boost::system::error_code` and `std::error_code` interoperability (or lack thereof), in that Beast's Async{Read,Write}Stream requirements are not met (`asio::ip::tcp::socket` has no method that accepts a handler with a `boost::system::error_code`). Is there any easy workaround? It seems it is not so simple as replacing the `error_code` alias in `<beast/core/error.hpp>` as that generates a generous amount of errors. Or do I have no choice but to use Asio within the Boost namespace?  
  
Thanks in advance!

---

## Comment 1

> Username: vinniefalco  
> Created at: 2017-09-28 12:46:12 UTC  
> Url: https://github.com/boostorg/beast/issues/799#issuecomment-332825124  

Thanks for the kind words.  
  
Yes, I centralized the error code aliases in error.hpp precisely so it would be easy to patch Beast to work with the stand-alone Asio in the fashion you described. However, I have not actually tried it. Can you please post the error you are getting? I think we should be able to make this work. And if you don't mind putting in the effort, I can accept a pull request with the changes needed to make this support official.

---

## Comment 2

> Username: vimpunk  
> Created at: 2017-09-28 22:48:47 UTC  
> Updated at: 2017-10-02 20:50:30 UTC  
> Url: https://github.com/boostorg/beast/issues/799#issuecomment-332984705  

I'm not going to be home from tomorrow until the beginning of next week, but after that I'd be happy to work on this!  
  
The example used was `example/http/client/async/` (my use case for Beast has been HTTP all along, so that's where I started), so this is by no means an exhaustive investigation.  
  
#### Initial changes:  
- `core/error.hpp`: aliases changed from `boost::system` to `std`, `namespace errc = ...` to `using std::errc;`  
- `{http, websocket, zlib}/impl/error.ipp`: the namespace in which `is_error_code_enum` is placed changed form `boost::system` to `std`  
- similar namespace changes in the example file  
  
#### Errors:  
The above resulted in about 1500 lines of errors, so I have taken the liberty to find the common ones and summarize them here:  
  
- The first type of error occurs when `errc` values are directly assigned to an `error_code` using `error_code::assign(int, const error_category&)`, which fails to compile as `errc` is an `enum class` in C++11, rather than a traditional enum like in Boost. Example:  
```  
../../../../include/boost/beast/core/impl/file_posix.ipp: In member function 'uint64_t boost::beast::file_posix::size(boost::beast::error_code&) const':  
../../../../include/boost/beast/core/impl/file_posix.ipp:213:61: error: no matching function for call to ‘std::error_code::assign(std::errc, const std::_V2::error_category&)’  
         ec.assign(errc::invalid_argument, generic_category());  
```  
This issue appears to be solved by using `make_error_code(errc::invalid_argument)`, which finds the appropriate `error_category` for the given error code enum using ADL.  
  
- The next type of error stems from directly referring to `boost::asio::error::eof`.  
```  
../../../../include/boost/beast/http/impl/read.ipp: In member function ‘void boost::beast::http::detail::read_some_op<Stream, DynamicBuffer, isRequest, Derived, Handler>::operator()(boost::beast::error_code, std::size_t)’:  
../../../../include/boost/beast/http/impl/read.ipp:126:15: error: no match for ‘operator==’ (operand types are ‘boost::beast::error_code {aka std::error_code}’ and ‘boost::asio::error::misc_errors’)  
         if(ec == boost::asio::error::eof)  
```  
- The third error, at the very bottom of the log, is about Async{Read,Write}Stream requirements not being met. I haven't had a lot of time to look into it more thoroughly, so I'm unsure of its cause, as the `is_async_{read,write}stream` test should be correct. Example:  
```  
../../../../include/boost/beast/http/impl/write.ipp: In instantiation of ‘boost::beast::async_return_type<ReadHandler, void(std::error_code, long unsigned int)> boost::beast::http::async_write(AsyncWriteStream&, boost::beast::http::message<isRequest, Body, Fields>&, WriteHandler&&) [with AsyncWriteStream = asio::basic_stream_socket<asio::ip::tcp>; bool isRequest = true; Body = boost::beast::http::empty_body; Fields = boost::beast::http::basic_fields<std::allocator<char> >; WriteHandler = std::_Bind<void (session::*(std::shared_ptr<session>, std::_Placeholder<1>, std::_Placeholder<2>))(std::error_code, long unsigned int)>; boost::beast::async_return_type<ReadHandler, void(std::error_code, long unsigned int)> = void]’:  
http_client_async.cpp:116:39:   required from here  
../../../../include/boost/beast/http/impl/write.ipp:811:5: error: static assertion failed: AsyncWriteStream requirements not met  
     static_assert(  
```  
  
The last error terminated the compilation, but there should be more errors similar to the second one listed above, as there are more `boost::asio` references further in the execution, and this is example only uses `{read,write}.ipp` (I believe). So I think this is the biggest hurdle in supporting standalone Asio, as there are many more references to Asio via the Boost namespace throughout the codebase (a quick 'grep + wc -l' showed that there are roughly 740 'boost::asio' results, though some of them comments).  
  
I'm uncertain as to what the most viable solution would be for maintainability's sake. Perhaps a separate `asio.hpp` file with the proper includes and namespace aliases could work, which would allow the implementation to refer to Asio through `asio::` unambiguously. The problem I see with this is that it would be cumbersome to only pull in the headers one actually needs. Do you have anything in mind?  
  
EDIT: clarification. Formatting.

---

## Comment 3

> Username: vinniefalco  
> Created at: 2017-09-29 12:11:53 UTC  
> Url: https://github.com/boostorg/beast/issues/799#issuecomment-333109845  

If stand-alone Asio has a preprocessor switch to choose between `boost::system::error_code` and `std::error_code` we can start by respecting that setting in Beast. What do you think?

---

## Comment 4

> Username: vimpunk  
> Created at: 2017-10-02 20:43:17 UTC  
> Updated at: 2017-10-02 20:53:20 UTC  
> Url: https://github.com/boostorg/beast/issues/799#issuecomment-333659026  

Having done a little research, it looks like Boost.Asio (i.e. `boost::asio::`) and the standalone Asio (just `asio::`) are two separate code bases, therefore I was somewhat inaccurate in describing my request. What I really meant was for Beast to work with either Boost.Asio or Asio (I've renamed the issue in light of this). This means that the ASIO_STANDALONE flag is insufficient, as it is entirely possible to use the standalone Asio without that flag set.  
  
Which also means that your suggestion, unfortunately, won't work, as Boost.Asio solely uses `boost::system::error_code`, and Asio [uses](https://github.com/chriskohlhoff/asio/blob/master/asio/include/asio/error_code.hpp) `std::error_code`, if the ASIO_STANDALONE flag is defined, or its own implementation, but not `boost::system::error_code`, unfortunately.  
  
Moreover, the distinction between the code bases means that the Asio includes are going to have to depend on which type of Asio is used, i.e. either `<boost/asio/...>` or `<asio/...>`. I believe the most viable solution, if you still want to support this feature (given the additional complexities), is to have an Asio forward declaration header, which picks the proper includes depending on which is needed. A simplified example:  
```  
// Copyright ...  
  
#ifndef BOOST_BEAST_ASIO_HPP  
#define BOOST_BEAST_ASIO_HPP  
  
#include <boost/beast/config.hpp> // for BOOST_BEAST_IS_ASIO_STANDALONE  
  
#ifdef  BOOST_BEAST_IS_ASIO_STANDALONE  
# include <asio.hpp>  
#else  
# include <boost/asio.hpp>  
// This is so that we can refer to Asio through only the asio:: namespace everywhere. It's probably  
// not the right solution, it's just for the sake of this example.  
namespace asio = boost::asio;  
#endif  
  
#endif  
```  
  
Then, anywhere in the Beast implementation where Asio is needed, the `<boost/beast/asio.hpp>` would have to be included. Although the fact that Asio without the C++11 backing (i.e. no `std::error_code`) defines its own implementation of `error_code` introduces further complexities, since I presume Beast doesn't want to use that version of `error_code`.  
  
This feature appears to require a lot more thought than initially presumed. What are your thoughts on this?  
  
EDIT: formatting and added a link.

---

## Comment 5

> Username: vinniefalco  
> Created at: 2017-10-02 21:04:41 UTC  
> Url: https://github.com/boostorg/beast/issues/799#issuecomment-333664766  

>What are your thoughts on this?  
  
There are no plans for any version of Beast to support stand-alone Asio.

---

## Comment 6

> Username: vimpunk  
> Created at: 2017-10-03 08:31:09 UTC  
> Url: https://github.com/boostorg/beast/issues/799#issuecomment-333775524  

Oh, that is somewhat disappointing, but I completely understand. In that case, this issue can be closed, as far as I am concerned. Thanks for your time.

---

## Comment 7

> Username: vinniefalco  
> Created at: 2017-10-03 11:49:06 UTC  
> Url: https://github.com/boostorg/beast/issues/799#issuecomment-333818231  

Glad to help!

---

## Comment 8

> Username: halvors  
> Created at: 2022-06-25 13:18:25 UTC  
> Url: https://github.com/boostorg/beast/issues/799#issuecomment-1166283448  

Has there been any progress on this? Since asio is getting added to the standard library in C++23 i would love to see beast supporting the standalone asio/std::experimental::net

---

## Comment 9

> Username: vinniefalco  
> Created at: 2022-06-25 14:53:41 UTC  
> Url: https://github.com/boostorg/beast/issues/799#issuecomment-1166304568  

Asio is not getting added to C++23, in fact it was voted down in favor of some not-yet-specified networking capabilities based on "Sender/Receiver" (which I hate).

---

## Comment 10

> Username: pfeatherstone  
> Created at: 2022-12-13 10:24:49 UTC  
> Updated at: 2022-12-13 10:27:49 UTC  
> Url: https://github.com/boostorg/beast/issues/799#issuecomment-1348142920  

I don't know why the committee is so intent on unifying async so much. It doens't matter if networking uses a different async model to distributed compute, gpu or whatever. It's not the end of the world if there are two async models in the standard library. Originally they wanted io streams to be used in networking. That didn't work out but who cares, it doesn't mean io streams were a complete disaster and killed C++. It feels like the committee is driven by people who don't write day-to-day software. We just need good tools to get the job done. Asio and Beast are good examples of good tools. We could have had Asio in std like 10 years ago now. It would have been fantastic. Asio is usable for your average skilled developer too. I wonder if senders/receivers will be for library implementers or expert users only. Does that really belong in a standard library?

---

## Comment 11

> Username: vinniefalco  
> Created at: 2022-12-13 15:11:44 UTC  
> Url: https://github.com/boostorg/beast/issues/799#issuecomment-1348771167  

The C++ Standardization Committee is a dying institution whose bureaucratic structure and membership is not aligned with the needs of the wider C++ community.
