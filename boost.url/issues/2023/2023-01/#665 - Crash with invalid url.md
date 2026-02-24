# #665 - Crash with invalid url [Closed]

> Username: JohannesGajdosikPKE  
> Created at: 2023-01-19 14:55:59 UTC  
> Updated at: 2023-01-24 23:29:35 UTC  
> Closed at: 2023-01-24 23:29:35 UTC  
> Url: https://github.com/boostorg/url/issues/665  

The following Program reproduces a crash:   
  
```console  
a.out: /usr/local/include/boost/url/detail/path.hpp:82: std::size_t boost::urls::detail::path_segments(boost::urls::string_view, std::size_t): Assertion `nseg == 0' failed.  
Aborted  
```  
  
```cpp  
#include <boost/url/src.hpp>  
#include <iostream>  
int main(void) {  
  try {  
    boost::urls::url_view u("A:\\");  
  } catch (...) {  
    std::cout << "exception caught" << std::endl;  
  }  
  std::cout << "bye." << std::endl;  
  return 0;  
}  
  
```

---

## Comment 1

> Username: JohannesGajdosikPKE  
> Created at: 2023-01-19 14:57:13 UTC  
> Url: https://github.com/boostorg/url/issues/665#issuecomment-1397105639  

Pasting the code did not work correctly: it should be  
#include <iostream>

---

## Comment 2

> Username: JohannesGajdosikPKE  
> Created at: 2023-01-19 14:58:02 UTC  
> Url: https://github.com/boostorg/url/issues/665#issuecomment-1397106905  

Angain didi not work.  
#include less iostream greater

---

## Comment 3

> Username: JohannesGajdosikPKE  
> Created at: 2023-01-19 14:59:38 UTC  
> Updated at: 2023-01-19 15:04:03 UTC  
> Url: https://github.com/boostorg/url/issues/665#issuecomment-1397109119  

Also the url string cannot be pasted. It is  
"A: backslash backslash" without any spaces, just 2 backslashes

---

## Comment 4

> Username: vinniefalco  
> Created at: 2023-01-19 15:02:45 UTC  
> Url: https://github.com/boostorg/url/issues/665#issuecomment-1397113753  

Confirmed: https://godbolt.org/z/EYfThE73T  
And: https://godbolt.org/z/dTq763nh3
