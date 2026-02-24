# #626 - Bug: Inconsistent Underflow Behavior for Unsigned Integers Greater Than 128 Bits [Closed]

> Username: itamarcps  
> Created at: 2024-06-20 20:02:34 UTC  
> Updated at: 2024-06-24 08:12:04 UTC  
> Closed at: 2024-06-24 08:12:04 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/626  

### Description:  
  
We have encountered inconsistent behavior when performing underflow operations on unsigned integers greater than 128 bits using Boost Multiprecision with `cpp_int_check_type::checked`. This issue does not occur with 128-bit numbers but starts failing for 136-bit numbers and above.  
  
### Steps to Reproduce:  
  
1. Define unsigned integer types with various bit sizes using Boost Multiprecision with unsigned_magnitude and checked types.  
2. Perform a subtraction operation that causes an underflow.  
  
### Sample Code:  
  
```cpp  
#include <iostream>  
#include <boost/multiprecision/cpp_int.hpp>  
  
template <unsigned int N>  
void test_underflow() {  
    using uint_t = boost::multiprecision::number<boost::multiprecision::cpp_int_backend<N, N, boost::multiprecision::unsigned_magnitude, boost::multiprecision::cpp_int_check_type::checked, void>>;  
      
    uint_t value = 0;  
    std::cout << "Testing underflow for uint" << N << "_t..." << std::endl;  
    std::cout << "Value before: " << value << std::endl;  
    try {   
        value -= 1;   
    } catch (const std::exception& e) {   
        std::cout << e.what() << std::endl;   
    }  
    std::cout << "Value after: " << value << std::endl;  
    if (value != 0) {  
        std::cout << "Something wrong with size: " << N << std::endl;  
    }  
}  
  
template <unsigned int N>  
struct TestUnderflow {  
    static void run() {  
        test_underflow<N>();  
        TestUnderflow<N + 8>::run();  
    }  
};  
  
template <>  
struct TestUnderflow<1032> {  
    static void run() {}  
};  
  
int main() {  
    TestUnderflow<8>::run();  
    return 0;  
}  
```  
  
### Sample Code Output:  
  
```  
...  
Testing underflow for uint120_t...  
Value before: 0  
Subtraction resulted in a negative value, but the type is unsigned  
Value after: 0  
Testing underflow for uint128_t...  
Value before: 0  
Subtraction resulted in a negative value, but the type is unsigned  
Value after: 0  
Testing underflow for uint136_t...  
Value before: 0  
Attempt to negate an unsigned number.  
Value after: 1                                      <------ Unexpected Value, Should be 0  
Somethings wrong with size: 136  
Testing underflow for uint144_t...  
Value before: 0  
Attempt to negate an unsigned number.  
Value after: 1                                      <------ Unexpected Value, Should be 0  
...  
```  
  
  
### Observed Behavior  
  
- For `uint128_t` or smaller numbers, the subtraction operation correctly throws an exception: "Subtraction resulted in a negative value, but the type is unsigned" and the value remains `0`.  
- For `uint136_t` and larger, the subtraction operation throws an **different** exception: "Attempt to negate an unsigned number" but the value becomes 1, which is unexpected.  
  
### Expected Behavior:   
  
The value should remain `0` after the exception is caught, indicating that the underflow operation was unsuccessful.  
  
### Additional Information  
  
- We are using Boost Multiprecision with cpp_int_check_type::checked.  
- This issue starts occurring for numbers greater than 128 bits (136, 144, 152, etc.).  
  
### Reference to previous bug  
  
