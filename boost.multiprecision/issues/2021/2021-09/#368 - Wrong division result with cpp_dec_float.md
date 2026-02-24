# #368 - Wrong division result with cpp_dec_float [Open]

> Username: dlaugt  
> Created at: 2021-09-18 12:00:52 UTC  
> Updated at: 2024-02-29 18:47:59 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/368  

I'm using cpp_dec_float to don't deal on rounding issues when mathematic operations give some results with an exact base-10 representation. The result of 69000 / 184 = 375. With cpp_dec_float, it is a bit more.  
  
```cpp  
#include <iostream>  
#include <boost/multiprecision/cpp_dec_float.hpp>  
  
using decimal = boost::multiprecision::number<boost::multiprecision::cpp_dec_float<10>,  
                                              boost::multiprecision::et_off>;  
  
int main (int argc, char* argv[])  
{  
  decimal a = decimal {"69000"} / decimal {"184"};  
  decimal b = ceil (a);  
  
  std::cout << "a = " << a << std::endl;  
  std::cout << "b = " << b << std::endl;  
  
  return 0;  
}  
```  
  
The above program shows:  
  
> a = 375  
> b = 376

---

## Comment 1

> Username: ckormanyos  
> Created at: 2021-09-18 12:28:54 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/368#issuecomment-922269466  

> The above program shows:  
  
```  
    a = 375  
    b = 376  
```  
  
Hmmm... For some reason, I can not, at the moment reproduce this behavior. I tried with Boost 1.77 and both VC14.2 and GCC 9.3 on x86_64-linux-gnu and got `375` for both `a` as well as `b`.  
  
Could you please provide more information on the compiler/Boost-version you are using to obtain this result?

---

## Comment 2

> Username: jzmaddock  
> Created at: 2021-09-18 12:29:13 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/368#issuecomment-922269505  

I'm fairly sure this is a result of the cpp_dec_float not performing any rounding (and having guard digits), but @ckormanyos will no doubt confirm shortly.

---

## Comment 3

> Username: ckormanyos  
> Created at: 2021-09-18 12:52:30 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/368#issuecomment-922272310  

> `cpp_dec_float` not performing any rounding  
  
Thanks john. That's what I thought at first, ... but...  
  
> but @ckormanyos will no doubt confirm shortly  
  
... actually, I am having difficulty reproducing the result. I obtain `375` for both `a` and `b`. I tried the compilers/Boost-version mentioned above and also develop branch of Boost.  
  
So I would like to find out the compiler/Boost-version/target-system exhibiting this phenomenon.

---

## Comment 4

> Username: dlaugt  
> Created at: 2021-09-18 12:52:33 UTC  
> Updated at: 2021-09-18 12:56:49 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/368#issuecomment-922272316  

I'm using boost 1.73 with visual studio 16.11.2. The compilation is done in 32 bits.  
  
I've tried with more decimals (50). This time, the result is smaller than 375.  
  
```cpp  
#include <iostream>  
#include <boost/multiprecision/cpp_dec_float.hpp>  
  
using decimal = boost::multiprecision::number<boost::multiprecision::cpp_dec_float<50>,  
                                              boost::multiprecision::et_off>;  
  
int main (int argc, char* argv[])  
{  
  decimal a = decimal {"69000"} / decimal {"184"};  
  decimal b = floor (a);  
  decimal c = ceil (a);  
  
  std::cout << "a = " << a << std::endl;  
  std::cout << "b = " << b << std::endl;  
  std::cout << "c = " << c << std::endl;  
  
  double a2 = 69000.0 / 184.0;  
  double b2 = floor (a2);  
  double c2 = ceil (a2);  
  
  std::cout << std::endl;  
  std::cout << "a2 = " << a2 << std::endl;  
  std::cout << "b2 = " << b2 << std::endl;  
  std::cout << "c2 = " << c2 << std::endl;  
  
  return 0;  
}  
```  
  
The above program shows:  
> a = 375  
> b = 374  
> c = 375  
>  
> a2 = 375  
> b2 = 375  
> c2 = 375  
   
I will try to upgrade to boost 1.77.

---

## Comment 5

