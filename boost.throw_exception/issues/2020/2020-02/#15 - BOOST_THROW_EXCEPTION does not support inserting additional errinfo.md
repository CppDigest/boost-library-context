# #15 - BOOST_THROW_EXCEPTION does not support inserting additional errinfo [Closed]

> Username: sdebionne  
> Created at: 2020-02-04 13:54:46 UTC  
> Updated at: 2020-02-04 17:44:16 UTC  
> Closed at: 2020-02-04 17:44:16 UTC  
> Url: https://github.com/boostorg/throw_exception/issues/15  

AFAIU from the [documentation](https://www.boost.org/doc/libs/1_72_0/libs/exception/doc/boost_throw_exception_hpp.html) that defines BOOST_THROW_EXCEPTION as  
  
```  
#define BOOST_THROW_EXCEPTION(x)\  
        ::boost::throw_exception( ::boost::enable_error_info(x) <<\  
        ::boost::throw_function(BOOST_THROW_EXCEPTION_CURRENT_FUNCTION) <<\  
        ::boost::throw_file(__FILE__) <<\  
        ::boost::throw_line((int)__LINE__) )  
```  
  
it should be possible to write  
  
```c++  
#include <boost/exception/errinfo_errno.hpp>  
#include <boost/throw_exception.hpp>  
  
BOOST_THROW_EXCEPTION(std::runtime_error("oupsy")) << boost::errinfo_errno(res);  
```  
  
But looking at the current implementation of `BOOST_THROW_EXCEPTION`  
  
```c++  
# define BOOST_THROW_EXCEPTION(x) ::boost::exception_detail::throw_exception_(x,BOOST_THROW_EXCEPTION_CURRENT_FUNCTION,__FILE__,__LINE__)  
```  
  
it looks like it's not possible anymore.  
  
```bash  
<source>:9:70: error: no match for 'operator<<' (operand types are 'void' and 'boost::errinfo_errno' {aka 'boost::error_info<boost::errinfo_errno_, int>'})  
```  
  
Is this an oversight?

---

## Comment 1

> Username: pdimov  
> Created at: 2020-02-04 14:52:11 UTC  
> Url: https://github.com/boostorg/throw_exception/issues/15#issuecomment-581946298  

I don't see why you think that would be possible. If you substitute the definition of BOOST_THROW_EXCEPTION you quote into the expression, the result is the exact same error.

---

## Comment 2

> Username: sdebionne  
> Created at: 2020-02-04 15:31:03 UTC  
> Url: https://github.com/boostorg/throw_exception/issues/15#issuecomment-581965114  

My bad, I screw up with the parenthesis when reading the documentation, sorry. It would be nice to have though. Maybe something like this could be helpful  
  
```c++  
BOOST_THROW_EXCEPTION2(  
    std::runtime_error("oupsy"),  
    (boost::errinfo_errno(res))(boost::errinfo_file_name(filename))  
)  
```  
  
or  
  
```c++  
boost::throw_exception(BOOST_EXCEPTION(std::runtime_error("oupsy")) << boost::errinfo_errno(res)) << boost::errinfo_file_name(filename));  
```  
  
or is there any other recommended way to achieve the same goal, that is, throw a boost exception with the function, file and line infos plus additional errinfos?

---

## Comment 3

> Username: pdimov  
> Created at: 2020-02-04 16:34:18 UTC  
> Url: https://github.com/boostorg/throw_exception/issues/15#issuecomment-581996931  

The current way is  
```  
BOOST_THROW_EXCEPTION(boost::enable_error_info(std::runtime_error("oops")) << boost::errinfo_errno(res));  
```  
but this rarely occurs in practice because you typically throw your own exception type which already derives from `boost::exception` so the call to `boost::enable_error_info` is not necessary.  
  
(In 1.73, there will be changes to `throw_exception`, but they don't affect this issue. See https://www.boost.org/doc/libs/develop/libs/throw_exception/doc/html/throw_exception.html.)

---

## Comment 4

> Username: sdebionne  
> Created at: 2020-02-04 17:44:16 UTC  
> Url: https://github.com/boostorg/throw_exception/issues/15#issuecomment-582030449  

Thanks, Boost.ThrowException has everything I need in the end!
