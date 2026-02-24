# #38 - Parsing Failures in Corner Cases [Closed]

> Username: m8mble  
> Created at: 2020-02-10 18:21:58 UTC  
> Updated at: 2020-02-14 01:46:42 UTC  
> Closed at: 2020-02-14 01:46:42 UTC  
> Url: https://github.com/boostorg/json/issues/38  

I've benchmarked this JSON library with the [Native JSON Benchmark Suite][1]. This revealed a few interesting "Errors" in corner-cases. I'm not certain, whether the expected results are mandated by JSON, but wanted to share the findings nonetheless.  
  
## Doubles  
```  
* `[-0.0]`  
  * expect: `-0 (0x0168000000000000000)`  
  * actual: `0 (0x0160)`  
  
* `[2.22507e-308]`  
  * expect: `2.2250699999999998e-308 (0x016FFFFE2E8159D0)`  
  * actual: `2.2250700000295652e-308 (0x016FFFFE2E824391)`  
  
* `[-2.22507e-308]`  
  * expect: `-2.2250699999999998e-308 (0x016800FFFFE2E8159D0)`  
  * actual: `-2.2250700000295652e-308 (0x016800FFFFE2E824391)`  
  
* `[4.9406564584124654e-324]`  
  * expect: `4.9406564584124654e-324 (0x0161)`  
  * actual: `0 (0x0160)`  
  
* `[2.2250738585072009e-308]`  
  * expect: `2.2250738585072009e-308 (0x016FFFFFFFFFFFFF)`  
  * actual: `0 (0x0160)`  
  
* `[2.2250738585072014e-308]`  
  * expect: `2.2250738585072014e-308 (0x01610000000000000)`  
  * actual: `0 (0x0160)`  
  
* `[1e-10000]`  
  * expect: `0 (0x0160)`  
  * actual: `0 (0x0160)`  
  
* `[0.9868011474609375]`  
  * expect: `0.9868011474609375 (0x0163FEF93E000000000)`  
  * actual: `0.98680114746093761 (0x0163FEF93E000000001)`  
  
* `[2.2250738585072011e-308]`  
  * expect: `2.2250738585072009e-308 (0x016FFFFFFFFFFFFF)`  
  * actual: `0 (0x0160)`  
  
* `[1e-214748363]`  
  * expect: `0 (0x0160)`  
  * actual: `0 (0x0160)`  
  
* `[1e-214748364]`  
  * expect: `0 (0x0160)`  
  * actual: `0 (0x0160)`  
  
* `[0.017976931348623157e+310]`  
  * expect: `1.7976931348623157e+308 (0x0167FEFFFFFFFFFFFFF)`  
  * actual: `inf (0x0167FF0000000000000)`  
  
* `[2.2250738585072012e-308]`  
  * expect: `2.2250738585072014e-308 (0x01610000000000000)`  
  * actual: `0 (0x0160)`  
  
* `[2.22507385850720113605740979670913197593481954635164564e-308]`  
  * expect: `2.2250738585072009e-308 (0x016FFFFFFFFFFFFF)`  
  * actual: `0 (0x0160)`  
  
* `[2.22507385850720113605740979670913197593481954635164565e-308]`  
  * expect: `2.2250738585072014e-308 (0x01610000000000000)`  
  * actual: `0 (0x0160)`  
  
* `[0.999999999999999944488848768742172978818416595458984374]`  
  * expect: `0.99999999999999989 (0x0163FEFFFFFFFFFFFFF)`  
  * actual: `1 (0x0163FF0000000000000)`  
  
* `[1.00000000000000011102230246251565404236316680908203125]`  
  * expect: `1 (0x0163FF0000000000000)`  
  * actual: `1.0000000000000002 (0x0163FF0000000000001)`  
  
* `[1.00000000000000011102230246251565404236316680908203124]`  
  * expect: `1 (0x0163FF0000000000000)`  
  * actual: `1.0000000000000002 (0x0163FF0000000000001)`  
  
* `[7205759403792793199999e-5]`  
  * expect: `72057594037927928 (0x016436FFFFFFFFFFFFF)`  
  * actual: `72057594037927936 (0x0164370000000000000)`  
  
* `[10141204801825834086073718800384]`  
  * expect: `1.0141204801825834e+31 (0x016465FFFFFFFFFFFFF)`  
  * actual: `1.0141204801825835e+31 (0x0164660000000000000)`  
  
* `[1014120480182583464902367222169599999e-5]`  
  * expect: `1.0141204801825834e+31 (0x016465FFFFFFFFFFFFF)`  
  * actual: `1.0141204801825835e+31 (0x0164660000000000000)`  
  
* `[5708990770823839207320493820740630171355185152]`  
  * expect: `5.7089907708238395e+45 (0x0164970000000000000)`  
  * actual: `5.7089907708238389e+45 (0x016496FFFFFFFFFFFFF)`  
  
* `[5708990770823839207320493820740630171355185152001e-3]`  
  * expect: `5.7089907708238395e+45 (0x0164970000000000000)`  
  * actual: `5.7089907708238389e+45 (0x016496FFFFFFFFFFFFF)`  
  
* `[2.225073858507201136057409796709131975934819546351645648023426109724822222021076945516529523908135087914149158913039621106870086438694594645527657207407820621743379988141063267329253552286881372149012981122451451889849057222307285255133155755015914397476397983411801999323962548289017107081850690630666655994938275772572015763062690663332647565300009245888316433037779791869612049497390377829704905051080609940730262937128958950003583799967207254304360284078895771796150945516748243471030702609144621572289880258182545180325707018860872113128079512233426288368622321503775666622503982534335974568884423900265498198385487948292206894721689831099698365846814022854243330660339850886445804001034933970427567186443383770486037861622771738545623065874679014086723327636718751234567890123456789012345678901e-308]`  
  * expect: `2.2250738585072014e-308 (0x01610000000000000)`  
  * actual: `0 (0x0160)`  
```  
  
