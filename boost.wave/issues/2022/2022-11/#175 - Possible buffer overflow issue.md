# #175 - Possible buffer overflow issue [Closed]

> Username: DavidCNou  
> Created at: 2022-11-07 15:00:17 UTC  
> Updated at: 2022-11-07 22:05:48 UTC  
> Closed at: 2022-11-07 21:04:16 UTC  
> Url: https://github.com/boostorg/wave/issues/175  

Discovered by scanning an application that uses boost::wave using Fortify, there is a possible buffer overflow issue at https://github.com/boostorg/wave/blob/58fd0bce3fa50129890072a5efb0a9815e649c2b/include/boost/wave/cpp_exceptions.hpp#L396-L398 . If `macroname` doesn't contain a null byte in the first 512 bytes, the referenced line will write outside the bounds of the `name` buffer. A similar problematic pattern was fixed in this file in version 1.59 at https://github.com/boostorg/wave/blob/58fd0bce3fa50129890072a5efb0a9815e649c2b/include/boost/wave/cpp_exceptions.hpp#L162-L164 by only iterating N-1 times, leaving the last byte free for the null byte.   
  
Given this is in exception handling code for macros, I'm not sure in what context this could be exploited; I'm not a security researcher. Even if the impact is low, I'd like to not have to keep explaining why it shows up on our static code scanning results (too bad we're using distro bundled packages from an older version, maybe a back-port patch is possible.)

---

## Comment 1

> Username: jefftrull  
> Created at: 2022-11-07 20:13:24 UTC  
> Url: https://github.com/boostorg/wave/issues/175#issuecomment-1306133878  

Thanks for the bug report; this seems correct to me. We have two days before the first code freeze and I will try to get this into 1.81.  
  
Would you be willing to share what you use Wave for?

---

## Comment 2

> Username: DavidCNou  
> Created at: 2022-11-07 20:54:40 UTC  
> Url: https://github.com/boostorg/wave/issues/175#issuecomment-1306177808  

Hi, no problem. We have a utility that generates code from IDL files (https://www.omg.org/spec/IDL/4.2/PDF) and uses boost::spirit, but since IDL allows for C preprocessor semantics, we use Wave to deal with that in a standards compliant way.

---

## Comment 3

> Username: jefftrull  
> Created at: 2022-11-07 20:58:47 UTC  
> Url: https://github.com/boostorg/wave/issues/175#issuecomment-1306182721  

Do you use the tokens produced by Wave directly (i.e. through something like Spirit Classic)?

---

## Comment 4

> Username: DavidCNou  
> Created at: 2022-11-07 22:05:48 UTC  
> Url: https://github.com/boostorg/wave/issues/175#issuecomment-1306284391  

> Do you use the tokens produced by Wave directly (i.e. through something like Spirit Classic)?  
  
I inherited this pretty old code (originally developed under boost 1.35) and it looks like the spirit grammar definition is built up using spirit parsers specialized by wave tokens e.g. `boost::wave::T_FLOAT`. There is a special case where `#include` macros require extra care and it uses the `found_include_directive` and `opened_include_file` hooks. No idea if there is a newer API but it works well enough for us.  
  
Thanks for accepting the pull.
