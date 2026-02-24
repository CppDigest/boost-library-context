# #158 - Surprising move-from behavior when initializing a json::object [Closed]

> Username: anticrisis  
> Created at: 2020-08-16 05:34:32 UTC  
> Updated at: 2020-08-16 22:57:16 UTC  
> Closed at: 2020-08-16 22:57:16 UTC  
> Url: https://github.com/boostorg/json/issues/158  

Thank you for this well designed, well documented, and easy to use library! It is quite pleasant to use.   
  
I wonder if this behavior is as you designed? The documentation of 'Initializer Lists' indicates that "Elements that are rvalues will be moved upon initialization," but this program exhibits behavior that is surprising to me:  
  
```c++  
#include <boost/json.hpp>  
#include <iostream>  
  
int  
main()  
{  
  using namespace boost::json;  
  
  string s1{ "hello world" };  
  
  // obj1 will move from s1, invalidating s1  
  object obj1{ { { "s1", s1 } } };  
  
  if (s1 == "hello world")  
    std::cout << "OK\n";  
  else  
    std::cout << "FAIL\n";  
  
  //  
  
  // making s2 const inhibits the move-from  
  string const s2{ "hello world" };  
  
  object obj2{ { { "s2", s2 } } };  
  
  if (s2 == "hello world")  
    std::cout << "OK\n";  
  else  
    std::cout << "FAIL\n";  
  
  //  
  // program prints:  
  //  
  // FAIL  
  // OK  
  //  
}  
```  
  
Unless I'm mistaken, `s1` is not an rvalue, and should not be moved from. Making the variable `const` inhibits the move.  
  
I hope this is not intended behavior, because it does make it far too easy to shoot myself in the foot with use-after-move issues, especially since no `std::move` is visible in my own code. Thank you again!

---

## Comment 1

> Username: vinniefalco  
> Created at: 2020-08-16 09:28:34 UTC  
> Url: https://github.com/boostorg/json/issues/158#issuecomment-674503632  

That looks like a bug!

---

## Comment 2

> Username: anticrisis  
> Created at: 2020-08-16 19:55:47 UTC  
> Updated at: 2020-08-16 20:15:02 UTC  
> Url: https://github.com/boostorg/json/issues/158#issuecomment-674570332  

Thank you for the quick action! I'm sorry, but I should have been more complete in my report. I believe this behavior happens for all value types, not just string. For example, with the latest commit applied:  
  
```c++  
#include <boost/json.hpp>  
#include <iostream>  
  
int  
main()  
{  
  using namespace boost::json;  
  
  string s1{ "hello world" };  
  object o1{ {"a", 1}, {"b", 2} };  
  
  // obj1 will move from s1 and o1, invalidating s1 and o1  
  object obj_s1{ { { "s1", s1 } } };  
  object obj_o1{ { { "o1", o1 } } };  
  
  if (s1 == "hello world")  
    std::cout << "OK\n";  
  else  
    std::cout << "FAIL\n";  
  
  if (o1.contains("a") && o1.at("a").as_int64() == 1)  
    std::cout << "OK\n";  
  else  
    std::cout << "FAIL\n";  
  
  //  
  
  // making o2 and s2 const inhibits the move-from  
  string const s2{ "hello world" };  
  object const o2{ {"a", 1}, {"b", 2} };  
  
  object obj_s2{ { { "s2", s2 } } };  
  object obj_o2{ { { "o2", o2 } } };  
  
  if (s2 == "hello world")  
    std::cout << "OK\n";  
  else  
    std::cout << "FAIL\n";  
  
  if (o2.contains("a") && o2.at("a").as_int64() == 1)  
    std::cout << "OK\n";  
  else  
    std::cout << "FAIL\n";  
  
  //  
  // program prints:  
  //  
  // OK  
  // FAIL  
  // OK  
  // OK  
  //  
}  
```  
  
I can open a Pull Request with a failing test, now that I see how to do that from the previous commit, if it would help. I've added failing tests for `object` and `array`. But it's probably trivial enough for you to take care of it.

---

## Comment 3

> Username: vinniefalco  
> Created at: 2020-08-16 20:45:36 UTC  
> Url: https://github.com/boostorg/json/issues/158#issuecomment-674575731  

Yeah we will look at it again, thanks!

---

## Comment 4

> Username: sdkrystian  
> Created at: 2020-08-16 21:17:25 UTC  
> Updated at: 2020-08-16 21:17:41 UTC  
> Url: https://github.com/boostorg/json/issues/158#issuecomment-674579160  

Whoops... I should have verified that this was isolated to just `string`.
