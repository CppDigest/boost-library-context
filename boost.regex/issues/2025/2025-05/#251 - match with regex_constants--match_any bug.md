# #251 - match with regex_constants::match_any bug [Open]

> Username: zmysysz  
> Created at: 2025-05-23 07:29:28 UTC  
> Updated at: 2025-05-26 04:05:53 UTC  
> Url: https://github.com/boostorg/regex/issues/251  

![Image](https://github.com/user-attachments/assets/abee81e0-f3c8-4d85-860a-47cda93b06b6)  
  
In multiple overloads of regex_match, regex_constants::match_any is enabled by default, which can cause a drastic drop in matching performance (by a factor of tens to hundreds). This appears to be a bug in either the implementation or the way the function is invoked.

---

## Comment 1

> Username: jzmaddock  
> Created at: 2025-05-23 07:36:19 UTC  
> Url: https://github.com/boostorg/regex/issues/251#issuecomment-2903550798  

You're going to have to give me a concrete example: the whole point of `match_any` is to *improve* performance, as in "I don't care what the match is, just stop when you find one".

---

## Comment 2

> Username: zmysysz  
> Created at: 2025-05-23 07:59:24 UTC  
> Url: https://github.com/boostorg/regex/issues/251#issuecomment-2903607573  

![Image](https://github.com/user-attachments/assets/cca03701-6935-4e43-89c9-32ec54df2f3a)  
  
![Image](https://github.com/user-attachments/assets/63a863aa-888d-4393-bf75-434757408570)  
  
Yes, the original intent of match_any was probably to improve performance, potentially by allowing optimizations such as non-greedy matching. However, those optimizations are more appropriate for regex_search. In contrast, regex_match is expected to perform a full match on the entire input string — a strict yes-or-no result. If performance improvements are needed, the implementation could, for example, short-circuit trailing .*. But in practice, this flag (match_any) clearly has the opposite effect, significantly degrading performance.

---

## Comment 3

> Username: jzmaddock  
> Created at: 2025-05-23 11:17:16 UTC  
> Url: https://github.com/boostorg/regex/issues/251#issuecomment-2904102533  

Ca you please provide a specific example of a string and regex where matching is slower?

---

## Comment 4

> Username: zmysysz  
> Created at: 2025-05-26 03:58:23 UTC  
> Updated at: 2025-05-26 04:05:52 UTC  
> Url: https://github.com/boostorg/regex/issues/251#issuecomment-2908446771  

Sure, you can easily reproduce it with a random example.  
```  
#include <iostream>  
#include <string>  
#include <boost/regex.hpp>  
#include <chrono>  
#include <boost/version.hpp>  
  
constexpr size_t ITERATIONS = 10'000'000;  
constexpr size_t STRING_SIZE = 500;  
  
int main() {  
    // Create a string of approximately 1KB with a matchable pattern at the end  
    std::string sample = "AAAAAAAAAAXYZ1234567" + std::string(STRING_SIZE, 'A');  
    std::cout << "Boost version (numeric): " << BOOST_VERSION << std::endl;  
    std::cout << "Boost version (string): " << BOOST_LIB_VERSION << std::endl;  
    std::cout << "Sample str size: " << sample.size() << std::endl;  
    // Regular expression to match the end pattern  
    boost::regex pattern("^[A]{10}XYZ\\d{7}.*");  
    // ----------- Overload with match result (boost::smatch) -----------  
    {  
        auto start = std::chrono::steady_clock::now();  
        boost::smatch result;  
        size_t matched = 0;  
  
        for (size_t i = 0; i < ITERATIONS; ++i) {  
            if (boost::regex_match(sample, result, pattern)) {  
                ++matched;  
  
            }  
        }  
  
        auto end = std::chrono::steady_clock::now();  
        std::chrono::duration<double> elapsed = end - start;  
        std::cout << "[With result]   Time: " << elapsed.count() << "s, Matched times: " << matched << std::endl;  
    }  
  
    // ----------- Overload without match result -----------  
    {  
        auto start = std::chrono::steady_clock::now();  
        size_t matched = 0;  
  
        for (size_t i = 0; i < ITERATIONS; ++i) {  
            if (boost::regex_match(sample, pattern)) {  
                ++matched;  
            }  
        }  
  
        auto end = std::chrono::steady_clock::now();  
        std::chrono::duration<double> elapsed = end - start;  
        std::cout << "[Without result]     Time: " << elapsed.count() << "s, Matched: " << matched << std::endl;  
    }  
  
    return 0;  
}  
```  
With this results  
  
![Image](https://github.com/user-attachments/assets/043fd4c6-abf8-457b-a03f-941f91fc8fb7)
