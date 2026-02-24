# #734 - cpp_dec_float.str(0, fixed) produces unexpected result [Closed]

> Username: f1u77y  
> Created at: 2025-09-05 05:19:34 UTC  
> Updated at: 2025-09-06 15:39:45 UTC  
> Closed at: 2025-09-06 15:39:44 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/734  

The following code:  
```c++  
mp::cpp_dec_float<15> x("0.0000000000222222222222222");  
std::cout << x.str(0, std::ios_base::fixed);  
```  
Produces `0` while according to documentaion for [backend requirements](https://www.boost.org/doc/libs/latest/libs/multiprecision/doc/html/boost_multiprecision/ref/backendconc.html) it should produce "as many digits as are required to reconstruct the original value", which is `0.0000000000222222222222222`. This might be true for other backend as well.  
  
That example is reproducible at least on boost 1.83.0 and 1.89.0.  
  
**UPD** is also reproducible on latest develop branch which is `43ce2d31497ac22f2c35d064bbc68ff377dcbcb6` at the moment.

---

## Comment 1

> Username: ckormanyos  
> Created at: 2025-09-05 07:03:58 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/734#issuecomment-3257292856  

@f1u77y it seems like you are using the `cpp_dec_float` backend directly.  
  
That is not the real way the library is intended to be used. You need to use the higher-layer wrapping of a so-called `number` type.  
  
In the modified example below, this is shown. It is also essential to set the precision flags of the output stream that is being used. Consider the example below which wraps the backend in number and uses a stream output.  
  
```  
#include <boost/multiprecision/cpp_dec_float.hpp>  
  
#include <iomanip>  
#include <iostream>  
  
auto main() -> int  
{  
  // Use the number wrapper.  
  using mp_type = boost::multiprecision::number<boost::multiprecision::cpp_dec_float<15>>;  
  
  mp_type x("0.0000000000222222222222222");  
  
  // Set the output stream flags.  
  std::cout << std::setprecision(15) << std::fixed << x << std::endl;  
}  
```

---

## Comment 2

> Username: ckormanyos  
> Created at: 2025-09-05 07:05:56 UTC  
> Updated at: 2025-09-05 07:06:35 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/734#issuecomment-3257297427  

In a further iteration, some developers prefer to avoid setting the flags of `std::cout`. In that case, you can use an intermediate `std::stringstream` for formatting.  
  
I also posted this example at [Godbolt here](https://godbolt.org/z/Wq5fcoE46).  
  
```  
#include <boost/multiprecision/cpp_dec_float.hpp>  
  
#include <iomanip>  
#include <iostream>  
#include <sstream>  
  
auto main() -> int  
{  
  using mp_type = boost::multiprecision::number<boost::multiprecision::cpp_dec_float<15>>;  
  
  mp_type x("0.0000000000222222222222222");  
  
  std::stringstream strm { };  
  
  strm << std::setprecision(15) << std::fixed << x;  
  
  std::cout << strm.str() << std::endl;  
}  
```

---

## Comment 3

> Username: f1u77y  
> Created at: 2025-09-05 07:38:54 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/734#issuecomment-3257382945  

@ckormanyos I've tried with `mp::number` as well but it doesn't make a difference. Using `std::ostream` output instead of `str` function doesn't make a difference as well.  
  
Godbolt example outputs `0.000000000022222` which behaves exactly like the docs suggest, but I'm talking about the `str(ss, pp)` method. Also, docs for "Backend requirements" state that in case of `ss== 0` it should produce "as many digits as are required to reconstruct the original value" but [docs for `mp::number`](https://www.boost.org/doc/libs/latest/libs/multiprecision/doc/html/boost_multiprecision/ref/number.html) don't mandate such a requirement. Said docs look outdated for the `str()` method though: its signature is `str()` in synopsis and `str(unsigned precision, bool scientific = true)` in the actual method documentation below, neither of which is in sync with the library code.  
  
So, I believe it's one of those:  
  
- Docs for "Backend requirements" should not mention the case of `prec == 0` to be consistent with existing code and `mp::number` docs.  
- Docs for `mp::number` as well as the backend code should be updated to behave correctly for the case of `prec == 0`  
  
TBH I think it's better to leave an option to output a number in such a way that it doesn't lose precision (so, I prefer the second option) but IG it's up for discussion.

---

## Comment 4

> Username: ckormanyos  
> Created at: 2025-09-05 08:34:44 UTC  
> Updated at: 2025-09-05 08:35:57 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/734#issuecomment-3257536069  

You are _allowed_ to call the backend `str(...)` method. And it has two parameters (that are both defaulted). Again, this is not for the normal use case. But if you are writing your own number backend, then you need to get into the details of the `str(...)` method.  
  
A typical signature of the backend `str(...)` method will look something like this:  
  
```  
std::string str(std::streamsize digits = 0, std::ios_base::fmtflags f = std::ios_base::fmtflags(0)) const;  
```  
  
In the second parameter, you must pass all the flags you want to control the output format (and its precision).  
  
I will have to look at the documentation of the backend requirements. To be honest, I haven't looked there for a while. But I do have an open issue for docs and examples. So I will take a look along the way.

---

## Comment 5

> Username: jzmaddock  
> Created at: 2025-09-05 08:42:21 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/734#issuecomment-3257561999  

@ckormanyos this does look like a bug to me - I admit I hadn't foreseen str() being called with zero precision and `std::ios_base::fixed` as potentially that could result in outrageously long strings - long enough to run the system out of memory actually when we have `long long` exponents, but still, you get what you asked for...

---

## Comment 6

> Username: ckormanyos  
> Created at: 2025-09-05 09:02:03 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/734#issuecomment-3257615699  

> @ckormanyos this does look like a bug to me - I admit I hadn't foreseen `str()` being called with zero precision and `std::ios_base::fixed` as potentially that could result in outrageously long strings - long enough to run the system out of memory actually when we have long long exponents, but still, you get what you asked for...  
  
Hi John. I am actually in a real bug-fixing, edge-case-finding mode right now. Do you think we should provide an upper limit?  
  
I will also look at the docs.

---

## Comment 7

> Username: ckormanyos  
> Created at: 2025-09-05 09:13:31 UTC  
> Updated at: 2025-09-05 09:17:26 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/734#issuecomment-3257654831  

As it turns out, for `cpp_dec_float`, calling the backend `str()` method with `std::fixed` and zero requested digits, does get to a funny line, in particular if the exponent is negative. We will need to discuss how to repair this.  
  
I quickly scraped together some test code. I find (for `cpp_dec_float`) the behavior is somehow defined. But it is defined so poorly in the code, that it's practically undefined.  
  
```  
#include <boost/multiprecision/cpp_dec_float.hpp>  
  
#include <iomanip>  
#include <iostream>  
#include <sstream>  
  
auto main() -> int  
{  
  using mp_type = boost::multiprecision::number<boost::multiprecision::cpp_dec_float<15>>;  
  
  mp_type x("0.0000000000222222222222222");  
  
  std::stringstream strm { };  
  
  strm << std::fixed;  
  
  const std::string str_out { x.backend().str(0, strm.flags()) };  
  
  std::cout << str_out << std::endl;  
}  
```

---

## Comment 8

> Username: ckormanyos  
> Created at: 2025-09-05 09:32:50 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/734#issuecomment-3257708669  

Hi @jzmaddock I ran some tests for the dec and bin backends, these then compared with double. I actually find the behavior correct.  
  
The full test code is below.  
  
The output is here:  
  
```  
for double:  
streamsize  0: 0  
streamsize 15: 0.000000000022222  
for cpp_bin_float:  
streamsize  0: 0  
streamsize 15: 0.000000000022222  
for cpp_dec_float:  
streamsize  0: 0  
streamsize 15: 0.000000000022222  
```  
  
TEST CODE  
  
```  
#include <boost/multiprecision/cpp_bin_float.hpp>  
#include <boost/multiprecision/cpp_dec_float.hpp>  
  
#include <iomanip>  
#include <iostream>  
#include <sstream>  
  
namespace local {  
  
template<typename BackendType>  
auto print_mp(const std::streamsize strm_size) -> void  
{  
  using float_type = boost::multiprecision::number<BackendType>;  
  
  float_type x("0.0000000000222222222222222");  
  
  std::stringstream strm { };  
  
  strm << std::setprecision(strm_size) << std::fixed;  
  
  const std::string str_out { x.backend().str(strm_size, strm.flags()) };  
  
  std::cout << str_out << std::endl;  
}  
  
auto print_dbl(const std::streamsize strm_size) -> void  
{  
  using float_type = double;  
  
  float_type x(0.0000000000222222222222222);  
  
  std::stringstream strm { };  
  
  strm << std::setprecision(strm_size) << std::fixed << x;  
  
  const std::string str_out { strm.str() };  
  
  std::cout << str_out << std::endl;  
}  
  
} // namespace local  
  
auto main() -> int  
{  
  std::cout << "for double:\n";  
  std::cout << "streamsize  0: "; local::print_dbl(0);  
  std::cout << "streamsize 15: "; local::print_dbl(15);  
  
  std::cout << "for cpp_bin_float:\n";  
  std::cout << "streamsize  0: "; local::print_mp<boost::multiprecision::cpp_bin_float<15>>(0);  
  std::cout << "streamsize 15: "; local::print_mp<boost::multiprecision::cpp_bin_float<15>>(15);  
  
  std::cout << "for cpp_dec_float:\n";  
  std::cout << "streamsize  0: "; local::print_mp<boost::multiprecision::cpp_dec_float<15>>(0);  
  std::cout << "streamsize 15: "; local::print_mp<boost::multiprecision::cpp_dec_float<15>>(15);  
}  
```  
  
@f1u77y your suspected bug might just be a matter of documentation? I will run a few more tests and maybe even add some edge-case tests. I am adding lots of edge-case tests these days. These might also lead to bug findings and maybe better code coverage.

---

## Comment 9

> Username: ckormanyos  
> Created at: 2025-09-05 09:39:55 UTC  
> Updated at: 2025-09-05 09:40:41 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/734#issuecomment-3257728180  

> Produces `0` while according to documentaion for [backend requirements](https://www.boost.org/doc/libs/latest/libs/multiprecision/doc/html/boost_multiprecision/ref/backendconc.html) it should produce "as many digits as are required to reconstruct the original value", which is `0.0000000000222222222222222`. This might be true for other backend as well.  
  
OK back to the original query. So `0` is the string that _should_ be printed. And the dec and bin backends _do_ behave this way. But this disagrees with the docs.  
  
I wonder what the right fix is here docs or code? I think docs? John, your thoughts?

---

## Comment 10

> Username: f1u77y  
> Created at: 2025-09-05 09:49:54 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/734#issuecomment-3257754943  

@jzmaddock   
> that could result in outrageously long strings - long enough to run the system out of memory  
  
It's already the case of a huge positive exponent, so I guess it would not harm to do the same for huge negative exponent.

---

## Comment 11

> Username: f1u77y  
> Created at: 2025-09-05 10:24:34 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/734#issuecomment-3257860290  

@ckormanyos   
> your suspected bug might just be a matter of documentation?  
  
It depends on the point of view but I personally see it as something that should be fixed in code, because it would just be nice to have a way to retrive a fixed representation without losing precision.

---

## Comment 12

> Username: ckormanyos  
> Created at: 2025-09-05 13:16:30 UTC  
> Updated at: 2025-09-05 13:18:08 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/734#issuecomment-3258324637  

>> your suspected bug might just be a matter of documentation?  
  
> It depends on the point of view but I personally see it as something that should be fixed in code, because it would just be nice to have a way to retrive a fixed representation without losing precision.  
  
That is a valid point. At the moment, the multiprecision types behave (in this matter) the same way as, for example, built-in `double`. But there is one other minute difference between built-ins and multiprecision types. So we don't promise to be _exactly_ the same anyway.  
  
You can always specifically set the precision you need with `std::setprecision(std::streamsize)`. This kind of activity can be seen in some of my examples.  
  
Unfortunately, at this moment in the evolution of Boost.Multiprecision, I would _not_ recommend making your proposed change. I judge the risk of potentially disturbing already-existing client code as too high. I would recommend correcting the docs. I do not, however, know what @jzmaddock thinks on this.  
  
Cc: @f1u77y

---

## Comment 13

> Username: ckormanyos  
> Created at: 2025-09-05 14:21:14 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/734#issuecomment-3258536346  

Ok what I've found so far:  
  - The docs about `number`'s method `str(...)` have the wrong signature for the function's parameters in the synopsis.  
  - The docs for the backend-requirements _might_ also need correction.  
  - The new `cpp_double_fp_backend` backend behaved differently than dec/bin-float.  
  
I can work out corrections and add tests for these.

---

## Comment 14

> Username: jzmaddock  
> Created at: 2025-09-05 16:11:28 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/734#issuecomment-3258913658  

Apologies to @ckormanyos and @f1u77y I may have led you astray and posted before thinking (always dangerous!!)  
  
Here's what I vaguely remember:  
  
* When the library was first written the intention was that a precision of zero meant "give me all the digits".  This was chosen because I couldn't think of a legitimate use case for zero precision.  
* Later it was pointed out to me that there was a legitimate use case: when printing with `std::ios_base::fixed` then a precision of zero should mean "give me the nearest integer".  So we made `std::ios_base::fixed` a special case, and there is no longer a means of getting all the digits when using `std::ios_base::fixed`.  This is an inconvenience, but...  
* This is a "good thing", remember we can have exponents up to 2^63, well actually 2^127 if you really want to go there, so having such an option is downright dangerous if your code starts trying to format 2^127 digits after erroneous user input.  Of course this particular foot-gun still exists for very large numbers, but at least we exclude the very small ones.  Note that for type `double` we only have an 11 bit exponent, so formatting DBL_MAX in fixed precision while foolish, will probably not crash the spacecraft ;)  
  
So my suggestion is that if you really want all the digits of an arbitrary floating point number, you should never ever use `std::ios_base::fixed`, probably even in double precision.  
  
Whew.  
  
And yes, I'm sure the docs need updating, we probably tidied up our integer type usages at some point without updating the docs.

---

## Comment 15

> Username: ckormanyos  
> Created at: 2025-09-06 08:09:48 UTC  
> Updated at: 2025-09-06 08:10:38 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/734#issuecomment-3261563331  

OK that all makes sense.  
  
I can find two places in the docs and one trivial error in the new `cpp_double_fp_backend` that could benefit from corrections. I also have some local new tests for the specific case of `std::ios::fixed` that I can add to the main test suite.  
  
I will work up a PR for these trivial changes.  
  
Thanks @jzmaddock for the details.  
  
And thank you @f1u77y for pointing out this issue.
