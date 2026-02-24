# #119 - Stack use after scope [Closed]

> Username: NAThompson  
> Created at: 2019-02-25 19:16:34 UTC  
> Updated at: 2019-02-26 21:38:21 UTC  
> Closed at: 2019-02-26 21:38:21 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/119  

Code:  
  
```cpp  
#include <iostream>  
#include <boost/math/special_functions/airy.hpp>  
#include <boost/multiprecision/cpp_bin_float.hpp>  
  
using boost::multiprecision::cpp_bin_float_50;  
int main()  
{  
    auto x = boost::math::airy_ai<cpp_bin_float_50>(0.3);  
    std::cout << x << "\n";  
}  
```  
  
Compile with:  
  
```  
g++-8 --std=gnu++17 -g -O2 -fsanitize=address -fsanitize=undefined -fvisibility=hidden -Wfatal-errors reproduce.cpp  
```  
  
Error  
```  
﻿=================================================================  
==29770==ERROR: AddressSanitizer: stack-use-after-scope on address 0x7ffeef603890 at pc 0x00010066dbc9 bp 0x7ffeef603850 sp 0x7ffeef603848  
WRITE of size 8 at 0x7ffeef603890 thread T0  
    #0 0x10066dbc8 in boost::enable_if_c<(boost::multiprecision::number_category<boost::multiprecision::backends::cpp_bin_float<50u, (boost::multiprecision::backends::digit_base_type)10, void, int, 0, 0> >::value) == ((boost::multiprecision::number_category_type)1), boost::multiprecision::number<boost::multiprecision::number_category_type, (boost::multiprecision::expression_template_option)0> >::type boost::multiprecision::fabs<boost::multiprecision::backends::cpp_bin_float<50u, (boost::multiprecision::backends::digit_base_type)10, void, int, 0, 0> >(boost::enable_if_c<(boost::multiprecision::number_category<boost::multiprecision::backends::cpp_bin_float<50u, (boost::multiprecision::backends::digit_base_type)10, void, int, 0, 0> >::value) == ((boost::multiprecision::number_category_type)1), boost::multiprecision::number<boost::multiprecision::number_category_type, (boost::multiprecision::expression_template_option)0> >::type const&) cpp_int.hpp:593  
    #1 0xaaaaaaaaaaaaaaa9  (<unknown module>)  
Address 0x7ffeef603890 is located in stack of thread T0 at offset 32 in frame  
    #0 0x10066d3af in boost::enable_if_c<(boost::multiprecision::number_category<boost::multiprecision::backends::cpp_bin_float<50u, (boost::multiprecision::backends::digit_base_type)10, void, int, 0, 0> >::value) == ((boost::multiprecision::number_category_type)1), boost::multiprecision::number<boost::multiprecision::number_category_type, (boost::multiprecision::expression_template_option)0> >::type boost::multiprecision::fabs<boost::multiprecision::backends::cpp_bin_float<50u, (boost::multiprecision::backends::digit_base_type)10, void, int, 0, 0> >(boost::enable_if_c<(boost::multiprecision::number_category<boost::multiprecision::backends::cpp_bin_float<50u, (boost::multiprecision::backends::digit_base_type)10, void, int, 0, 0> >::value) == ((boost::multiprecision::number_category_type)1), boost::multiprecision::number<boost::multiprecision::number_category_type, (boost::multiprecision::expression_template_option)0> >::type const&) default_ops.hpp:3656  
  
  This frame has 1 object(s):  
    [32, 96) 'result' <== Memory access at offset 32 is inside this variable  
HINT: this may be a false positive if your program uses some custom stack unwind mechanism or swapcontext  
      (longjmp and C++ exceptions *are* supported)  
SUMMARY: AddressSanitizer: stack-use-after-scope cpp_int.hpp:593 in boost::enable_if_c<(boost::multiprecision::number_category<boost::multiprecision::backends::cpp_bin_float<50u, (boost::multiprecision::backends::digit_base_type)10, void, int, 0, 0> >::value) == ((boost::multiprecision::number_category_type)1), boost::multiprecision::number<boost::multiprecision::number_category_type, (boost::multiprecision::expression_template_option)0> >::type boost::multiprecision::fabs<boost::multiprecision::backends::cpp_bin_float<50u, (boost::multiprecision::backends::digit_base_type)10, void, int, 0, 0> >(boost::enable_if_c<(boost::multiprecision::number_category<boost::multiprecision::backends::cpp_bin_float<50u, (boost::multiprecision::backends::digit_base_type)10, void, int, 0, 0> >::value) == ((boost::multiprecision::number_category_type)1), boost::multiprecision::number<boost::multiprecision::number_category_type, (boost::multiprecision::expression_template_option)0> >::type const&)  
Shadow bytes around the buggy address:  
  0x1fffddec06c0: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  
  0x1fffddec06d0: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  
  0x1fffddec06e0: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  
  0x1fffddec06f0: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  
  0x1fffddec0700: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 f1 f1  
=>0x1fffddec0710: f1 f1[f8]f8 f8 f8 f8 f8 f8 f8 f3 f3 f3 f3 00 00  
  0x1fffddec0720: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  
  0x1fffddec0730: 00 00 00 00 f1 f1 f1 f1 01 f2 f2 f2 f2 f2 f2 f2  
  0x1fffddec0740: 01 f2 f2 f2 f2 f2 f2 f2 01 f2 f2 f2 f2 f2 f2 f2  
  0x1fffddec0750: 01 f2 f2 f2 f2 f2 f2 f2 01 f2 f2 f2 f2 f2 f2 f2  
  0x1fffddec0760: 01 f2 f2 f2 f2 f2 f2 f2 01 f2 f2 f2 f2 f2 f2 f2  
Shadow byte legend (one shadow byte represents 8 application bytes):  
  Addressable:           00  
  Partially addressable: 01 02 03 04 05 06 07  
  Heap left redzone:       fa  
  Freed heap region:       fd  
  Stack left redzone:      f1  
  Stack mid redzone:       f2  
  Stack right redzone:     f3  
  Stack after return:      f5  
  Stack use after scope:   f8  
  Global redzone:          f9  
  Global init order:       f6  
  Poisoned by user:        f7  
  Container overflow:      fc  
  Array cookie:            ac  
  Intra object redzone:    bb  
  ASan internal:           fe  
  Left alloca redzone:     ca  
  Right alloca redzone:    cb  
==29770==ABORTING  
```

