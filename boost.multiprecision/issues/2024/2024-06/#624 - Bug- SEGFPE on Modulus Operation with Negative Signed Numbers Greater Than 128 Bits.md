# #624 - Bug: SEGFPE on Modulus Operation with Negative Signed Numbers Greater Than 128 Bits [Closed]

> Username: itamarcps  
> Created at: 2024-06-18 22:41:51 UTC  
> Updated at: 2024-06-21 16:14:56 UTC  
> Closed at: 2024-06-21 16:14:55 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/624  

### Description:  
  
We have encountered a segmentation fault (SEGFPE) when performing the modulus operation on zero with negative signed numbers greater than 128 bits using Boost Multiprecision. This issue does not occur with 128-bit numbers.  
  
### Steps to Reproduce:  
  
1. Define int128_t and int129_t using Boost Multiprecision with signed_magnitude and checked types.  
2. Perform modulus operation with zero on a negative number.  
  
### Sample Code:  
  
```cpp  
#include <iostream>  
#include <boost/multiprecision/cpp_int.hpp>  
  
using int128_t = boost::multiprecision::number<boost::multiprecision::cpp_int_backend<128,128, boost::multiprecision::signed_magnitude, boost::multiprecision::cpp_int_check_type::checked, void>>;  
using int129_t = boost::multiprecision::number<boost::multiprecision::cpp_int_backend<129,129, boost::multiprecision::signed_magnitude, boost::multiprecision::cpp_int_check_type::checked, void>>;  
  
int main()  
{  
    int128_t ii = -42;  
    std::cout << "Trying int128_t..." << std::endl;  
    try { ii = ii % 0; } catch (const std::exception& e) { std::cout << "Exception: " << e.what() << std::endl; }  
    std::cout << "int128_t catched..." << std::endl;  
      
    int129_t i = -42;  
    std::cout << "Trying int129_t..." << std::endl;  
    try { i = i % 0; } catch (const std::exception& e) { std::cout << "Exception: " << e.what() << std::endl; }  
    std::cout << "int129_t catched..." << std::endl;  
}  
```   
  
  
### Observed Behavior:  
  
- For int128_t, the modulus operation throws an exception as expected: "Exception: Division by zero."  
- For int129_t, the modulus operation causes an illegal instruction, resulting in a core dump.  
  
### Expected Behavior:  
  
The modulus operation should throw an exception for both int128_t and int129_t.  
  
### Additional Information:  
  
- If we change the negative number to a positive number, the issue disappears.  
- We are using CHECKED Boost number types.  
- Replacing the modulus operator with a division works flawlessly but prints two different exceptions. See the expected output:  
  
```cpp  
#include <iostream>  
#include <boost/multiprecision/cpp_int.hpp>  
  
using int128_t = boost::multiprecision::number<boost::multiprecision::cpp_int_backend<128,128, boost::multiprecision::signed_magnitude, boost::multiprecision::cpp_int_check_type::checked, void>>;  
using int129_t = boost::multiprecision::number<boost::multiprecision::cpp_int_backend<129,129, boost::multiprecision::signed_magnitude, boost::multiprecision::cpp_int_check_type::checked, void>>;  
  
int main()  
{  
    int128_t ii = -42;  
    std::cout << "Trying int128_t..." << std::endl;  
    try { ii = ii / 0; } catch (const std::exception& e) { std::cout << "Exception: " << e.what() << std::endl; }  
    std::cout << "int128_t catched..." << std::endl;  
      
    int129_t i = -42;  
    std::cout << "Trying int129_t..." << std::endl;  
    try { i = i / 0; } catch (const std::exception& e) { std::cout << "Exception: " << e.what() << std::endl; }  
    std::cout << "int129_t catched..." << std::endl;  
}  
```  
  
**Expected Output**  
  
```  
Trying int128_t...  
Exception: Division by zero.  
int128_t catched...  
Trying int129_t...  
Exception: Integer Division by zero.  
int129_t catched...  
```  
  
It seems that the templates picked are different for the modulus and division operations depending on the bit size of the number. Please investigate this discrepancy and provide a fix.

---

## Comment 1

> Username: jzmaddock  
> Created at: 2024-06-19 16:17:09 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/624#issuecomment-2179071276  

Confirmed, will fix shortly...
