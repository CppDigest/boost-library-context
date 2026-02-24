# #14 - Should bool shl_input_streamable(InputStreamable& input) take a const reference? [Closed]

> Username: snarkmaster  
> Created at: 2015-04-20 01:22:27 UTC  
> Updated at: 2015-04-20 01:41:59 UTC  
> Closed at: 2015-04-20 01:41:59 UTC  
> Url: https://github.com/boostorg/lexical_cast/issues/14  

I'm referring to this line:  
https://github.com/boostorg/lexical_cast/blob/c4fcfc33afcc4069c0fbc3b88e0578989311466b/include/boost/lexical_cast/detail/converter_lexical_streams.hpp#L224  
  
I have an enum class, for which I defined a simple ostream operator:  
  
```  
std::ostream& operator<<(std::ostream& os, const FileExists& e) {  
  os << enumName(e);      
  return os;  
}  
```  
  
Using an ostream directly works fine, e.g.  
  
```  
std::ostrstream s;  
s << FileExists::FAIL;  
```  
  
However, boost::lexical_cast fails:  
  
```  
boost::lexical_cast<std::string>(FileExists::FAIL);  
```  
  
The error is useless: cannot bind ‘std::basic_ostream<char>’ lvalue to ‘std::basic_ostream<char>&&’  
  
However, it's easy enough to reproduce:  
  
```  
template <typename T>  
bool willFail(T& e) {  
  std::ostrstream s;  
  return !(s << e).fail();  
}  
```  
  
The error is now pretty obvious: invalid initialization of non-const reference of type ‘FileExists&’ from an rvalue of type ‘FileExists’.  
  
Making `shl_input_streamable` take a const reference would fix this, but I'm not sure if it breaks anything else.

---

## Comment 1

> Username: snarkmaster  
> Created at: 2015-04-20 01:31:35 UTC  
> Url: https://github.com/boostorg/lexical_cast/issues/14#issuecomment-94332974  

Uh-oh, something very weird is going on, and I'm backpedaling on what I wrote above. Hold on.

---

## Comment 2

> Username: snarkmaster  
> Created at: 2015-04-20 01:41:59 UTC  
> Url: https://github.com/boostorg/lexical_cast/issues/14#issuecomment-94333653  

Okay, I have (re)discovered that ostream/istream operators are supposed to be declared in the namespace of the right operand. This works:  
  
```  
#include <boost/lexical_cast.hpp>  
#include <iostream>  
#include <strstream>  
  
namespace foo { namespace bar {  
enum class FileExists {  
  FAIL = 1,  
  FOO = 2,  
};  
std::ostream& operator<<(std::ostream& os, const foo::bar::FileExists& e) {  
  os << "XXX";  
  return os;  
}  
}}  
  
int main() {  
  std::ostrstream s;  
  s << foo::bar::FileExists::FAIL;  
  std::cout << boost::lexical_cast<std::string>(foo::bar::FileExists::FAIL);  
  return 0;  
}  
```  
  
Putting the `operator<<` definition **outside** the namespace does not. C++ finds a way to punch you every day.