---

## Comment 1

> Username: jzmaddock  
> Created at: 2019-02-26 18:33:43 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/119#issuecomment-467557846  

Here's what I've found:  
  
* The error is specific to g++-8, it does not occur with clang or g++-7.  
* The error is specific to -O2 or -O3 and doesn't occur with -O1 which makes debugging it rather hard.  
* The stack trace for the error points to `cpp_int_base::data_type::data_type(0)` which in turn comes from the default constructor for `cpp_int_base`, which is constructed for the result inside `fabs`.  I'm having a really hard time imagining how it is even possible for a _constructor_ to trigger a "stack use _after_ scope" error.  
  
For these reasons I'm leaning towards this being a GCC bug, but I don't see anything relevant on bugzilla.  In any case at this point I'm stuck as the constructor in question is so innocuous I can't see what could be wrong.  Have you found anything more?

---

## Comment 2

> Username: NAThompson  
> Created at: 2019-02-26 20:54:56 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/119#issuecomment-467609619  

I reproduced what you found. It may be that g++-8 is using the 'custom stack unwinding mechanism' referenced in the error message, and that ASAN needs to be updated. But frankly, I have no clue!

---

## Comment 3

> Username: kcc  
> Created at: 2019-02-26 21:13:01 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/119#issuecomment-467615806  

Does this happen with clang?

---

## Comment 4

> Username: NAThompson  
> Created at: 2019-02-26 21:26:58 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/119#issuecomment-467620507  

@kcc: Doesn't happen with clang. Looks like this is my bad; it is a false positive, and has been reported:  
  
https://gcc.gnu.org/bugzilla/show_bug.cgi?id=85774  
  
https://gcc.gnu.org/bugzilla/show_bug.cgi?id=81021
