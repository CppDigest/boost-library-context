# #164 - Support "named" variadic macros [Open]

> Username: jefftrull  
> Created at: 2022-06-16 19:31:47 UTC  
> Updated at: 2024-09-08 16:16:20 UTC  
> Url: https://github.com/boostorg/wave/issues/164  

This piece of code appears in recent Ubuntu distributions, in `/usr/include/linux/stddef.h`:  
  
```c++  
#define __struct_group(TAG, NAME, ATTRS, MEMBERS...) \  
        union { \  
                struct { MEMBERS } ATTRS; \  
                struct TAG { MEMBERS } ATTRS NAME; \  
        }  
```  
  
The [gcc manual](https://gcc.gnu.org/onlinedocs/cpp/Variadic-Macros.html) notes that names other than `__VA_ARGS__` - in this case, `MEMBERS` - may be used to refer to the variadic arguments "as an extension". Given the ubiquity of this header it may be useful to add support for this extension.

---

## Comment 1

> Username: hkaiser  
> Created at: 2022-06-16 22:59:56 UTC  
> Url: https://github.com/boostorg/wave/issues/164#issuecomment-1158241482  

Isn't this dangerous/ambiguous? Wasn't the comma right before the `...` optional? I don't remember the details...

---

## Comment 2

> Username: jefftrull  
> Created at: 2022-06-16 23:09:29 UTC  
> Url: https://github.com/boostorg/wave/issues/164#issuecomment-1158245936  

It seems unambiguous to me. If there is some identifier token prior to the `...` we use that instead of `__VA_ARGS__`.  
  
Can you give an example?

---

## Comment 3

> Username: hkaiser  
> Created at: 2022-06-17 01:32:13 UTC  
> Url: https://github.com/boostorg/wave/issues/164#issuecomment-1158377498  

> It seems unambiguous to me. If there is some identifier token prior to the `...` we use that instead of `__VA_ARGS__`.  
>   
> Can you give an example?  
  
Most likely I'm misremembering things. Please feel free to implement this gcc extension if you feel it is valuable. However, as wave was always meant to be strictly conforming, I'd suggest enabling this feature only based on an explicit option the user has to provide. Wave could even mention this new option in the error message if it discovers the construct, but the option was not enabled.

---

## Comment 4

> Username: jwnhy  
> Created at: 2024-07-31 06:27:43 UTC  
> Url: https://github.com/boostorg/wave/issues/164#issuecomment-2259767012  

Is this still ongoing?  
  
I need this feature to compile kernel code and I'd like to help.  
But I am quite new to Wave too...  
Could anyone provide rough guidance about which file I should learn and modify to add this feature?

---

## Comment 5

> Username: jefftrull  
> Created at: 2024-07-31 19:20:21 UTC  
> Url: https://github.com/boostorg/wave/issues/164#issuecomment-2261259170  

Like many open source libraries, Wave progresses at a rate determined by the energy level and availability of its maintainers :) I think your contribution would be very welcome, particularly given the use of this syntax in the Linux kernel (a popular target of analysis)! I'd be happy to help you get involved.  
  
Given that this is a non-standard extension we would need to create a feature flag for it. We support certain MSVC extensions with a macro flag `BOOST_WAVE_SUPPORT_MS_EXTENSIONS` at compile time; another model would be to connect into the language support feature flags available at runtime in [this file](https://github.com/boostorg/wave/blob/develop/include/boost/wave/language_support.hpp). The latter might be more appropriate given that the lexer itself (probably) does not need to be modified.  
  
The actual code implementing variadic macros definition starts about [here](https://github.com/boostorg/wave/blob/46bd710ada96c642008a3e6bb387f6738376d758/include/boost/wave/util/cpp_iterator.hpp#L1714) while the expansion is handled [here](https://github.com/boostorg/wave/blob/46bd710ada96c642008a3e6bb387f6738376d758/include/boost/wave/util/cpp_macromap.hpp#L1401). You are looking for a way to conditionally use a different token (probably an identifier) with a specific name instead of  `__VA_ARGS__`. And some minimal unit tests would be required.

---

## Comment 6

> Username: jwnhy  
> Created at: 2024-09-08 16:16:19 UTC  
> Url: https://github.com/boostorg/wave/issues/164#issuecomment-2336741623  

@jefftrull Hi, thank you for the suggestion.  
  
I just opened up a PR to support this feature.  
  
Please let me know if anything is missing or wrong.
