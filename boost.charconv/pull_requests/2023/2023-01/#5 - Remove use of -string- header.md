# #5 Remove use of <string> header [Merged]

> Username: mborland  
> Created at: 2023-01-09 21:32:48 UTC  
> Updated at: 2023-01-10 17:15:20 UTC  
> Merged at: 2023-01-10 00:51:03 UTC  
> Closed at: 2023-01-10 00:51:03 UTC  
> Url: https://github.com/boostorg/charconv/pull/5  

Use std::strtol and std::strtoll for integer types to avoid bringing in string.

---

## Comment 1

> Username: pdimov  
> Created_at: 2023-01-10 00:58:39 UTC  
> Url: https://github.com/boostorg/charconv/pull/5#issuecomment-1376569497  

Note that the use of `std::string` was a stub, merely to make the test pass. In our real implementation we can't use `strtol` because it's locale-dependent, and `from_chars` is locale-independent.

---

## Comment 2

> Username: mborland  
> Created_at: 2023-01-10 16:33:27 UTC  
> Url: https://github.com/boostorg/charconv/pull/5#issuecomment-1377535186  

> Note that the use of `std::string` was a stub, merely to make the test pass. In our real implementation we can't use `strtol` because it's locale-dependent, and `from_chars` is locale-independent.  
  
The parsers will be [locale dependent](https://en.cppreference.com/w/cpp/utility/from_chars). It'll require pre-processing if not in the standard "C" locale

---

## Comment 3

> Username: pdimov  
> Created_at: 2023-01-10 17:02:48 UTC  
> Url: https://github.com/boostorg/charconv/pull/5#issuecomment-1377574816  

I don't understand. The specification of `from_chars` requires independence. Are you saying you won't implement that?

---

## Comment 4

> Username: mborland  
> Created_at: 2023-01-10 17:09:18 UTC  
> Url: https://github.com/boostorg/charconv/pull/5#issuecomment-1377582928  

from_chars_result from_chars(const char* first, const char* last, integer-type & value, int base = 10);  
  
1. Preconditions: base has a value between 2 and 36 (inclusive).  
  
2. Effects: The pattern is the expected form of the subject sequence in the "C" locale for the given nonzero base, as described for strtol, except that no "0x" or "0X" prefix shall appear if the value of base is 16, and except that ’-’ is the only sign that may appear, and only if value has a signed type.  
  
3. Throws: Nothing.  
  
I am saying that the parser itself requires a "C" locale so we would need to pre-process. I am not saying the user needs to do this.

---

## Comment 5

> Username: pdimov  
> Created_at: 2023-01-10 17:15:20 UTC  
> Url: https://github.com/boostorg/charconv/pull/5#issuecomment-1377591374  

Preprocess? We just need to parse it ourselves without using `strtol`, not preprocess.

---