> Username: ckormanyos  
> Created at: 2021-09-18 12:55:23 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/368#issuecomment-922272665  

> upgrade to boost 1.77  
  
Yes, please try that. If the behavior is different/correct, then i would also take the time on our side to backtrace to 1.73 and figure out what did/is going on.  
  
For me this one is, ... to be continued. But if oyu get a chance, please report your experienc with a more modern Boost. I did a big refactor of `cpp_dec_float` at either 1.76 or 1.77 (I forgot which). So this issue is quite interestung to me/us.

---

## Comment 6

> Username: dlaugt  
> Created at: 2021-09-18 13:18:37 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/368#issuecomment-922275516  

I'm using vcpkg as package manager. In vcpkg, boost 1.77 is not present yet. However, I confirm that I have the same issue with boost 1.76.

---

## Comment 7

> Username: ckormanyos  
> Created at: 2021-09-18 16:23:48 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/368#issuecomment-922335495  

>  boost 1.77 is not present yet. However, I confirm that I have the same issue with boost 1.76.  
  
I can reproduce and confirm the errant behavior as reported on 1.76. This phenomenon has been corrected in 1.77.  
  
As a next step I will figure out why. I am rather sure it is the refactor of `cpp_dec_float`. I will report the reason shortly.

---

## Comment 8

> Username: ckormanyos  
> Created at: 2021-09-19 11:32:18 UTC  
> Updated at: 2021-09-19 11:35:14 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/368#issuecomment-922458695  

This issue points out that `cpp_dec_float` still needs rounding. There are also some potential weaknesses in the `ceil` function. I believe we need to leave this issue open and, for the moment, not corrected.  
  
The good news is that the long-term plan is to correct all these. The bad news is that some calculations of the genre reported above still fail with 1.77.  
  
I wrote the program below to scan thousands of test cases and found several distinct failure modes on the `ceil` func after calling division --- all easy to explain. I am not yet sure how to best fix them. Adding a little more logic (like some kind of rounding) to `floor`/`ceil` only and postponing the full treatment of rounding would be one potential help. But I'd still like to address rounding in the entire class.  
  
```  
#include <iostream>  
#include <boost/multiprecision/cpp_dec_float.hpp>  
  
// cd /mnt/c/Users/User/Documents/Ks/PC_Software/Test  
// g++ -Wall -O3 -I/mnt/c/boost/boost_1_77_0 test.cpp -o test.exe  
  
using decimal = boost::multiprecision::number<boost::multiprecision::cpp_dec_float<10>,  
                                              boost::multiprecision::et_off>;  
  
int main(int argc, char* argv[])  
{  
  volatile std::uint64_t debug = 0U;  
  
  for(std::uint32_t lo_index = 101U; lo_index < 1010U; lo_index += 3U)  
  {  
    for(std::uint32_t hi_index = 10001U; hi_index < 100010U; hi_index += 17U)  
    {  
      const std::uint32_t lo_hi = lo_index * hi_index;  
  
      const decimal a = decimal { lo_hi } / decimal { lo_index };  
      const decimal b = ceil(a);  
  
      const std::string str_a = boost::lexical_cast<std::string>(a);  
      const std::string str_b = boost::lexical_cast<std::string>(b);  
  
      const bool result_is_ok = (str_a == str_b);  
  
      if(result_is_ok == false)  
      {  
        std::cout << "Error: " << "lo_index: " << lo_index << ", hi_index: " << hi_index << std::endl;  
  
        const decimal a_debug = decimal { lo_hi } / decimal { lo_index };  
        const decimal b_debug = ceil(a_debug);  
  
        ++debug;  
      }  
    }  
  }  
}  
```  
  
I'll correct these in the long term on `cpp_dec_float`. The class `cpp_bin_float` does not, at the moment, suffer from these weaknesses. But it is binary and not base-10. So in summary, it's time to improve rounding, `floor`/`ceil` on `cpp_dec_float`. This has been known for a while, but not yet clearly reported in a client case, found _in the wild_. So thanks for this report, as it motivates improvement.

---

## Comment 9

> Username: ckormanyos  
> Created at: 2021-09-19 11:43:19 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/368#issuecomment-922460137  

