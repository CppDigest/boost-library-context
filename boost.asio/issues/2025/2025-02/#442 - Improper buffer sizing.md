# #442 - Improper buffer sizing [Closed]

> Username: maliberty  
> Created at: 2025-02-23 06:08:57 UTC  
> Updated at: 2025-09-09 21:47:53 UTC  
> Closed at: 2025-09-09 21:47:53 UTC  
> Url: https://github.com/boostorg/asio/issues/442  

In commit https://github.com/boostorg/asio/commit/bbda6205905bc74344c1e846dd93fa8fb24c229b the follow code was introduced in include/boost/asio/impl/config.hpp  
  
```  
    char buf[std::numeric_limits<long long>::max_digits10 + 1];  
    if (const char* str = service.get_value(section, key, buf, sizeof(buf)))  
    {  
      char* end = nullptr;  
      long long result = std::strtoll(str, &end, 0);  
      if (errno == ERANGE || result < (std::numeric_limits<T>::min)()  
          || result > (std::numeric_limits<T>::max)())  
        detail::throw_exception(std::out_of_range("config out of range"));  
      return static_cast<T>(result);  
    }  
```  
  
According to https://en.cppreference.com/w/cpp/types/numeric_limits/max_digits10 the values for `std::numeric_limits<long long>::max_digits10` is 0 so the buffer is size 1.  The call to get_value then returns an empty string leading to an exception being thrown.    
  
I don't see how this code could work.  In my program this is triggered by a simple `asio::thread_pool pa_pool(1);` statement while using 1.87.0.

---

## Comment 1

> Username: hzeller  
> Created at: 2025-03-04 23:14:18 UTC  
> Url: https://github.com/boostorg/asio/issues/442#issuecomment-2699210631  

Looks like this has been fixed in acbf6b48f66ac60b31eda6cd26190e2ba10ace5e by @chriskohlhoff

---

## Comment 2

> Username: maliberty  
> Created at: 2025-03-04 23:41:57 UTC  
> Url: https://github.com/boostorg/asio/issues/442#issuecomment-2699271764  

Great.  It would be nice to add a test for this code.

---

## Comment 3

> Username: psalinge  
> Created at: 2025-03-07 06:14:04 UTC  
> Url: https://github.com/boostorg/asio/issues/442#issuecomment-2705619468  

Report in sibling repo: https://github.com/chriskohlhoff/asio/issues/1587
