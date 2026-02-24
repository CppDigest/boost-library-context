# #188 - Initializing multiprecision types in `std::array`s [Open]

> Username: NAThompson  
> Created at: 2020-01-28 02:09:02 UTC  
> Updated at: 2020-01-28 17:02:25 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/188  

In the following code:  
  
```  
#include <boost/multiprecision/cpp_bin_float.hpp>  
#include <array>  
  
using boost::multiprecision::cpp_bin_float_oct;  
int main() {  
    std::array<cpp_bin_float_oct, 4> a{"0.482962913144534143374871599864448683816952419504202275201171538155211606550",   
                                         "0.836516303737807905575293780916873203459370388348439293495341472652894725147",  
                                         "0.224143868042013381025972762240400355467883518184271761387168330840154631777",  
                                         "-0.129409522551260381174449418812024164174534450659965256907001603657528486820"};  
  
    cpp_bin_float_oct b{"0.482962913144534143374871599864448683816952419504202275201171538155211606550"};  
    std::cout << b << "\n";  
  
}  
```  
  
There is no problem initializing `b`, but initializing `a` causes a problem:  
  
```  
oct_test.cpp:6:40: error: could not convert '"0.482962913144534143374871599864448683816952419504202275201171538155211606550"' from 'const char [78]' to 'boost::multiprecision::number<boost::multiprecision::backends::cpp_bin_float<237, boost::multiprecision::backends::digit_base_2, void, int, -262142, 262143>, boost::multiprecision::et_off>'  
    6 |     std::array<cpp_bin_float_oct, 4> a{"0.482962913144534143374871599864448683816952419504202275201171538155211606550",  
      |                                        ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
      |                                        |  
      |                                        const char [78]  
compilation terminated due to -Wfatal-errors.  
```  
  
What is the best way to initialize a `std::array` with multiprecision types?

---

## Comment 1

> Username: ram-nad  
> Created at: 2020-01-28 07:02:25 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/188#issuecomment-579108915  

Sir, according to [this answer](https://stackoverflow.com/questions/48742738/initializing-array-through-explicit-constructor) given in stackoverflow and by the rules of aggregate initialization, explicit constructors are not considered for initializing member objects. So, for initializing with strings we may need a helper function as given in the answer.

---

## Comment 2

> Username: jzmaddock  
> Created at: 2020-01-28 11:59:51 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/188#issuecomment-579212293  

Yeh that sucks, but I think that constructor really should be explicit on the whole.  So that means each string has to be cast to a cpp_bin_float_oct I'm afraid.  
  
Longer term, now that we're starting to get better constexpr support, it might be good to support user-defined literals for cpp_bin_float.

---

## Comment 3

> Username: ram-nad  
> Created at: 2020-01-28 12:02:32 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/188#issuecomment-579213279  

Sir, but can't we have a simple template function which takes this values and converts it to given number type to be initialized in the std::array.

---

## Comment 4

> Username: jzmaddock  
> Created at: 2020-01-28 12:08:25 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/188#issuecomment-579215417  

>but can't we have a simple template function which takes this values and converts it to given number type to be initialized in the std::array.  
  
We have one already - it's the explicit constructor, I don't see that:  
  
```  
make_cpp_bin_float_oct("1234")  
```  
Is better than:  
```  
cpp_bin_float_oct("1234");  
```

---

## Comment 5

> Username: ram-nad  
> Created at: 2020-01-28 12:10:29 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/188#issuecomment-579216100  

No sir, something like mentioned in this answer: https://stackoverflow.com/questions/48742738/initializing-array-through-explicit-constructor. It will make initializing array's easier.

---

## Comment 6

> Username: jzmaddock  
> Created at: 2020-01-28 16:36:06 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/188#issuecomment-579339423  

>No sir, something like mentioned in this answer: https://stackoverflow.com/questions/48742738/initializing-array-through-explicit-constructor. It will make initializing array's easier.  
  
That's actually a pretty cunning solution - I like it!

---

## Comment 7

> Username: ram-nad  
> Created at: 2020-01-28 17:02:24 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/188#issuecomment-579352056  

Sir, can I start working on adding this to the library?
