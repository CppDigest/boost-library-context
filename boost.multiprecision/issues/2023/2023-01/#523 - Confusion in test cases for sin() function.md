# #523 - Confusion in test cases for sin() function [Closed]

> Username: ckormanyos  
> Created at: 2023-01-22 10:11:14 UTC  
> Updated at: 2023-01-22 16:13:05 UTC  
> Closed at: 2023-01-22 16:13:05 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/523  

Hi John (@jzmaddock) what am I missing that must be simple (or am I crazy)?  
  
We have a detailed development of a new backend going on and while doing that, I study the test cases in [test_sin.cpp](https://github.com/boostorg/multiprecision/blob/develop/test/test_sin.cpp).  
  
The control data seem to be inteneded to represent what's pasted below for [Wolfram Alpha (R)](https://www.wolframalpha.com/input?i=Table%5BN%5BSin%5BEulerGamma*%28%28100+k%29+-+5000%29%5D%2C+101%5D%2C+%7Bk%2C+0%2C+100%2C+1%7D%5D) or locally. But the numbers I get there seem somehow to differ from the [control data](https://github.com/boostorg/multiprecision/blob/98d72b682c8a38661905d23655721776534f11eb/test/test_sin.cpp#L83).  
  
```  
Table[N[Sin[EulerGamma*((100 k) - 5000)], 101], {k, 0, 100, 1}]  
```  
  
There is a big tolerance on the test. Did I just confuse the wrong control data table in the first test loop? I hope so. Or do we need to look into the control data? Or is my translation to the language of Wolfram Alpha (R) maybe wrong?  
  
Cc: @mborland

---

## Comment 1

> Username: ckormanyos  
> Created at: 2023-01-22 10:13:27 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/523#issuecomment-1399446380  

For instance,  
  
```  
N[Sin[EulerGamma ((100 1) - 5000)], 101]  
```  
  
gives `-0.797638583669534812597149...`. But I believe element 1 in the data set differs from this expected value. I often make silly mistake. Wonder if I did?

---

## Comment 2

> Username: ckormanyos  
> Created at: 2023-01-22 10:15:15 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/523#issuecomment-1399446691  

Putting Fahad (@sinandredemption) in Cc since he is co-author of this backend.

---

## Comment 3

> Username: ckormanyos  
> Created at: 2023-01-22 11:00:51 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/523#issuecomment-1399454418  

Uggghhh. I believe the index variable `k`, as being `unsigned` wraps the underflow around and confuses.  
  
I would cast the index `k` to a signed integral type and maybe consider changing the test `data`?

---

## Comment 4

> Username: jzmaddock  
> Created at: 2023-01-22 11:02:13 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/523#issuecomment-1399454693  

@ckormanyos : I see what you're seeing, and I don't know where the difference is :(  BTW didn't you write that test?  
  
If I extract the code into  
  
```  
#include <iostream>  
#include <iomanip>  
#include <boost/multiprecision/cpp_bin_float.hpp>  
  
int main()  
{  
   std::cout << std::setprecision(30);  
   using T = boost::multiprecision::cpp_bin_float_quad;  
   static const T euler_gamma = static_cast<T>("5.77215664901532860606512090082402431042159335939923598805767234884867726777664670936947063291746749514631447249807082480960504014486542836224173997644923536253500333742937337737673942792595258247094916008735203948165670853233151776611528621199501507984793745085705740029921354786146694029604325421519e-1");  
   for (int k = 0; k < 10; ++k)  
   {  
      T val = sin(euler_gamma * ((100 * k) - 5000));  
      std::cout << val << std::endl;  
   }  
  
   std::cout << std::endl << std::setprecision(16);  
  
   double g = 5.77215664901532860606512090082402431042159335e-1;  
   for (int k = 0; k < 10; ++k)  
   {  
      T val = std::sin(g * ((100 * k) - 5000));  
      std::cout << val << std::endl;  
   }  
  
  
}  
```  
  
I see:  
  
```  
-0.865087179683067252708187359165  
-0.797638583669534812597149574894  
0.246947514812703938169894550153  
0.989013546180296021750901758475  
0.519500491219840443397382144914  
-0.586420359093869515237687266954  
-0.97395403906078050869854812754  
-0.168357099807987317797633647904  
0.843483668915081157881609334416  
0.822024967814241172490083350374  
  
-0.8650871796829448  
-0.7976385836694377  
0.2469475148128118  
0.9890135461803051  
0.5195004912198302  
-0.5864203590938389  
-0.9739540390608004  
-0.1683570998081225  
0.8434836689149808  
0.8220249678143758  
```  
  
So std::sin and our sin agree but appear different from wolframalpha and our test data ????  
  
If I put some printf statements in the test program, it's generating the same values as the test data, but I can't for the life of me see what the difference is to my reduced test case above.  
  
Also confused, yours!

---

## Comment 5

> Username: ckormanyos  
> Created at: 2023-01-22 11:22:00 UTC  
> Updated at: 2023-01-22 11:22:40 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/523#issuecomment-1399458223  

Hi John (@jzmaddock) I believe the way that test is written relies on an underflow of `unsigned` resulting in large integral arguments for low values of `k`.  
  
If I wrote that test (as I think I did), then today I'm most definitely feeling the urge to _rewrite_ it using an explicit cast to the integral `k`-factor to a signed type. In that way, this test would make a lot more intuitive sense to me.  
  
I am almost done re-writing the test locally. I can present it in a PR if you agree?  
  
Cc: @mborland

---

## Comment 6

> Username: jzmaddock  
> Created at: 2023-01-22 11:52:38 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/523#issuecomment-1399463694  

OMG that's horrible!  Yes please do go ahead and fix.  test_cos.cpp has the same issue.  I would make the loop variable signed and cast to unsigned for the table lookup only if that warns, but anything is better than where it is now frankly.  We might find the test tolerances can be reduced quite a bit as a result of this change too...

---

## Comment 7

> Username: ckormanyos  
> Created at: 2023-01-22 12:09:21 UTC  
> Updated at: 2023-01-22 12:11:37 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/523#issuecomment-1399467442  

> OMG that's horrible!  
  
Affirmative.  
  
> please do go ahead and fix. `test_cos.cpp` has the same issue. I would make the loop variable signed and cast to unsigned for the table lookup only if that warns, but anything is better than where it is now frankly  
  
Understood. I will comply and do this. I'm going to go the `signed` `long` `long` way. I might put a slight asymmetry about the origin with `+/- 1/3` (or similar) as well.  
  
Also it is only needed to calculate `euler_gamma` once per instantiation (not in the loop from string). So that helps a tiny bit.  
  
I'll fix both `sin()` as well as `cos()` tests and have them running through hopefully by tomorrow, definitely for 1.82.  
  
Always a pleasure improving this body of work with you, John (@jzmaddock). Good advice!  
  
Cc: @mborland and @sinandredemption
