# #475 - subchroma_image_view methods return reference to local temporary object [Closed]

> Username: mloskot  
> Created at: 2020-04-06 21:40:42 UTC  
> Updated at: 2021-02-02 22:50:59 UTC  
> Closed at: 2021-02-02 22:50:59 UTC  
> Url: https://github.com/boostorg/gil/issues/475  

### Actual behavior  
  
<!-- A clear description of what happens -->  
  
```  
../../boost/gil/extension/toolbox/image_types/subchroma_image.hpp:248:49: warning: returning reference to local temporary object [-Wreturn-stack-address]  
    const point_t& v_ssfactors() const { return point_t( get_deref_fn().vx_ssfactor(), get_deref_fn().vx_ssfactor() ); }  
                                                ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
```  
  
```  
../../boost/gil/extension/toolbox/image_types/subchroma_image.hpp:249:49: warning: returning reference to local temporary object [-Wreturn-stack-address]  
    const point_t& u_ssfactors() const { return point_t( get_deref_fn().ux_ssfactor(), get_deref_fn().ux_ssfactor() ); }  
                                                ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
```  
  
  
### Expected  behavior  
  
<!-- A clear and concise description of what you expected to happen. -->  
  
### C++ Minimal Working Example  
  
<!-- C++ program or snippet that can be compiled and used to reproduce your problem -->  
  
```cpp  
$ cat test.cpp  
#include <boost/gil/extension/toolbox//image_types/subchroma_image.hpp>  
int main() {}  
  
$ clang++-10 -std=c++11 -I/mnt/d/boost.wsl test.cpp  
```  
  
### Environment  
  
<!-- Any details that may help GIL maintainers help you -->  
  
- Version (Git ref or `<boost/version.hpp>`): 1.72 and current `develop` branch  
- Compiler: clang 3.9, 6, 7, ...

---

## Comment 1

> Username: Scramjet911  
> Created at: 2021-01-06 13:22:04 UTC  
> Url: https://github.com/boostorg/gil/issues/475#issuecomment-755295825  

Is this still an issue? I couldn't reproduce it on g++ 9.3 and c++11, all warnings enabled.

---

## Comment 2

> Username: mloskot  
> Created at: 2021-01-08 14:17:03 UTC  
> Url: https://github.com/boostorg/gil/issues/475#issuecomment-756777755  

> Is this still an issue?  
  
The code in question has not changed.  
  
> I couldn't reproduce it on g++ 9.3  
  
As given in the issue description, I reproduced it with clang.

---

## Comment 3

> Username: sdebionne  
> Created at: 2021-01-08 15:28:02 UTC  
> Url: https://github.com/boostorg/gil/issues/475#issuecomment-756815984  

Isn't the fix as simple as returning by value instead of by reference?
