# #539 - Can't compile with boost [Closed]

> Username: AnthonySabbagh  
> Created at: 2021-03-25 16:03:21 UTC  
> Updated at: 2021-03-26 14:03:01 UTC  
> Closed at: 2021-03-26 14:03:01 UTC  
> Url: https://github.com/boostorg/json/issues/539  

### Version of Boost  
  
1_75  
  
### Steps necessary to reproduce the problem  
  
json.cpp  
```  
#include <boost/json.hpp>  
  
int main(){  
    boost::json::parser p;  
    return 0;  
}  
  
```  
If I try to compile this with g++ -std=c++11 -o json json.cpp, I get the following errors:  
```  
/usr/bin/ld: /tmp/cc8D7xCy.o: in function `boost::json::detail::handler::~handler()':  
json.cpp:(.text._ZN5boost4json6detail7handlerD2Ev[_ZN5boost4json6detail7handlerD5Ev]+0x18): undefined reference to `boost::json::value_stack::~value_stack()'  
/usr/bin/ld: /tmp/cc8D7xCy.o: in function `boost::json::basic_parser<boost::json::detail::handler>::~basic_parser()':  
json.cpp:(.text._ZN5boost4json12basic_parserINS0_6detail7handlerEED2Ev[_ZN5boost4json12basic_parserINS0_6detail7handlerEED5Ev]+0x1c): undefined reference to `boost::json::detail::stack::~stack()'  
/usr/bin/ld: /tmp/cc8D7xCy.o: in function `boost::json::parser::parser()':  
json.cpp:(.text._ZN5boost4json6parserC2Ev[_ZN5boost4json6parserC5Ev]+0x5e): undefined reference to `boost::json::parser::parser(boost::json::storage_ptr, boost::json::parse_options const&)'  
collect2: error: ld returned 1 exit status  
```  
### All relevant compiler information  
  
gcc version 9.3.0 (Ubuntu 9.3.0-17ubuntu1~20.04)   
  
I have boost 1.75.0 in my usr/include folder. I am able to use the beast library with no issue, but I can't compile programs that use the json library. What am I doing wrong?

---

## Comment 1

> Username: beached  
> Created at: 2021-03-25 16:10:05 UTC  
> Url: https://github.com/boostorg/json/issues/539#issuecomment-807018173  

I think you need to add -Lboost_json to link to boost json or include #include <boost/json/src.hpp> also so that it's header only

---

## Comment 2

> Username: vinniefalco  
> Created at: 2021-03-25 16:25:12 UTC  
> Url: https://github.com/boostorg/json/issues/539#issuecomment-807044317  

That's a link error, not a compiler error.

---

## Comment 3

> Username: grisumbras  
> Created at: 2021-03-25 17:16:33 UTC  
> Url: https://github.com/boostorg/json/issues/539#issuecomment-807124761  

Since you do not link to the library, this is header-only usage variant. Which is documented [here](https://www.boost.org/doc/libs/1_75_0/libs/json/doc/html/json/overview.html#json.overview.requirements.header_only). In other words, see @beached's comment above.

---

## Comment 4

> Username: AnthonySabbagh  
> Created at: 2021-03-25 19:31:48 UTC  
> Url: https://github.com/boostorg/json/issues/539#issuecomment-807339329  

Thanks guys. I got the header only option to work. I am quite a noob with linking. IDK if this is the best place to ask, but how would I get the other option working (linking to boost json). I still get the same linking errors when I try to compile with the linker option -Lboost_json. I'm guessing I have to compile something first (which I didn't do)?

---

## Comment 5

> Username: vinniefalco  
> Created at: 2021-03-25 19:48:03 UTC  
> Url: https://github.com/boostorg/json/issues/539#issuecomment-807364372  

Have you ever linked a library before?

---

## Comment 6

> Username: AnthonySabbagh  
> Created at: 2021-03-26 14:03:01 UTC  
> Url: https://github.com/boostorg/json/issues/539#issuecomment-808245040  

Only really simple stuff for learning. I think I understand it now after looking more into it.
