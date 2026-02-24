# #137 - Nested BOOST_LOG_TRIVIAL calls override the loglevel of the outer call. [Closed]

> Username: stephanroslen  
> Created at: 2021-01-13 11:16:08 UTC  
> Updated at: 2021-01-13 11:35:49 UTC  
> Closed at: 2021-01-13 11:35:48 UTC  
> Url: https://github.com/boostorg/log/issues/137  

Using boost 1.75 (also reproduced in boost 1.74).  
  
Code:  
```cpp  
#include <string_view>  
  
#include <boost/log/trivial.hpp>  
  
std::string_view innerFunction(std::string_view sv) {  
  BOOST_LOG_TRIVIAL(warning) << "This is a warning";  
  return sv;  
}  
  
int main() {  
  BOOST_LOG_TRIVIAL(error) << innerFunction("This should be an error");  
  BOOST_LOG_TRIVIAL(error) << "This is an error";  
  return 0;  
}  
```  
  
Output is:  
```  
[2021-01-13 11:13:07.216427] [0x00007fd20ae8f740] [warning] This is a warning  
[2021-01-13 11:13:07.216466] [0x00007fd20ae8f740] [warning] This should be an error  
[2021-01-13 11:13:07.216472] [0x00007fd20ae8f740] [error]   This is an error  
```  
  
The second line seems to have its loglevel overriden by the loglevel of the first line.

---

## Comment 1

> Username: Lastique  
> Created at: 2021-01-13 11:21:25 UTC  
> Url: https://github.com/boostorg/log/issues/137#issuecomment-759384539  

Yes, this is expected behavior.

---

## Comment 2

> Username: stephanroslen  
> Created at: 2021-01-13 11:25:59 UTC  
> Url: https://github.com/boostorg/log/issues/137#issuecomment-759386686  

Is there a recommended workaround besides avoiding nested calls?

---

## Comment 3

> Username: Lastique  
> Created at: 2021-01-13 11:34:15 UTC  
> Url: https://github.com/boostorg/log/issues/137#issuecomment-759390763  

Not really. You could implement your own logger with a [`mutable_constant`](https://www.boost.org/doc/libs/1_75_0/libs/log/doc/html/log/detailed/attributes.html#log.detailed.attributes.mutable_constant) attribute used to store the severity level. You would have to use `BOOST_LOG_SEV` instead of `BOOST_LOG_TRIVIAL` to emit log records. There is an example [here](https://www.boost.org/doc/libs/1_75_0/libs/log/doc/html/log/extension/sources.html) and you can also see how [`basic_severity_logger`](https://github.com/boostorg/log/blob/1c4de7d8c69c8d59327972a1dc839721dc86d9ad/include/boost/log/sources/severity_feature.hpp#L147) is implemented.  
  
In the `basic_severity_logger`, which is used internally by `BOOST_LOG_TRIVIAL`, the severity level is saved in thread-specific storage for performance reasons. Nested log records are not really expected because you should not be calling methods that affect program behavior (which might log something) from the formatting expression.

---

## Comment 4

> Username: stephanroslen  
> Created at: 2021-01-13 11:35:48 UTC  
> Url: https://github.com/boostorg/log/issues/137#issuecomment-759391516  

Thank you!
