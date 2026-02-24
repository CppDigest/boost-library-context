# #1750 - Document the executor's default completion token [Open]

> Username: vinniefalco  
> Created at: 2019-10-31 02:32:26 UTC  
> Updated at: 2024-06-06 05:34:19 UTC  
> Url: https://github.com/boostorg/beast/issues/1750  

NT

---

## Comment 1

> Username: madmongo1  
> Created at: 2020-04-07 15:45:54 UTC  
> Url: https://github.com/boostorg/beast/issues/1750#issuecomment-610464502  

The asio `use_awaitable.as_default_on()` example does not compile.  
  
I have reported this issue in the Asio repo.  
https://github.com/chriskohlhoff/asio/issues/477  
  
Will revisit this issue in time for the next milestone.

---

## Comment 2

> Username: vinniefalco  
> Created at: 2020-04-09 05:19:17 UTC  
> Url: https://github.com/boostorg/beast/issues/1750#issuecomment-611333684  

Yeah, I hate trying to get last-minute stuff in when it hasn't compiled for most of the development cycle. It can wait until 1.74, thanks.

---

## Comment 3

> Username: alandefreitas  
> Created at: 2022-06-18 20:06:48 UTC  
> Url: https://github.com/boostorg/beast/issues/1750#issuecomment-1159552225  

Where should this documentation go?

---

## Comment 4

> Username: vinniefalco  
> Created at: 2022-06-21 23:26:20 UTC  
> Url: https://github.com/boostorg/beast/issues/1750#issuecomment-1162457575  

Well for example `basic_stream` has a default executor:  
https://www.boost.org/doc/libs/1_79_0/libs/beast/doc/html/beast/ref/boost__beast__basic_stream.html  
  
And its async initiating functions have a default completion token:  
https://www.boost.org/doc/libs/1_79_0/libs/beast/doc/html/beast/ref/boost__beast__basic_stream/async_read_some.html  
  
We could first make sure that `as_default_on()` works (see Richard's comment above). And then maybe we could put an example code in the javadoc along with a test or two in the .cpp?
