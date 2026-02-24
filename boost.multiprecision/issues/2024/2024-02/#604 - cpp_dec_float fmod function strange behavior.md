# #604 - cpp_dec_float fmod function strange behavior [Closed]

> Username: ser90368141  
> Created at: 2024-02-29 13:31:50 UTC  
> Updated at: 2024-03-02 10:16:16 UTC  
> Closed at: 2024-03-01 13:11:34 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/604  

Hi guys!  
I used cpp_dec_float type in my project and found some strange behavior of fmod function  
`using namespace boost::multiprecision;`  
`const auto val1 = cpp_dec_float_50{ "6" };`  
`const auto val2 = cpp_dec_float_50{ "3" };`  
`std::cout << std::setprecision(std::numeric_limits<cpp_dec_float_50>::max_digits10);`  
`std::cout << "Val1: " << val1 << std::endl;`  
`std::cout << "Val2: " << val2 << std::endl;`  
`std::cout << "fmod(Val1, Val2): " << boost::multiprecision::fmod(val1, val2) << std::endl;`  
Result:  
`Val1: 6`  
`Val2: 3`  
`fmod(Val1, Val2): 3`  
  
Is this expected behavior? How do I get 0 in this case?  
Thanks!

---

## Comment 1

> Username: jzmaddock  
> Created at: 2024-02-29 17:41:27 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/604#issuecomment-1971640954  

Confirmed as a bug - it's ultimately down to cpp_dec_float not doing correct rounding, but it's still a bug, working on a fix now...

---

## Comment 2

> Username: ckormanyos  
> Created at: 2024-02-29 18:46:53 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/604#issuecomment-1971746592  

> it's ultimately down to `cpp_dec_float` not doing correct rounding, but it's still a bug, working on a fix now  
  
Thank you John. Thanks for looking into this. I was actually kind of lost on how to address this issue.  
  
Hi @ser90368141 it is unfortunate, but known, that `cpp_dec_float` does not round. This has led to several reports of unexpected behavior, such as this [detailed issue](https://github.com/boostorg/multiprecision/issues/368).  
  
This has been particularly troublesome when using pure integer arguments. As it turns out, the _not_ _rounding_ part of `cpp_dec_float` often leads to result such as $x.99999999999{\ldots}$ or similar, which just aren't quite integral. Then they get internally truncated, or rounded down.  
  
So far, I/we have not yet been able to implement efficient, correct base-10 rounding. But it is on the _very_ long-term list of things to do.  
  
The other backends do round, such as `cpp_bin_float`.

---

## Comment 3

> Username: ckormanyos  
> Created at: 2024-02-29 18:49:13 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/604#issuecomment-1971753382  

See also #368

---

## Comment 4

> Username: ser90368141  
> Created at: 2024-03-01 06:18:06 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/604#issuecomment-1972581125  

Hi @ckormanyos, @jzmaddock!  
Thanks for the quick response!  
It's a pity that rounding errors occur with this type. I use this library instead of the standard double to avoid accumulation of rounding errors due to the binary representation of the number.  
I noticed that the problem is most often observed when dividing by 3 without remainder. For example, 6 / 3 gives the result 1.999999999999...   
This also causes problems with the trunc function from the result of division. For example, trunc(6 / 3) gives the result 1 (instead of the expected 2)  
Thanks!

---

## Comment 5

> Username: ckormanyos  
> Created at: 2024-03-01 06:26:19 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/604#issuecomment-1972588127  

> Thanks for the quick response!  
> It's a pity that rounding errors occur with this type. I use this library instead of the standard double to avoid accumulation of rounding errors due to the binary representation of the number.  
I noticed that the problem is most often observed when dividing by 3 without remainder. For example, 6 / 3 gives the result $1.999999999999{\ldots}$  
  
Indeed.  
  
I did, however, also try the example on the develop branch using the `cpp_bin_float` type. And it seems to behave as expected. The code is below.  
  
If you don't require base-10 (some clients do, some don't), then you could simply use `cpp_bin_float`.  
  
```  
#include <boost/multiprecision/cpp_bin_float.hpp>  
  
#include <iomanip>  
#include <iostream>  
  
using namespace boost::multiprecision;  
  
auto main() -> int  
{  
  const auto val1 = cpp_bin_float_50{ "6" };  
  const auto val2 = cpp_bin_float_50{ "3" };  
  std::cout << std::setprecision(std::numeric_limits<cpp_bin_float_50>::max_digits10);  
  std::cout << "Val1: " << val1 << std::endl;  
  std::cout << "Val2: " << val2 << std::endl;  
  std::cout << "fmod(Val1, Val2): " << boost::multiprecision::fmod(val1, val2) << std::endl;  
}  
```

