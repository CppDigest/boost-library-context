# #15 Include <boost/functional/hash.hpp> if required [Merged]

> Username: ecatmur  
> Created at: 2017-02-16 16:24:55 UTC  
> Updated at: 2017-05-12 20:00:42 UTC  
> Merged at: 2017-05-06 07:26:31 UTC  
> Closed at: 2017-05-06 07:26:31 UTC  
> Url: https://github.com/boostorg/type_index/pull/15  

With gcc 6.3.0, the following program fails to link:  
```  
#include <boost/type_index.hpp>  
int main() {  
    return boost::typeindex::type_id<int>().hash_code();  
}  
In function `main':  
main.cpp:(.text.startup+0x23): undefined reference to `unsigned long boost::hash_range<char const*>(char const*, char const*)'  
collect2: error: ld returned 1 exit status  
```  
Example: http://coliru.stacked-crooked.com/a/0b04de325c6f6529  
  
Workaround is to `#include <boost/functional/hash.hpp>` before/after including Boost.TypeIndex headers.

---

## Comment 1

> Username: apolukhin  
> Created_at: 2017-02-16 20:02:39 UTC  
> Url: https://github.com/boostorg/type_index/pull/15#issuecomment-280442500  

Yes, that's a right and good fix.  
  
But `include/boost/type_index/ctti_type_index.hpp` header must also include `boost/functional/hash.hpp`. Docs must be also fixed (examples shall not include `boost/functional/hash.hpp`, reference to `boost/type_index/type_index_facade.hpp` must not have note on inclusion of that header).

---

## Comment 2

> Username: ecatmur  
> Created_at: 2017-02-16 22:18:02 UTC  
> Url: https://github.com/boostorg/type_index/pull/15#issuecomment-280480355  

Thanks! I've updated the `boost/type_index/type_index_facade.hpp` to say that the derived class header must include `boost/functional/hash.hpp` unless the derived class redefines `hash_code()` to some other mechanism.

---

## Comment 3

> Username: apolukhin  
> Created_at: 2017-05-06 13:00:49 UTC  
> Url: https://github.com/boostorg/type_index/pull/15#issuecomment-299638562  

Thanks a lot for fixing the issue!

---

## Comment 4

> Username: 1100101  
> Created_at: 2017-05-12 09:30:54 UTC  
> Url: https://github.com/boostorg/type_index/pull/15#issuecomment-301030126  

I have a question:  
In file [type_index/stl_type_index.hpp on line 181](https://github.com/boostorg/type_index/blob/develop/include/boost/type_index/stl_type_index.hpp#L181) why is the second variant (using `boost::has_range`) used for any gcc version **>= 5.0+**?  
Shouldn't the compiler version check be adapted to also use the first path for gcc5 and newer, since `std::type_info::hash_code()` is available with C++11?

---

## Comment 5

> Username: ecatmur  
> Created_at: 2017-05-12 20:00:41 UTC  
> Url: https://github.com/boostorg/type_index/pull/15#issuecomment-301172169  

Hi Frank,  
Yes, that looks incorrect. However, later versions of g++ (and other compilers) declare C++11 support with `__cplusplus >= 201103L` rather than `__GXX_EXPERIMENTAL_CXX0X__` so the correct fix would be to add that comparison, there and at line 43. I'd suggest filing a separate issue or preferably pull request.

---
