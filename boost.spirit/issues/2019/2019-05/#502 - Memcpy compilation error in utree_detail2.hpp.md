# #502 - Memcpy compilation error in utree_detail2.hpp [Closed]

> Username: janos-varga  
> Created at: 2019-05-01 17:00:13 UTC  
> Updated at: 2019-05-01 17:22:57 UTC  
> Closed at: 2019-05-01 17:22:57 UTC  
> Url: https://github.com/boostorg/spirit/issues/502  

Hi Boost Community,  
Consider the following Hello World program, hello.cpp:  
```  
#include <boost/spirit/include/support_utree.hpp>  
#include <iostream>  
using namespace std;  
int main() {  
    cout << "Hello, World!";  
    return 0;  
}  
```  
When compiling this, g++ hello.cpp -o hello, we get the following compilation error (excerpt):  
```  
utree_detail2.hpp:48:14: error: 'memcpy' is not a member of 'std'  
         std::memcpy(&tmp, &buff[small_string_size-2], sizeof(tmp));  
              ^~~~~~  
```  
I propose a fix:  
include cstring and add using namespace std; just like in lexer/file_input.hpp or lexer/parser/tokeniser/re_tokeniser.hpp.

---

## Comment 1

> Username: Kojoley  
> Created at: 2019-05-01 17:11:40 UTC  
> Url: https://github.com/boostorg/spirit/issues/502#issuecomment-488345731  

1) Does including `<cstring>` before utree solves the problem?  
2) What are you compiler and standard library versions?  
  
> add using namespace std;  
  
This is not required. It was a workaround for old standard library implementations IIRC.

---

## Comment 2

> Username: janos-varga  
> Created at: 2019-05-01 17:16:02 UTC  
> Url: https://github.com/boostorg/spirit/issues/502#issuecomment-488346967  

1. Yes.  
2. My GCC and stdlib are version 8.3.0.