---

## Comment 6

> Username: mborland  
> Created at: 2024-03-01 07:26:03 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/604#issuecomment-1972661972  

> Hi @ckormanyos, @jzmaddock! Thanks for the quick response! It's a pity that rounding errors occur with this type. I use this library instead of the standard double to avoid accumulation of rounding errors due to the binary representation of the number. I noticed that the problem is most often observed when dividing by 3 without remainder. For example, 6 / 3 gives the result 1.999999999999... This also causes problems with the trunc function from the result of division. For example, trunc(6 / 3) gives the result 1 (instead of the expected 2) Thanks!  
  
If you need a decimal floating point type, and only need the precision of a double @ckormanyos and I are working on: https://github.com/cppalliance/decimal. It provides the fixed width decimal types specified in IEEE 754.

---

## Comment 7

> Username: ckormanyos  
> Created at: 2024-03-02 07:35:18 UTC  
> Updated at: 2024-03-02 07:35:59 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/604#issuecomment-1974665021  

Hi @ser90368141   
  
I've tried John's fix on the develop branch and the results are now as expected.  
  
Thanks @jzmaddock  
  
I wrote new sample code just for the record. I like using `et_off` when debuggung. And since I wanted to step through John's new fix in the debugger, I did that in the revised sample code. See below.  
  
```  
#include <boost/multiprecision/cpp_dec_float.hpp>  
  
#include <iomanip>  
#include <iostream>  
  
auto main() -> int  
{  
  using local_mp_type = boost::multiprecision::number<boost::multiprecision::cpp_dec_float<50>, boost::multiprecision::et_off>;  
  
  const auto val1    = local_mp_type{ "6" };  
  const auto val2    = local_mp_type{ "3" };  
  const auto val_mod = fmod(val1, val2);  
  
  const auto flg = std::cout.flags();  
  
  std::cout << std::setprecision(std::numeric_limits<local_mp_type>::max_digits10);  
  
  std::cout << "Val1: "             << val1    << std::endl;  
  std::cout << "Val2: "             << val2    << std::endl;  
  std::cout << "fmod(Val1, Val2): " << val_mod << std::endl;  
  
  std::cout.flags(flg);  
}  
```

---

## Comment 8

> Username: ckormanyos  
> Created at: 2024-03-02 07:39:07 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/604#issuecomment-1974666948  

>> It's a pity that rounding errors occur with this type.   
  
> working on: https://github.com/cppalliance/decimal. It provides the fixed width decimal types specified in IEEE 754.  
  
Thanks @mborland indeed, I hope that one day, `Decimal` might nicely fill the niche where `cpp_dec_float` does not properly round, but clients require a formally correct, low-precision (under $128$-bits), base-10 type with rounding. This is the purpose of `Decimal` in the link (under construction).

---

## Comment 9

> Username: ckormanyos  
> Created at: 2024-03-02 07:41:04 UTC  
> Updated at: 2024-03-02 07:41:36 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/604#issuecomment-1974667341  

And finally, hi @jzmaddock. Thanks again for taking this on and solving it.  
  
As you know I've struggled with the _not_ _rounding_  of `cpp_dec_float` for a while now.  
  
Do you thing this kind of post-fix might work on `floor()` and/or `ceil()`? Then we could at least provide a workaround for clients who have known, small-valued integral arguments in problems like #368.

---

## Comment 10

> Username: jzmaddock  
> Created at: 2024-03-02 09:45:03 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/604#issuecomment-1974748537  

>Do you thing this kind of post-fix might work on floor() and/or ceil()? Then we could at least provide a workaround for clients who have known, small-valued integral arguments in problems like https://github.com/boostorg/multiprecision/issues/368.  
  
I don't think so - because we don't know where the number has been - if it's a fraction under an integer, then it might be that integer, or it might really be lower than it.  Obviously rounded values can exhibit strange behaviour too, but I think the danger is greater here.  fmod was a special case because we can tell via the functions post-conditions, when the division has gone bad.

---

## Comment 11

> Username: ckormanyos  
> Created at: 2024-03-02 10:16:02 UTC  
> Updated at: 2024-03-02 10:16:16 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/604#issuecomment-1974755037  

> I don't think so - because we don't know where the number has been - if it's a fraction under an integer, then it might be that integer, or it might really be lower than it. Obviously rounded values can exhibit strange behaviour too, but I think the danger is greater here. fmod was a special case because we can tell via the functions post-conditions, when the division has gone bad.  
  
Thanks for your insight John. Got it.  
  
Cool fix here on `fmod()`.
