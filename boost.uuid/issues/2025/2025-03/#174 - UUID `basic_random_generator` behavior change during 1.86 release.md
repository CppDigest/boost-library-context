# #174 - UUID `basic_random_generator` behavior change during 1.86 release [Closed]

> Username: corpoverlords  
> Created at: 2025-03-13 22:25:34 UTC  
> Updated at: 2025-06-28 23:01:21 UTC  
> Closed at: 2025-06-28 22:58:30 UTC  
> Url: https://github.com/boostorg/uuid/issues/174  

Hello,  
  
The following code:  
```cxx  
#include <boost/beast/core/detail/base64.hpp>  
#include <boost/uuid/random_generator.hpp>  
#include <iostream>  
#include <random>  
  
// Type your code here, or load an example.  
int main() {  
    std::mt19937_64 rng(0);  
  
    boost::uuids::basic_random_generator<std::mt19937_64> uuid_gen{rng};  
  
    auto generated = uuid_gen();  
    auto enc_max = boost::beast::detail::base64::encoded_size(16);  
    std::string encoded(" ", enc_max);  
    auto buffer = std::string(generated.begin(), generated.end());  
    boost::beast::detail::base64::encode(  
        (void*)encoded.c_str(), (const void*)buffer.c_str(), buffer.size());  
    std::cout << encoded << "\n";  
}  
```  
  
Generated  
```  
PtxBy8U3SCiL+UA+fDr9/Q==  
```  
for Boost < 1.86  
  
But after 1.86 it generated  
```  
xTfoKHw6Tf2XMiEKA2n1mA==  
```  
  
which is not listed in the changelog. Is there a way to restore the original behavior?

---

## Comment 1

> Username: pdimov  
> Created at: 2025-06-28 23:01:21 UTC  
> Url: https://github.com/boostorg/uuid/issues/174#issuecomment-3016136068  

I don't think `basic_random_generator` was ever supposed to give reproducible results. Nevertheless, in https://github.com/boostorg/uuid/commit/95855404d2e672d123e2f6e8577ac6f75493d531 I have applied a fix that should address your issue. It also makes the results consistent and portable; let's hope nobody else objects because they were relying on some other value under some other platform. :-)
