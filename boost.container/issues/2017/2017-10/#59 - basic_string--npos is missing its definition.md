# #59 - basic_string::npos is missing its definition [Closed]

> Username: tavianator  
> Created at: 2017-10-16 19:35:42 UTC  
> Updated at: 2017-10-19 22:32:29 UTC  
> Closed at: 2017-10-19 22:32:16 UTC  
> Url: https://github.com/boostorg/container/issues/59  

Mostly this isn't noticed because it's a constant, but if you take its address (perhaps implicitly through some function that takes parameters by reference) you'll get a link-time error:  
  
```  
$ cat foo.cpp  
#include <boost/container/string.hpp>  
#include <iostream>  
  
int main() {  
  std::cout << &boost::container::string::npos << "\n";  
  return 0;  
}  
$ g++ foo.cpp  
foo.cpp:5: error: undefined reference to 'boost::container::basic_string<char, std::char_traits<char>, boost::container::new_allocator<char> >::npos'  
collect2: error: ld returned 1 exit status  
```

---

## Comment 1

> Username: igaztanaga  
> Created at: 2017-10-19 22:32:29 UTC  
> Url: https://github.com/boostorg/container/issues/59#issuecomment-338056421  

Thanks for the report!
