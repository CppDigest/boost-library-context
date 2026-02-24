# #697 - Range for loop over header copies the fields::value_type incorrectly [Closed]

> Username: arun11299  
> Created at: 2017-07-29 07:41:37 UTC  
> Updated at: 2017-07-29 19:50:55 UTC  
> Closed at: 2017-07-29 19:50:55 UTC  
> Url: https://github.com/boostorg/beast/issues/697  

Beast version :- 90  
Branch :- master  
  
Code which reproduces the issue:  
```  
#include <iostream>  
#include "beast/http/message.hpp"  
using namespace boost;  
  
void headers_test_works()  
{  
  beast::http::request_header<> h;  
  h.set("a", "b");  
  auto h2 = std::move(h);  
  
  for (auto& e : h2) {  
    std::cout << e.name_string() << '\n';  
  }  
}  
  
void headers_test_does_not_work()  
{  
  beast::http::request_header<> h;  
  h.set("a", "b");  
  auto h2 = std::move(h);  
  
  for (auto e : h2) {  
    std::cout << e.name_string() << '\n';  
  }  
}  
  
int main() {  
  headers_test_works();  
  headers_test_does_not_work();  
  return 0;  
}  
```  
  
Output:  
  
> ArunMu:field-copy amuralid$ ./test_params  
> a  
> �  
  
Compiled as:  
  
> g++ -I beast/include/ -I beast/include/boost  -std=c++14 -o test_params test_params.cc -lboost_system  
  
If the `basic_fileds::value_type` is not meant to be copyable, then I would like to get a compiler error by deleting its copy constructor. Else, if the intention for it to be is to model regular type, then the copy constructor needs to be implemented correctly.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2017-07-29 10:16:28 UTC  
> Url: https://github.com/boostorg/beast/issues/697#issuecomment-318818961  

Yes, you are right! It seems I forgot to delete the copy members, the `basic_fields::value_type` is not copyable because it doesn't own its strings.

---

## Comment 2

> Username: vinniefalco  
> Created at: 2017-07-29 10:55:55 UTC  
> Url: https://github.com/boostorg/beast/issues/697#issuecomment-318820915  

The fix will go into **version 92**, here https://github.com/boostorg/beast/pull/698

---

## Comment 3

> Username: arun11299  
> Created at: 2017-07-29 11:59:46 UTC  
> Url: https://github.com/boostorg/beast/issues/697#issuecomment-318825968  

Cool, thanks.   
Amazing library! Thanks for all your efforts.
