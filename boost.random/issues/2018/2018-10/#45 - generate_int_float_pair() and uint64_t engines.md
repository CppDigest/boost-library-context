# #45 - generate_int_float_pair() and uint64_t engines [Open]

> Username: Fuzzier  
> Created at: 2018-10-02 03:22:33 UTC  
> Updated at: 2020-09-22 10:24:02 UTC  
> Url: https://github.com/boostorg/random/issues/45  

When a random engine with a 'result_type' of 'uint64_t' is fed to exponential_distribution::operator()(), MSVC 2010 generates lots of warnings.  
The key warning complains that:  
warning C4244: '=' : conversion from 'base_unsigned' to 'int', possible loss of data.  
  
Cause of the issue:  
In random/detail/int_float_pair.hpp, at line 65, a local variable 'bucket' is defined as an 'int'.  
At line 68, 'generate_one_digit()' returns a value of type 'base_unsigned' (actually 'uint64_t').  
At line 69, the value joins certain calculation, and the result value of type 'uint64_t' is assigned to 'bucket', which is a 64-bit to 32-bit conversion, and issues the warning.  
At line 77, there is the same problem.  
  
Suggestions:  
At line 67, there is a 'for' loop. However, if 'base_unsigned' has 64 digits, 'm' is 64, and the loop will never run.  
At line 69, it should be safe to cast the result of the calculation into 'int' before it is assigned to 'bucket'.  
i.e., `bucket = static_cast<int>((bucket << m) | u);`  
At line 77, `bucket = static_cast<int>((bucket << (w%m)) | (mask & u));`

---

## Comment 1

> Username: Vishal-Mahor  
> Created at: 2020-09-22 10:24:02 UTC  
> Url: https://github.com/boostorg/random/issues/45#issuecomment-696635076  

I want to workon this issue, plz assign a mentership to me to do work on it.
