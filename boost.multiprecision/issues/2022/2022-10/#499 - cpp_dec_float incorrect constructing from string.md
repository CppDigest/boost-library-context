# #499 - cpp_dec_float incorrect constructing from string [Closed]

> Username: yaroslavchahovets  
> Created at: 2022-10-05 18:05:05 UTC  
> Updated at: 2023-02-16 17:02:06 UTC  
> Closed at: 2023-02-16 17:02:06 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/499  

When constructing **cpp_dec_float** from certain string values which don't represent correct numbers, we expect that the exception is thrown, but the object is successfully constructed:  
  
```  
#include <iostream>  
#include <boost/multiprecision/cpp_dec_float.hpp>  
  
using boost::multiprecision::cpp_dec_float_50;  
  
int main()  
{  
    std::cout.precision(10);  
  
    cpp_dec_float_50 val1{"12a3.4"};  
    std::cout << "no exception. val1=" << val1 << "\n";  
  
    cpp_dec_float_50 val2{"1.2a34"};  
    std::cout << "no exception. val2=" << val2 << "\n";  
  
    return 0;  
}  
  
Output:  
no exception. val1=12.4  
no exception. val2=1.00000002  
```  
This bug is reproduced on Boost 1.79.0 and 1.80.0. Also it is reproduced on current repository https://github.com/boostorg/boost.git (as of 2022-10-05). The behaviour is correct on Boost 1.78.0. Compilers: gcc 9.4.0 and gcc 10.3.0.

---

## Comment 1

> Username: ckormanyos  
> Created at: 2022-10-06 10:32:32 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/499#issuecomment-1269774005  

> When constructing `cpp_dec_float` from certain string values which don't represent correct numbers, we expect that the exception is thrown, but the object is successfully constructed  
  
OK. Thanks for reporting this, @yaroslavchahovets.  
  
> bug is reproduced  
  
I'm not sure about the term _bug_ in this case. We simply need to define the proper behavior.  
  
> The behaviour is correct on Boost 1.78.0  
  
What do you think would be the correct behavior? Throw an exceptuion seems to be agreed. What value would be expected. I might prefer NaN.  
  
Thoughts?  
  
Cc: @jzmaddock

---

## Comment 2

> Username: yaroslavchahovets  
> Created at: 2022-10-06 11:43:36 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/499#issuecomment-1269879130  

> What do you think would be the correct behavior? Throw an exceptuion seems to be agreed.  
  
I think that correct behavior is to do what boost_multiprecision up to version 1_78_0 did in this case: throw **std::runtime_error** with explanatory string (what() return value) `Unable to parse the string "<string_argument>" as a floating point value.`  
For the example program above: throw **std::runtime_error** with explanatory string `Unable to parse the string "12a3.4" as a floating point value.`  
  
By the way, the code which do exactly this is in the source code in https://github.com/boostorg/multiprecision/blob/c6794e5638f9bf942eafe33da0f8940649696674/include/boost/multiprecision/cpp_dec_float.hpp#L2383-L2386

---

## Comment 3

> Username: jzmaddock  
> Created at: 2022-10-06 11:56:18 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/499#issuecomment-1269898561  

>I think that correct behavior is to do what boost_multiprecision up to version 1_78_0 did in this case: throw std::runtime_error with explanatory string (what() return value) Unable to parse the string "<string_argument>" as a floating point value.  
  
I agree, we've introduced a bug somewhere.

---

## Comment 4

> Username: yaroslavchahovets  
> Created at: 2022-10-06 12:03:44 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/499#issuecomment-1269910622  

With the another argument (in this case it should not start from decimal digit) the exception is thrown but the problem (in my opinion) is with its type and explanatory string:  
```  
#include <iostream>  
#include <boost/multiprecision/cpp_dec_float.hpp>  
  
using boost::multiprecision::cpp_dec_float_50;  
  
int main()  
{  
    try {  
        cpp_dec_float_50 val{"wrong"};  
        std::cout << "no exception. val=" << val << "\n";  
    } catch (const std::runtime_error& error) {  
        std::cout << "std::runtime_error. what():" << error.what() << "\n";  
    } catch (const std::invalid_argument& error) {  
        std::cout << "std::invalid_argument. what():" << error.what() << "\n";  
    }  
  
    return 0;  
}  
  
Output:  
std::invalid_argument. what():stol  (boost ver 1.79.0 - present)  
std::runtime_error. what():Unable to parse the string "wrong" as a floating point value. (boost ver 1.76.0 - 1.78.0)  
```  
I think that correct behavior is what versions up to 1.78.0 did.