Summary:  
  
`cpp_dec_float` could benefit from:  
- better rounding  
- improved logic on `floor`/`ceil`  
- optiionally implement Knuth-style long division algorithm for exact division on low limb counts.  
  
Keep this issue open.

---

## Comment 10

> Username: dlaugt  
> Created at: 2021-09-19 19:37:26 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/368#issuecomment-922525148  

Thanks for the update. It doesn't seem like an easy problem to solve. Take all the time necessary for the most appropriate approach. I'm afraid I can't help on how to fix it...

---

## Comment 11

> Username: ckormanyos  
> Created at: 2021-09-20 07:18:58 UTC  
> Updated at: 2021-09-20 07:19:55 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/368#issuecomment-922687651  

>  doesn't seem like an easy problem to solve. Take all the time necessary for the most appropriate approach.  
  
Indeed.  
  
Thanks for the clear report. The phenomenon you found is a bug and we have classified this as a bug. I will try to fix this for 1.78. Probably the first fix will involve rounding and slight refinements to `ceil` and maybe `floor` as well.

---

## Comment 12

> Username: MarcinZukowski  
> Created at: 2022-04-28 00:00:35 UTC  
> Updated at: 2022-04-28 00:13:36 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/368#issuecomment-1111590233  

I noticed a very similar problem, asked in [StackOverflow,](https://stackoverflow.com/questions/72033580/incorrect-result-with-cpp-dec-float-division) a comment pointed me here.  
  
My test case is super simple, I'm dividing `15 / 3` using `cpp_dec_float_15`, and I'm not getting an exact `5`.  
  
Full repro and output below:  
  
```  
#include <boost/multiprecision/cpp_dec_float.hpp>  
#include <iostream>  
  
int main()  
{  
   using namespace boost::multiprecision;  
   using namespace std;  
  
   cpp_dec_float_50 a = 15;  // exactly 5 * 3  
   cpp_dec_float_50 b = 3;  
   cpp_dec_float_50 c = a / b;  // should be exactly 5  
   cpp_dec_float_50 d = 5  
   cout << setprecision(std::numeric_limits<cpp_dec_float_50>::max_digits10);  
   cout << "c: " << c << endl;  
   cout << "d: " << d << endl;  
   cout << "c == d: " << (c == d ? "true" : "false") << endl;  
   return 0;  
}  
```  
This produces  
```  
c: 4.999999999999999999999999999999999999999999999999999999999999999999999995  
d: 5  
c == d: false  
```  
  
This is on MacOSX,with `g++-11 (Homebrew GCC 11.3.0) 11.2.0` and `boost: stable 1.78.0 (bottled), HEAD`.  
  
This behavior effectively prohibits using boost::multiprecision for some applications.  
  
*EDIT:* just in case also tested on Linux, with `g++ (GCC) 10.2.1 20210130 (Red Hat 10.2.1-11)` and boost 1.78, same thing.

---

## Comment 13

> Username: ckormanyos  
> Created at: 2022-04-28 06:04:40 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/368#issuecomment-1111782042  

> noticed a very similar problem  
  
Hi @MarcinZukowski thank you for pointing this out. Your observations are correct.  
  
Unfortunately, I have not yet found time to correct this issue, which is, in fact, marked  as a _bug_.  
  
> This behavior effectively prohibits using boost::multiprecision for some applications.  
  
Thanks for your query and clear statement, as this adds motivation for us to fix this.

---

## Comment 14

> Username: jeteve  
> Created at: 2022-08-16 13:00:15 UTC  
> Updated at: 2022-08-16 13:02:15 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/368#issuecomment-1216605863  

Hi,  
  
I found a similar issue:  
  
https://wandbox.org/permlink/Ly2kN6A7Fza0Jo26  
```  
#include <iostream>  
#include <boost/multiprecision/cpp_dec_float.hpp>  
#include <boost/math/special_functions/modf.hpp>  
  
using namespace std;  
using namespace boost::multiprecision;  
using my_f = cpp_dec_float_100;  
  
int main(){  
      const my_f dTS("3");  
      const my_f dTO("15");  
      const my_f ratio{ dTO / dTS };      
      my_f intPart;  
      my_f decPart = modf(ratio, &intPart);  
      cout.precision(std::numeric_limits<my_f>::digits10);  
      cout << "Ratio:" << ratio << " Int:" << intPart << " Dec:" << decPart << endl;  
}  
````  
  
Produces:  
  
`Ratio:5 Int:4 Dec:1`  
`

---

## Comment 15

> Username: ckormanyos  
> Created at: 2022-08-23 07:54:36 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/368#issuecomment-1223694281  

> found a similar issue  
  
Thanks for the report @jeteve. This issue remains open and marked as a bug. I do not yet have the proper codes for a fix, but the issue has not been forgotten.  
  
Kind regards, Chris

---

## Comment 16

> Username: kevin-1016  
> Created at: 2024-01-18 02:39:17 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/368#issuecomment-1897677059  

Hello, I had a similar problem and I found the possible cause.  
My test case is:  
  
```  
using cpp_dec_float_20_backend = boost::multiprecision::cpp_dec_float<20>;  
using cpp_dec_float_20 = boost::multiprecision::number<cpp_dec_float_20_backend, boost::multiprecision::et_off>;  
int main() {  
	cpp_dec_float_20 q1 = 15;  
	cpp_dec_float_20 q2 = 3;  
	cpp_dec_float_20 q3 = q1 / q2;  
  
	return 0;  
}  
```  
  
I compared boost 1.72 to boost 1.82.  
The result of boost1.72 is:  
![image](https://github.com/boostorg/multiprecision/assets/56884055/7024cddc-7d37-47d9-b052-5ec284fdbff7)  
The result of boost 1.82 is:  
![image](https://github.com/boostorg/multiprecision/assets/56884055/b71543a2-fd3f-4a0b-a36b-d2b813d44f1a)  
  
I found that in the cpp_dec_float.hpp file of boost 1.82, I looked for "Handle a potential carry". In the else code branch, there may be a problem with executing the second parameter of std::copy. The second parameter is std: :min is used to obtain the minimum value. The value of cpp_dec_float_elem_number is 6. However, the first parameter starts from the position of offset 1. As a result, only five precisions are obtained. Therefore, the obtained result is slightly smaller than expected. This problem has a large impact. When the round, ceil, and floor functions are used to operate the decimal operation result, the impact is magnified. Please confirm whether this is the problem.  
  
```  
// Handle a potential carry.  
if (result[0U] != static_cast<std::uint32_t>(0U))  
{  
	exp += static_cast<exponent_type>(cpp_dec_float_elem_digits10);  
  
	// Shift the result of the multiplication one element to the right.  
	std::copy(result.cbegin(),  
		result.cbegin() + static_cast<std::ptrdiff_t>(prec_elems_for_multiply),  
		data.begin());  
}  
else  
{  
	std::copy(result.cbegin() + 1,  
		result.cbegin() + (std::min)(static_cast<std::int32_t>(prec_elems_for_multiply + 1), cpp_dec_float_elem_number),  
		data.begin());  
}  
```

---

## Comment 17

> Username: ckormanyos  
> Created at: 2024-01-18 04:56:13 UTC  
> Updated at: 2024-01-18 04:57:46 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/368#issuecomment-1897798399  

> problem has a large impact. When the round, ceil, and floor functions are used to operate the decimal operation result, the impact is magnified. Please confirm whether this is the problem.  
  
Hi Kevin (@kevin-1016) thank you for looking into this.  
  
As far as i can tell, you have found the rounding problem. This is the same problem in this thread: The `cpp_dec_float`-backend does not round.  
  
Unfortunately, however, I think the the lines of code you mention are not the root cause of this problem.  
  
The comment _Handle_ _a_ _potential_ _carry_ in this particular case might be in the wrong place, or the comment is not adequately informative. In that part of the code, there might be a carry resulting from multiplication and the entire internal storage array needs to be shifted one element down in order to make room for the carry result.  
  
I could do a bit better with the clarity of the comment, but basically we have a case where all those trailing nines _should_ have resulted in internal rounding. But there is no rounding. So the result stays very near to, but not exaclty $5$.  
  
`cpp_dec_float` is not exact and it _does_ _not_ round.

---

## Comment 18

> Username: kevin-1016  
> Created at: 2024-01-18 06:22:20 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/368#issuecomment-1897874230  

> > problem has a large impact. When the round, ceil, and floor functions are used to operate the decimal operation result, the impact is magnified. Please confirm whether this is the problem.  
>   
> Hi Kevin (@kevin-1016) thank you for looking into this.  
>   
> As far as i can tell, you have found the rounding problem. This is the same problem in this thread: The `cpp_dec_float`-backend does not round.  
>   
> Unfortunately, however, I think the the lines of code you mention are not the root cause of this problem.  
>   
> The comment _Handle_ _a_ _potential_ _carry_ in this particular case might be in the wrong place, or the comment is not adequately informative. In that part of the code, there might be a carry resulting from multiplication and the entire internal storage array needs to be shifted one element down in order to make room for the carry result.  
>   
> I could do a bit better with the clarity of the comment, but basically we have a case where all those trailing nines _should_ have resulted in internal rounding. But there is no rounding. So the result stays very near to, but not exaclty 5.  
>   
> `cpp_dec_float` is not exact and it _does_ _not_ round.  
  
But I got the correct result after result.cbegin()**+1**  
  
	std::copy(result.cbegin() + 1,  
		result.cbegin() + 1 + (std::min)(static_cast<std::int32_t>(prec_elems_for_multiply + 1), cpp_dec_float_elem_number),  
		data.begin());

---

## Comment 19

> Username: kevin-1016  
> Created at: 2024-01-19 01:29:20 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/368#issuecomment-1899489756  

> `cpp_dec_float` is not exact  
  
I mean, although cpp_dec_float is not exact, it takes six decimal places for boost172, but only five decimal places for boost182, which causes this problem. As long as I change the decimal length to 6, it's fine.

---

## Comment 20

> Username: ckormanyos  
> Created at: 2024-01-19 02:38:32 UTC  
> Updated at: 2024-01-19 02:39:04 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/368#issuecomment-1899550284  

>  change the decimal length  
  
Hi @kevin-1016 in the code you posted just above, the copy operation could extend beyond the range of `result.data()`.  
  
You see in the proposed change,  
  
```  
std::copy(result.cbegin() + 1,  
          result.cbegin() + 1 + (std::min)(static_cast<std::int32_t>(prec_elems_for_multiply + 1), cpp_dec_float_elem_number),  
          data.begin());  
```  
  
you could end up copying to the end-range of  
  
```  
result.cbegin() + 1 + cpp_dec_float_elem_number  
```  
  
and that can't be _quite_ right.  
  
I will investigate further. But I fear, `cpp_dec_float` might not have a quick-fix for proper rounding.

---

## Comment 21

> Username: ckormanyos  
> Created at: 2024-01-19 03:20:52 UTC  
> Updated at: 2024-01-19 03:21:46 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/368#issuecomment-1899618265  

>>> although `cpp_dec_float` is not exact, it takes six decimal places for boost172, but only five decimal places for boost182  
  
Ah. Yes, @kevin-1016, I am finally comprehending your point. Sorry it took a few iterations for me to grasp your point.  
  
>> could end up copying to the end-range  
  
This statement from myself might be incorrect since the buffer of the multiplication result is wider than the destination range.  
  
> I will investigate further.  
  
I will run more local tests and CI. I'm not sure if this is complete rounding, but it might improve `cpp_dec_float`.

---

## Comment 22

> Username: ckormanyos  
> Created at: 2024-01-19 06:36:08 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/368#issuecomment-1899846970  

See also #585 resulting in reduction of truncation in `cpp_dec_float`'s multiplication.  
  
Cc: @kevin-1016 (with many thanks)

---

## Comment 23

> Username: ckormanyos  
> Created at: 2024-01-19 18:01:42 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/368#issuecomment-1900855264  

I have switched the labels on this issue to _enhancement_ and _low priority_. The next step to undertake would be trivial rounding on `floor` and `ceil` functions only.

---

## Comment 24

> Username: ckormanyos  
> Created at: 2024-02-29 18:47:57 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/368#issuecomment-1971750171  

See also #604
