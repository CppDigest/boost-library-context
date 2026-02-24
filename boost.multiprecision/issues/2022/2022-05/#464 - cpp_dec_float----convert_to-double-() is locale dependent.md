# #464 - cpp_dec_float<>::convert_to<double>() is locale dependent [Closed]

> Username: StefanBruens  
> Created at: 2022-05-28 14:32:33 UTC  
> Updated at: 2022-06-01 04:09:54 UTC  
> Closed at: 2022-06-01 04:08:58 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/464  

When a locale is used which does not use '.' as decimal point, `convert_to<double>` fails:  
  
```  
#include <boost/multiprecision/cpp_dec_float.hpp>  
#include <iostream>  
#include <locale>  
  
int main (int argc, char* argv[])  
{  
  auto a = boost::multiprecision::cpp_dec_float_50{12345};  
  
  std::cout << std::scientific << std::setprecision(8);  
  double d1 = a.convert_to<double>();  
  std::cout << "d1 = " << d1 << std::endl;  
  
  std::locale::global(std::locale("de_DE"));  
  double d2 = a.convert_to<double>();  
  std::cout << "d2 = " << d2 << std::endl;  
  
  return 0;  
}  
```  
```  
g++ -o boost_mp boost_mp.cpp -g3 && ./boost_mp   
d1 = 1.23450000e+04  
d2 = 1.00000000e+00  
```  
  
This is probably a regression due to:  
https://github.com/boostorg/multiprecision/commit/93d7e83d72e33e19183ff51fd65d56ba5c70860d  
  
The intermediate string always uses a '.' as decimal point:  
https://github.com/boostorg/multiprecision/blob/37f1c76375915493cf43621b333549671809071b/include/boost/multiprecision/detail/number_base.hpp#L1479  
  
but it is parsed in a locale dependent way, with std::strtod:  
https://github.com/boostorg/multiprecision/blob/37f1c76375915493cf43621b333549671809071b/include/boost/multiprecision/cpp_dec_float.hpp#L1606

---

## Comment 1

> Username: mborland  
> Created at: 2022-05-28 15:37:36 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/464#issuecomment-1140285118  

@jzmaddock and @ckormanyos I am pretty sure I broke this removing Boost.lexical_cast and string streaming. Investigating.

---

## Comment 2

> Username: ckormanyos  
> Created at: 2022-05-28 16:26:09 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/464#issuecomment-1140292698  

Thanks for looking into this Matt. I'm not entirely sure, but unfortunately, it looks like GCC5 on bionic is having [difficulty](https://github.com/boostorg/multiprecision/runs/6637778256?check_suite_focus=true#step:17:433) with locales.  
  
No problem from my side to disable those tests/functionalities locally for `(#defined (__GNUC__) && (__GNUC__ < 6))`. I don't know if this helps?

---

## Comment 3

> Username: StefanBruens  
> Created at: 2022-05-28 16:32:31 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/464#issuecomment-1140293553  

> Thanks for looking into this Matt. I'm not entirely sure, but unfortunately, it looks like GCC5 on bionic is having [difficulty](https://github.com/boostorg/multiprecision/runs/6637778256?check_suite_focus=true#step:17:433) with locales.  
>   
> No problem from my side to disable those tests/functionalities locally for `(#defined (__GNUC__) && (__GNUC__ < 6))`. I don't know if this helps?  
  
Does the CI host have any locales beyond 'C'?

---

## Comment 4

> Username: mborland  
> Created at: 2022-05-28 16:51:43 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/464#issuecomment-1140296211  

> > Thanks for looking into this Matt. I'm not entirely sure, but unfortunately, it looks like GCC5 on bionic is having [difficulty](https://github.com/boostorg/multiprecision/runs/6637778256?check_suite_focus=true#step:17:433) with locales.  
> > No problem from my side to disable those tests/functionalities locally for `(#defined (__GNUC__) && (__GNUC__ < 6))`. I don't know if this helps?  
>   
> Does the CI host have any locales beyond 'C'?  
  
It looks like the answer to that is no. Can you try the linked PR to make sure your problem is solved, and it's not an instance of works on my machine?

---

## Comment 5

> Username: StefanBruens  
> Created at: 2022-05-29 12:59:06 UTC  
> Updated at: 2022-05-29 14:55:39 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/464#issuecomment-1140444337  

> It looks like the answer to that is no. Can you try the linked PR to make sure your problem is solved, and it's not an instance of works on my machine?  
  
Cherry-picked into 1.79, build currently running.  
  
Update: Dependent package rebuilt, now works with both en_US and de_DE.

---

## Comment 6

> Username: StefanBruens  
> Created at: 2022-05-29 13:03:43 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/464#issuecomment-1140445095  

Too sad we can't just use [std::from_chars](https://en.cppreference.com/w/cpp/utility/from_chars) here (C++17). This would allow to do some error checking (the old code should have checked the *endptr).

---

## Comment 7

> Username: ckormanyos  
> Created at: 2022-06-01 04:08:58 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/464#issuecomment-1143095311  

Fixed by 6119ef16d682278d57a80b0ce7b0488160847ecf

---

## Comment 8

> Username: ckormanyos  
> Created at: 2022-06-01 04:09:28 UTC  
> Updated at: 2022-06-01 04:09:54 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/464#issuecomment-1143095560  

Thank you Stefan (@StefanBruens) for this report. Thanks Matt (@mborland) for fixing it.
