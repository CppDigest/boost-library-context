# #73 - Forward constructors in boost::optional [Closed]

> Username: thegreathir  
> Created at: 2019-04-17 12:55:35 UTC  
> Updated at: 2019-04-24 07:52:50 UTC  
> Closed at: 2019-04-24 07:52:50 UTC  
> Url: https://github.com/boostorg/optional/issues/73  

I wasn't able to construct `optional<string>` from `cons char *` implicitly.  
  
```c++  
#include <string>  
#include <boost/optional.hpp>  
  
void f (const boost::optional<std::string>& a) {  
}  
  
int main() {  
    f("amir");  
}  
  
```  
  
The following compilation error apears:  
```  
opt.cpp: In function ‘int main()’:  
opt.cpp:8:13: error: invalid initialization of reference of type ‘const boost::optional<std::__cxx11::basic_string<char> >&’ from expression of type ‘const char*’  
     f("amir");  
             ^  
opt.cpp:4:6: note: in passing argument 1 of ‘void f(const boost::optional<std::__cxx11::basic_string<char> >&)’  
 void f (const boost::optional<std::string>& a) {  
  
```  
  
I was wondering whether there is any way to fix it or it's a bug?

---

## Comment 1

> Username: akrzemi1  
> Created at: 2019-04-17 15:31:49 UTC  
> Updated at: 2019-04-17 15:32:01 UTC  
> Url: https://github.com/boostorg/optional/issues/73#issuecomment-484141033  

Unlike `std::optional` `boost::optional` does not offer the conversion from `U` to `optional<T>`. See synopsis: https://www.boost.org/doc/libs/1_70_0/libs/optional/doc/html/boost_optional/reference/header__boost_optional_optional_hpp_/header_optional_optional_values.html#reference_operator_template  
  
Such conversion, although attractive, contains certain gotchas that I would rather protect the users from.

---

## Comment 2

> Username: thegreathir  
> Created at: 2019-04-18 09:58:55 UTC  
> Url: https://github.com/boostorg/optional/issues/73#issuecomment-484434580  

Isn't there any other way to fix this issue?

---

## Comment 3

> Username: akrzemi1  
> Created at: 2019-04-18 13:39:07 UTC  
> Url: https://github.com/boostorg/optional/issues/73#issuecomment-484512856  

You require a double conversion: first from `const char *` to `std::string and then from `std::string` to `optional<std::string>`. This is not generally expected for such double user-conversion to work, so I do not consider this an issue. It is just that your expectations are not compatible with the type's interface.   
  
You could try changing the calling code:  
  
```c++  
int main() {  
    f(boost::optional<std::string>{"amir"});  
}  
```  
  
or  
  
```c++  
int main() {  
    using namespace std::string_literals;  
    f("amir"s);  
}  
```
