# #56 Construct string with iterators [Closed]

> Username: camerbam  
> Created at: 2022-05-11 17:02:29 UTC  
> Updated at: 2022-09-01 15:30:51 UTC  
> Closed at: 2022-09-01 15:30:51 UTC  
> Url: https://github.com/boostorg/dll/pull/56  

Using iterators to construct the string instead of using a char* which depends on a null character being at the end.  
The reason this came up is because of a static code analyzer flagged this and said that this depends on the null character being there. I am sure that it is working just fine, but that the iterators are just slightly better (and a little easier to read in my opinion). I would like to know what you think of this change though, hence why I am making a pull. This way you can see it and tell me why it is a bad idea. Or if you do like it, then you can accept the pull.

---

## Comment 1

> Username: apolukhin  
> Created_at: 2022-09-01 15:30:50 UTC  
> Url: https://github.com/boostorg/dll/pull/56#issuecomment-1234444088  

Thanks for the PR and for the interest!  
  
Alas, the code is not right. `text.end()` is the end of the whole String Table, that contains multiple `\0` in the middle https://docs.oracle.com/cd/E19683-01/816-1386/6m7qcoblj/index.html#chapter6-73709 . When you do range construction from a pair of iterators, the whole content goes into the std::string, including entries for other symbols and `\0`. There's no information on symbol name length, so we have to relay on zero termination. For the case of malformed Elf sections there's an additional '\0' added to the end of the symbols table:  
https://github.com/boostorg/dll/blob/edf277aaf65f697c5e46452953daff007e188dff/include/boost/dll/detail/elf_info.hpp#L189

---
