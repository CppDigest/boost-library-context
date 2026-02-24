# #25 - CTAD bug with GCC <11 [Closed]

> Username: strager  
> Created at: 2021-07-07 00:49:48 UTC  
> Updated at: 2021-07-10 06:02:58 UTC  
> Closed at: 2021-07-10 06:02:58 UTC  
> Url: https://github.com/boostorg/leaf/issues/25  

I noticed very strange behavior from boost LEAF with GCC 9.3.0 if I use CTAD for `boost::leaf::result`.  
  
Here is a small test program ([try on Compiler Explorer](https://godbolt.org/z/rx1bh6qe1)):  
```c++  
#include <boost/leaf/result.hpp>  
#include <cstdio>  
#include <type_traits>  
  
// Minimal repro based on boost::leaf::result:  
template <class T>  
class myresult {  
  public:  
    static int init_T_with_U( T && );  
    template <class U> myresult( U && u, decltype(init_T_with_U(std::forward<U>(u))) * = nullptr );  
};  
template<> class myresult<void> { };  
  
boost::leaf::result<void> g() { return {}; }  
myresult<void> g2() { return {}; }  
  
int main() {  
  boost::leaf::result r = g();  
  // This printf doesn't happen:  
  std::printf("!!r = %d\n", (int)!!r);  
  bool temp = !!r;  
  std::printf("!!r (temp) = %d\n", (int)temp);  
  
  // This static_assert fails:  
  //static_assert(std::is_same_v<boost::leaf::result<void>, decltype(r)>);  
  
  myresult r2 = g2();  
  // This static_assert fails:  
  //static_assert(std::is_same_v<myresult<void>, decltype(r2)>);  
}  
```  
  
I think there is a GCC compiler bug. GCC somewhat silently miscompiles code. Output with GCC 9.3.0 on my machine:  
```  
!!r (temp) = 0  
```  
  
If we add the explicit template parameter (`boost::leaf::result r = g();` -> `boost::leaf::result<void> r = g();`), the code behaves as expected. Output with GCC 9.3.0 on my machine:  
```  
!!r = 1  
!!r (temp) = 1  
```  
  
With both variants, GCC 11.1.0 seems to work as expected on my machine:  
```  
!!r = 1  
!!r (temp) = 1  
```  
  
Although this is probably a GCC bug, perhaps boost LEAF can work around the bug? Or document it?

---

## Comment 1

> Username: zajo  
> Created at: 2021-07-08 04:48:15 UTC  
> Url: https://github.com/boostorg/leaf/issues/25#issuecomment-876122152  

I investigated this and you're right that particular version of GCC has a rather severe bug.  
  
Since the `result<void>` specialization derives from `result<bool>`, I refactored this to a simpler non-deriving implementation in hopes it won't hit the same bug, but the problem persisted.  
  
If this will help you personally, I can refactor the `result` template to conditionally not use a `union` internally, perhaps this will work around the bug, at the cost of lower efficiency. Let me know, otherwise we should just report the GCC bug and move on.

---

## Comment 2

> Username: strager  
> Created at: 2021-07-08 05:11:28 UTC  
> Url: https://github.com/boostorg/leaf/issues/25#issuecomment-876131983  

> Since the `result<void>` specialization derives from `result<bool>`, [...], but the problem persisted.  
>   
> [...] I can refactor the `result` template to conditionally not use a `union` internally, perhaps this will work around the bug  
  
I don't think the union or the base class is the problem. In my example program, I reproduced the `static_assert` bug with `myresult`.  
  
A workaround might be to change this SFINAE magic to something else:  
```c++  
    static int init_T_with_U( T && );  
    template <class U> result( U && u, decltype(init_T_with_U(std::forward<U>(u))) * = 0 );  
```  
  
> If this will help you personally  
  
I don't need CTAD support. I just happened to encounter this bug by accident.

---

## Comment 3

> Username: zajo  
> Created at: 2021-07-08 06:05:25 UTC  
> Url: https://github.com/boostorg/leaf/issues/25#issuecomment-876155296  

I see what you mean.

---

## Comment 4

> Username: zajo  
> Created at: 2021-07-10 06:02:58 UTC  
> Url: https://github.com/boostorg/leaf/issues/25#issuecomment-877575568  

Fixed on `develop`.