---

## Comment 5

> Username: jzmaddock  
> Created at: 2022-10-06 12:36:37 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/499#issuecomment-1269963687  

@ckormanyos @mborland Looks like we introduced this when lexical_cast usage was removed.

---

## Comment 6

> Username: yaroslavchahovets  
> Created at: 2022-10-06 12:36:55 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/499#issuecomment-1269964130  

> I agree, we've introduced a bug somewhere.  
  
I think the bug is introduced in ver.1.79.0 by this commit: https://github.com/boostorg/multiprecision/commit/f1fa6e8c2e412443557a3fec971e7725bd1417af  
Specifically these changes:  
```  
     // First get the digits to the left of the decimal point...  
-     data[0u] = boost::lexical_cast<std::uint32_t>(str.substr(static_cast<std::size_t>(0u), pos));  
+     data[0u] = static_cast<std::uint32_t>(std::stol(str.substr(static_cast<std::size_t>(0u), pos)));  
```  
and  
```  
-         data[i + 1u] = boost::lexical_cast<std::uint32_t>(std::string(it, it + static_cast<std::string::size_type>(cpp_dec_float_elem_digits10)));  
+         data[i + 1u] = static_cast<std::uint32_t>(std::stol(std::string(it, it + static_cast<std::string::size_type>(cpp_dec_float_elem_digits10))));  
```

---

## Comment 7

> Username: yaroslavchahovets  
> Created at: 2022-10-06 12:54:45 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/499#issuecomment-1269992204  

When lexical_cast was used, it throwed **bad_lexical_cast** exception for all cases which I reported in previous posts ("12a3", "2a34", "wrong") and this exception was catched in   
```  
   catch (const bad_lexical_cast&)  
   {  
      // Rethrow with better error message:  
      std::string msg = "Unable to parse the string \"";  
      msg += s;  
      msg += "\" as a floating point value.";  
      throw std::runtime_error(msg);  
   }  
```  
So **std::runtime_error** is finally thrown (and this is what is expected).  
  
With these changes (where  `std::stol` is used) the code not produce error on "12a3", "2a34" (which is wrong).  
For  string "wrong" it throws different exception - **std::invalid_argument**, but it is not catched in the boost code (which is wrong), so it propagates to the client code.

---

## Comment 8

> Username: yaroslavchahovets  
> Created at: 2022-10-06 13:13:37 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/499#issuecomment-1270023503  

It is possible to use std::stol and still have correct behavior. For this we can use the second parameter of std::stol. For the block which processes the symbols before decimal point:  
```  
-      data[0u] = static_cast<std::uint32_t>(std::stol(str.substr(static_cast<std::size_t>(0u), pos)));  
+      std::size_t num_processed;  
+      data[0u] = static_cast<std::uint32_t>(std::stol(str.substr(static_cast<std::size_t>(0u), pos), &num_processed));  
+      if(num_processed != pos){  
+           throw <some exception which later be catched and rethrown>;  
+      }  
```  
Same logic can be used for the processing of symbols after the decimal point.  
  
And also catch clauses should be added to the block which prepares and rethrows the final exception.

---

## Comment 9

> Username: ckormanyos  
> Created at: 2022-10-06 13:28:24 UTC  
> Updated at: 2022-10-06 13:29:07 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/499#issuecomment-1270049201  

OK. This is all starting to make sense now. Thanks for the great info and suggestions, folks.  
  
I can start taking a look at this.  
  
Hi @mborland I might need some help and if it's OK, I'll call upon you if needed?  
  
Cc: @jz and @yaroslavchahovets

---

## Comment 10

> Username: mborland  
> Created at: 2022-10-12 17:42:04 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/499#issuecomment-1276525342  

Should be fixed in https://github.com/boostorg/multiprecision/pull/502. `boost::lexical_cast` was replaced with `std::stol`/`std:atol`, but we were still trying to catch a `bad_lexcial_cast` instead of a `std::exception`.

---

## Comment 11

> Username: yaroslavchahovets  
> Created at: 2022-12-04 17:57:15 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/499#issuecomment-1336478845  

@jzmaddock Please consider integrating the fix #502 into the main repository