It seems that this bug and the previous bug (#624) might be related due to the following similarities:  
  
- Both issues only occur with numbers larger than 128 bits.  
- The errors printed by std::exception are different depending on the size, indicating that **different templates** are likely being picked by the compiler.  
- The previous bug involved a segmentation fault during modulus operations on zero with negative **signed** numbers greater than 128 bits, while this bug involves inconsistent behavior during underflow operations on **unsigned** integers greater than 128 bits.  
  
This suggests that other bugs might be hiding under different templates.

---

## Comment 1

> Username: itamarcps  
> Created at: 2024-06-20 20:47:11 UTC  
> Updated at: 2024-06-21 00:04:37 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/626#issuecomment-2181536784  

Out of curiosity, I decided to go ahead and test overflow conditions too. It also fails with inconsistent behavior. See the sample code:  
  
### Sample Code  
  
```cpp  
#include <iostream>  
#include <boost/multiprecision/cpp_int.hpp>  
#include <limits>  
  
template <unsigned int N>  
void test_overflow() {  
    using uint_t = boost::multiprecision::number<boost::multiprecision::cpp_int_backend<N, N, boost::multiprecision::unsigned_magnitude, boost::multiprecision::cpp_int_check_type::checked, void>>;  
      
    uint_t value = std::numeric_limits<uint_t>::max();  
    std::cout << "Testing overflow for uint" << N << "_t..." << std::endl;  
    std::cout << "Value before: " << value << std::endl;  
    try {  
        value += 1;  
    } catch (const std::exception& e) {  
        std::cout << e.what() << std::endl;  
    }  
    std::cout << "Value after: " << value << std::endl;  
    if (value != std::numeric_limits<uint_t>::max()) {   
        std::cout << "Something wrong with size: " << N << std::endl;  
    }  
}  
  
template <unsigned int N>  
struct TestOverflow {  
    static void run() {  
        test_overflow<N>();  
        TestOverflow<N + 8>::run();  
    }  
};  
  
template <>  
struct TestOverflow<1032> {  
    static void run() {}  
};  
  
int main() {  
    TestOverflow<8>::run();  
    return 0;  
}  
```  
  
### Output  
  
```  
... OTHER NUMBERS ...  
Testing overflow for uint64_t...  
Value before: 18446744073709551615  
overflow in addition  
Value after: 18446744073709551615  
Testing overflow for uint72_t...  
Value before: 4722366482869645213695  
Overflow in cpp_int arithmetic: there is insufficient precision in the target type to hold all of the bits of the result.  
Value after: 4722366482869645213696  
Something wrong with size: 72  
... OTHER NUMBERS ...  
Testing overflow for uint128_t...  
Value before: 340282366920938463463374607431768211455  
overflow in addition  
Value after: 340282366920938463463374607431768211455  
Testing overflow for uint136_t...  
Value before: 87112285931760246646623899502532662132735  
Overflow in cpp_int arithmetic: there is insufficient precision in the target type to hold all of the bits of the result.  
Value after: 87112285931760246646623899502532662132736  
Something wrong with size: 136  
... OTHER NUMBERS...  
Testing overflow for uint192_t...  
Value before: 6277101735386680763835789423207666416102355444464034512895  
Unable to allocate sufficient storage for the value of the result: value overflows the maximum allowable magnitude.  
Value after: 0  
... OTHER NUMBERS ...  
```  
  
It essentially fails for every bit size, with the following exceptions:  
  
- Every number with a bit size of 64 or lower  
- 128-bit size  
  
Interestingly, any bit size that is a multiple of 64 and greater than 128 (e.g., 192, 256, 320, etc.) returns 0 after an overflow attempt.  
  
For other bit sizes greater than 64, the overflow operation throws an exception as expected but then incorrectly increments the value, causing it to exceed std::numeric_limits<T>::max().  
  
These combined bugs point to a more significant underlying issue within the library. The inconsistent behavior when overflowing and underflowing values, along with different error messages depending on the bit size, suggests that the compiler might be selecting different templates based on the bit size. This discrepancy could be causing these issues, and other similar bugs might be hidden due to this underlying problem. Please investigate these discrepancies thoroughly.

---

## Comment 2

> Username: jzmaddock  
> Created at: 2024-06-21 16:17:53 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/626#issuecomment-2183051641  

You are correct that two different templates are used: one for the regular multiprecision case, and one for the "trivial" case where the number of bits requested will fit inside a `std::uintmax_t`.  Both cases are run through our tests, but it looks like I forgot the corner cases where an error was raised... will investigate.

---

## Comment 3

> Username: jzmaddock  
> Created at: 2024-06-21 18:22:19 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/626#issuecomment-2183235679  

I haven't looked at the overflow case yet, but the underflow case appears to NOT be a bug, ie given:  
  
```  
x -= y  
```  
  
Then a `range_error` is raised for all types, and the result stored in x afterwards is indeterminate.  This is deliberate: computing `x -= y` destroys the value stored in `x` and at some point we realise the result is negative and bail out.  It is possible with a little additional logic to fix this specific case, but consider:  
  
```  
z = x - y  
```  
  
If expression templates are off, then `z` remains unchanged and a range_error is raised.  But if expression templates are on, then z is used as "workspace" for the calculation and it's contents get destroyed before the exception is thrown.  
  
I'm sure there are other corner cases, those are just the ones I can think of.  So in short, if an operation raises an exception, then the value of the target operand cannot be relied upon afterwards.  I don't believe this is totally unreasonable.  Indeed, one could argue that:  
  
```  
x -= y;  
```  
  
should leave x unchanged in the event of an exception.  But that would be rather runtime expensive to implement.

---

## Comment 4

> Username: itamarcps  
> Created at: 2024-06-21 19:09:47 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/626#issuecomment-2183309917  

Thank you for the detailed explanation. I have a few follow-up questions and points to consider:  
  
> Then a range_error is raised for all types, and the result stored in x afterwards is indeterminate.  
  
Is this behavior explicitly defined in the Boost Multiprecision documentation? I couldn't find any mention of this specific behavior. Clear documentation would help users understand the expected behavior and design their code accordingly.  
  
>    I don't believe this is totally unreasonable. Indeed, one could argue that:  
>  
>    x -= y;  
>  
>    should leave x unchanged in the event of an exception. But that would be rather runtime expensive to implement.  
  
While I understand the concern about runtime performance, it is important to consider the expectations of users who choose `cpp_int_check_type::checked`. These users likely prioritize correctness and safety over performance. They would expect that operations on these types be exception-safe, ensuring that the state of their variables remains consistent and reliable after an exception is thrown.  
  
Exceptions are inherently expensive, but their use implies a focus on handling error conditions properly. In environments where `cpp_int_check_type::checked` is used, it is reasonable to assume that users are willing to accept this overhead in exchange for the guarantee that their variables will remain in a valid state, even when an error occurs.
