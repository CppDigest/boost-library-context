# #26 - explicit conversion constructor called implicitly in result<T> -> result<U> conversion [Closed]

> Username: strager  
> Created at: 2021-07-09 08:59:37 UTC  
> Updated at: 2021-07-10 20:44:23 UTC  
> Closed at: 2021-07-10 20:44:22 UTC  
> Url: https://github.com/boostorg/leaf/issues/26  

It looks like LEAF has a way to construct a `result<U>` from a `result<T>`. This is handy, but the conversion happens *for explicit constructors*, which I find surprising.  
  
Here's an example:  
```c++  
#include <boost/leaf.hpp>  
#include <string>  
#include <vector>  
  
boost::leaf::result<std::size_t> g() {  
  return 42;  
}  
  
boost::leaf::result<std::vector<std::string>> f() {  
  // Surprise! std::vector<std::string>::vector(std::size_t) is called.  
  return g();  
}  
  
int main() {  
  auto r = f();  
  printf("vector contains %d strings\n", (int)r->size());  
}  
```  
  
I expect the above example to fail to compile. `std::vector<std::string>::vector(std::size_t)` is an explicit constructor, so converting from a `boost::leaf::result<std::size_t>` to a `boost::leaf::result<std::vector<std::string>>` should fail.  
  
If I use `std::optional` instead of `boost::leaf::result` everywhere in the example program, compilation fails, as expected:  
```  
test.cpp: In function ‘std::optional<std::vector<std::__cxx11::basic_string<char> > > f()’:  
test.cpp:11:11: error: could not convert ‘g()’ from ‘optional<long unsigned int>’ to ‘optional<std::vector<std::__cxx11::basic_string<char> >>’  
   11 |   return g();  
      |          ~^~  
      |           |  
      |           optional<long unsigned int>  
```  
  
I like `std::optional`'s behavior here. LEAF should not call an explicit constructor when converting result types.

---

## Comment 1

> Username: zajo  
> Created at: 2021-07-10 20:44:22 UTC  
> Url: https://github.com/boostorg/leaf/issues/26#issuecomment-877701603  

I added a couple of unit tests, this is now fixed on `develop`. Currently Boost is undergoing a Beta release and merges to `master` are disallowed. Once the beta is released, I'll be able to merge and it'll be included in the 1.77 Boost release.  
  
It may be a good time to report more issues if you find them. :)