## Strings  
```  
* `["Hello\u0000World"]`  
  * expect: `"Hello\0World"` (length: 11)  
  * actual: `"Hello"` (length: 5)  
  
* `["\uD834\uDD1E"]`  
  * expect: `"𝄞"` (length: 4)  
  * actual: `"턞"` (length: 3)  
```  
  
------------------  
  
For the reference: The tests have been performed similarly to the following snippet:  
```cpp  
#define BOOST_JSON_HEADER_ONLY 1  
#include <boost/json.hpp>  
  
// [...]  
  
   namespace JSON = boost::json;  
   bool ParseDouble(const char* json, double* d) const {  
      try {  
         const auto root = JSON::parse(JSON::string_view{json});  
         const auto& element = root.get_array()[0];  
         *d = [&]() -> double  
         {  
            switch (element.kind())  
            {  
               case JSON::kind::double_:  
                  return element.get_double();  
               case JSON::kind::uint64:  
                  return element.get_uint64();  
               case JSON::kind::int64:  
                  return element.get_int64();  
               default:  
                  throw false;  
            }  
         }();  
         return true;  
      }  
      catch (...) {  
         return false;  
      }  
   }  
  
   bool ParseString(const char* json, std::string& s) const {  
      try {  
         const auto root = JSON::parse(JSON::string_view{json});  
         const auto& element = root.get_array()[0];  
         s = element.get_string().c_str();  
         return true;  
      }  
      catch (...) {  
         return false;  
      }  
   }  
```  
In case I did a testing-mistake, I'd be happy to repeat the exercise.  
  
[1]: https://github.com/miloyip/nativejson-benchmark

---

## Comment 1

> Username: madmongo1  
> Created at: 2020-02-10 18:50:44 UTC  
> Url: https://github.com/boostorg/json/issues/38#issuecomment-584291617  

@m8mble Thanks very much for the test cases. Most of the double parsing errors are a result of the fact that we don't yet implement the 'slow path' for correct rounding (parsing floating point from text accurately is hard in some corner cases).  
  
The string parsing errors look concerning. I'll take a look right away.

---

## Comment 2

> Username: madmongo1  
> Created at: 2020-02-11 15:06:06 UTC  
> Updated at: 2020-02-11 15:09:04 UTC  
> Url: https://github.com/boostorg/json/issues/38#issuecomment-584679930  

Findings:  
  
* The floating point issues are already known. There are design discusions in progress to determine whether we should provide an 'accurate but slow' mode for parsing floats. What you are observing is the effect of rounding errors in the conversion of base-10 representation of floating point to the base-2 representation required by IEEE 754  
  
* The `"Hello\u0000World"` problem was not reproducible (i.e. my new tests are passing)  
  
* The `["\uD834\uDD1E"]` finding is a real bug. I have created a PR which fixes it: https://github.com/vinniefalco/json/pull/40  
  
Thanks once again @m8mble for taking the time to write this issue (and helpfully providing test cases).  
  
Please don't hesitate to get in touch if you wish to discuss further.  
  
R

---

## Comment 3

> Username: pdimov  
> Created at: 2020-02-11 15:20:35 UTC  
> Url: https://github.com/boostorg/json/issues/38#issuecomment-584687376  

`s = element.get_string().c_str();` will stop at the first 0.

---

## Comment 4

> Username: m8mble  
> Created at: 2020-02-11 19:42:40 UTC  
> Url: https://github.com/boostorg/json/issues/38#issuecomment-584815576  

#40 indeed fixes the second string bug. And I can confirm, that the "Hello World" issue is a bug in my testing code. Thanks @pdimov for pointing this out.   
  
The following variant makes all tests pass:  
```  
s = std::string{element.get_string().subview()};  
```  
  
Thanks for your investigations and support. I'm looking very much forward to using this library more often, particularly because of the efficiency tradeoffs and the slim API.

---

## Comment 5

> Username: vinniefalco  
> Created at: 2020-02-11 20:43:35 UTC  
> Url: https://github.com/boostorg/json/issues/38#issuecomment-584841958  

C strings are C strings :)
