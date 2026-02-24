# #241 - Requesting example of integration with Boost.Fiber for post 1.70.0 [Closed]

> Username: Warchant  
> Created at: 2019-05-18 10:36:04 UTC  
> Updated at: 2020-12-30 01:04:31 UTC  
> Closed at: 2020-12-30 01:04:30 UTC  
> Url: https://github.com/boostorg/asio/issues/241  

Coroutines are natively supported by asio. With coroutines it is possible to avoid callback hell, and turn async-callback-based code to look like it is synchronous.   
Asio has examples, and some glue code for best integration. However, native compiler coroutines require C++20 and [are not available in GCC](https://gcc.gnu.org/projects/cxx-status.html#cxx2a).  
  
There is one alternative to coroutines - Boost.Fiber. This library can be used in any C++11 (or higher) project.  
Documentation of Boost.Fiber does not have native glue code for asio+fiber, but has nice tutorial [[1]](https://www.boost.org/doc/libs/1_70_0/libs/fiber/doc/html/fiber/callbacks/then_there_s____boost_asio__.html) [[2]](https://www.boost.org/doc/libs/1_70_0/libs/fiber/doc/html/fiber/integration/deeper_dive_into___boost_asio__.html) and examples [[3]](https://github.com/boostorg/fiber/tree/develop/examples/asio). Though, tutorial and example code became outdated https://github.com/boostorg/fiber/issues/201 post 1.70.0 release.  
  
This issue is a request to add glue code, which will help to easily use Boost.Fiber with Boost.Asio, as well as a request to add some examples of using asio+fiber.  
  
P.S. : I am experimenting with asio+fiber, you can check this [server handler](https://github.com/Warchant/continuable-motivational-example/blob/master/fiber.cpp#L116-L129) - to have clue how it works

---

## Comment 1

> Username: djarek  
> Created at: 2019-05-18 19:05:05 UTC  
> Url: https://github.com/boostorg/asio/issues/241#issuecomment-493699836  

I've implemented a completion token based on Boost.Context: https://github.com/djarek/ufiber/pull/7/files#diff-f542f70e89a4104c0dc28e2f83ad7f4fR242 that makes use of the features in 1.70.   
  
I don't think that Boost.Fiber exposes the `resume_with` functionality of the underlying fiber, so you might not gain much by using the ASIO facilities in 1.70.

---

## Comment 2

> Username: ghost  
> Created at: 2020-12-30 01:04:29 UTC  
> Url: https://github.com/boostorg/asio/issues/241#issuecomment-752292142  

This issue was moved by [chriskohlhoff](https://github.com/chriskohlhoff) to [chriskohlhoff/asio#692](https://github.com/chriskohlhoff/asio/issues/692).
