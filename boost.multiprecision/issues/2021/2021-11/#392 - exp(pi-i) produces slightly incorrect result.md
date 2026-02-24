# #392 - exp(pi*i) produces slightly incorrect result [Open]

> Username: mgeck64  
> Created at: 2021-11-18 04:33:09 UTC  
> Updated at: 2025-02-26 02:16:43 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/392  

Basically, exp(pi*i) where i is the imaginary unit should produce -1 but produces a slightly different   
result with Boost 1.77.0; however it produces the correct result with Boost 1.74.0.  The following program reproduces the issue:  
```  
#include <boost/multiprecision/cpp_complex.hpp>  
  
using CT = boost::multiprecision::cpp_complex_50;  
static const auto pi = boost::math::constants::pi<CT::value_type>();  
static const auto i  = CT(0, 1);  
  
int main() {  
    std::cout << exp(pi*i) << std::endl;  
    // Output when compiled with Boost 1.74.0 is -1 as expected.  
    // Output when compiled with Boost 1.77.0 is (-1,4.33483e-51).  
    // Note: The equivalent program using std::complex when run in compiler  
    // explorer produces a similar result: (-1,1.22465e-16). Don't know if this  
    // could be related.  
    // Note 2: My version of g++ under which this was compiled is 11.2.0.  
}  
```

---

## Comment 1

> Username: NAThompson  
> Created at: 2021-11-18 04:53:22 UTC  
> Updated at: 2021-11-18 04:56:50 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/392#issuecomment-972537176  

Since π is not representable, we must apply the rounding model of floating point arithmetic:  π̂=π(1+μ), where μ is the unit roundoff.  
  
Then:  
  
exp(iπ̂) = exp(iπ)exp(iμπ) = -cos(μπ) -isin(μπ)  ≈ -1 -iμπ,  
  
which is exactly as you observe.

---

## Comment 2

> Username: mgeck64  
> Created at: 2021-11-18 06:19:51 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/392#issuecomment-972572749  

But why would version 1.74.0 produce -1 (the desired result) but 1.77.0   
produce (-1,4.33483e-51)?  
  
On Wed, Nov 17, 2021 at 10:12 pm, Mark Geck ***@***.***> wrote:  
>   
>   
> On Wed, Nov 17, 2021 at 8:53 pm, Nick ***@***.***>   
> wrote:  
>> Since π is not representable, we must apply the rounding model of   
>> floating point arithmetic: π̂=π(1+μ), where μ is the unit   
>> roundoff.  
>>   
>> Then:  
>>   
>> exp(iπ̂) = exp(iπ)exp(iμπ) = -isin(μπ) ≈ -iμπ,  
>>   
>> which is exactly as you observe.  
>>   
>> —  
>> You are receiving this because you authored the thread.  
>> Reply to this email directly, view it on GitHub   
>> <https://github.com/boostorg/multiprecision/issues/392#issuecomment-972537176>,   
>> or unsubscribe   
>> <https://github.com/notifications/unsubscribe-auth/ARELBE7QBM7GXT2GVTW2R2LUMSBE3ANCNFSM5IIUZC5A>.  
>> Triage notifications on the go with GitHub Mobile for iOS   
>> <https://apps.apple.com/app/apple-store/id1477376905?ct=notification-email&mt=8&pt=524675>   
>> or Android   
>> <https://play.google.com/store/apps/details?id=com.github.android&referrer=utm_campaign%3Dnotification-email%26utm_medium%3Demail%26utm_source%3Dgithub>.  
>>

---

## Comment 3

> Username: mgeck64  
> Created at: 2021-11-18 09:35:21 UTC  
> Updated at: 2021-11-18 09:37:38 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/392#issuecomment-972692688  

More precisely stated, my question is why sin(pi) correctly evaluates to 0 in version 1.74 but not in 1.77. Note also 1.75 produces the same result as 1.77 so whatever changed happened between 1.74 and 1.75.  
  
Note also that google calculator and wolfram alpha evaluate sin(pi) to 0. In particular, wolfram alpha will evaluate sin(3.1415926535897932384626433832795028841971693993751) to 0.  
  
Is pi being treated specially in 1.74 and the other calculators?

---

## Comment 4

> Username: ckormanyos  
> Created at: 2021-11-18 11:32:15 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/392#issuecomment-972782628  

I made some investigations to give us some content for the discussion.  
  
We are dealing with finite-precision numerical representations and should discuss what makes sense for really small numbers.  
  
```C  
#include <boost/multiprecision/cpp_complex.hpp>  
  
using CT = boost::multiprecision::cpp_complex_50;  
using component_value_type = typename CT::value_type;  
static const auto pi = boost::math::constants::pi<component_value_type>();  
static const auto i  = CT(0, 1);  
  
int main()  
{  
  {  
    std::stringstream strm;  
  
    strm << std::setprecision(std::numeric_limits<component_value_type>::digits10)  
         << exp(pi*i)  
         << std::endl;  
  
    std::cout << strm.str() << std::endl;  
  }  
  
  {  
    std::stringstream strm;  
  
    strm << std::fixed  
         << std::setprecision(std::numeric_limits<component_value_type>::digits10)  
         << exp(pi*i)  
         << std::endl;  
  
    std::cout << strm.str() << std::endl;  
  }  
  
  {  
    std::stringstream strm;  
  
    strm << std::fixed  
         << exp(pi*i)  
         << std::endl;  
  
    std::cout << strm.str() << std::endl;  
  }  
}  
```

---

## Comment 5

> Username: jzmaddock  
> Created at: 2021-11-18 12:10:32 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/392#issuecomment-972808290  

I believe this is a result of addressing this issue: https://github.com/boostorg/multiprecision/issues/264 via: https://github.com/boostorg/multiprecision/pull/270  
  
Which means we now do extended precision argument reduction, so reducing an input of PI, no longer results in 0 but something very close to it.  I'm in two minds what to do here.

---

## Comment 6

> Username: IRainman  
> Created at: 2025-02-26 02:16:42 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/392#issuecomment-2683724698  

It's C++ double precision error (IEEE 754 compatible double precision binary floating-point, which consumes 8 bytes per number, and gives an effective precision of nearly 16 decimal digits, with exponents ranging from −308 to +308) sin(pi) = 1.2246467991473532e-16
