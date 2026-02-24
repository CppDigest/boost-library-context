# #149 [Fix] Define status codes directly [Closed]

> Username: Flamefire  
> Created at: 2020-06-09 10:41:33 UTC  
> Updated at: 2020-06-09 12:56:44 UTC  
> Closed at: 2020-06-09 11:43:14 UTC  
> Url: https://github.com/boostorg/filesystem/pull/149  

Avoids Wc++11-narrowing warnings on clang I'm getting during compilation which seems to be even an error (but that might be me using `-Werror` though not fully sure)  
  
> libs\filesystem\src\unique_path.cpp(86,8): error: case value evaluates to 3221225495, which cannot be narrowed to type 'boost::winapi::NTSTATUS_' (aka 'long') [-Wc++11-narrowing]  
 case 0xC0000017l: // STATUS_NO_MEMORY  
  
This avoids this and is pretty much the same except that instead of a comment a named constant is used. Similar code is used in Boost.Process: https://github.com/boostorg/process/blob/04ab646f128674fe66871b092e3263e08aa41ca5/include/boost/process/detail/windows/handles.hpp#L32

---

## Comment 1

> Username: Lastique  
> Created_at: 2020-06-09 11:32:18 UTC  
> Url: https://github.com/boostorg/filesystem/pull/149#issuecomment-641231361  

Thanks for noticing.  
  
Given that `long` is 32-bit, I believe, that's still not good enough as `0xC0000017` would overflow. I think, I need to cast `status` to `unsigned long` and also use `unsigned long`-typed constants.

---

## Comment 2

> Username: Lastique  
> Created_at: 2020-06-09 11:33:29 UTC  
> Url: https://github.com/boostorg/filesystem/pull/149#issuecomment-641231961  

Also, note the [test failure](https://ci.appveyor.com/project/Lastique/filesystem/builds/33408437/job/c2w6u3vmvje48d6a#L199).

---

## Comment 3

> Username: Flamefire  
> Created_at: 2020-06-09 12:18:00 UTC  
> Updated_at: 2020-06-09 12:18:24 UTC  
> Url: https://github.com/boostorg/filesystem/pull/149#issuecomment-641251445  

Well the size should be enough as Boost:process is doing `static_cast<::boost::winapi::NTSTATUS_>(0xC0000004l);`. I'm not sure why `0xC0000004l` did lead to problems at all as it clearly is supposed to be a `long` constant. I'm afraid your fix might be problematic: Casting a signed value to a larger unsigned value might be undefined or implementation defined behavior (at least in older standards). Remember that you are getting a value like `long(-3)`. So my advice would be to use the named constants as done here. The test failure was a minor mistake on my side and fixed in 4db7148  
  
Just tell me if I shall rebase and reopen a PR

---

## Comment 4

> Username: Lastique  
> Created_at: 2020-06-09 12:37:37 UTC  
> Url: https://github.com/boostorg/filesystem/pull/149#issuecomment-641262669  

1. Casting to unsigned is never UB because unsigned overflow is well defined. The result is always `static_cast< unsigned type >(0) + signed_value`, which means underflowing if `signed_value` is negative. It's casting from unsigned to signed that may be UB, if the unsigned value doesn't fit in the positive range of the signed type.  
2. I believe, the type of `0xC0000004l` is not `long` since 32-bit `long` cannot represent the value of 0xC0000004 = 3221225476. According to [lex.icon]/3, its type is `unsigned long`.  
3. Given the above, `static_cast<::boost::winapi::NTSTATUS_>(0xC0000004l)` is UB because it casts `unsigned long` value of 3221225476 to signed `long`, which causes an overflow. Most compilers won't mind, though, and give you a negative value of the same bit pattern.  
  
I could convert the literals to constants, but it's just not worth it here, with all the casts needed anyway. The current code is descriptive enough.  
  
A better solution for the future is to add the list of `NTSTATUS` codes to Boost.WinAPI and use those.

---

## Comment 5

> Username: Flamefire  
> Created_at: 2020-06-09 12:56:44 UTC  
> Url: https://github.com/boostorg/filesystem/pull/149#issuecomment-641273073  

Wow, it looks like MS f*cked up their `NTSTATUS` type by defining it as `long` as you are right about Point 2: `0xC0000004l` is of type `unsigned long` on windows but `long` on others (32 vs 64 bit size)  
  
To support Point 1 (for future reference) I found:   
Section 4.7 paragraph 2:  
  
> If the destination type is unsigned, the resulting value is the least unsigned integer congruent to the source integer (module 2n where n is the number of bits used to represent the unsigned type). [Note: In a two's complement representation, this conversion is conceptual and there is no change in the bit pattern (if there is no truncation). ]  
  
> A better solution for the future is to add the list of NTSTATUS codes to Boost.WinAPI and use those.  
  
Would be great to have a generic solution to the above but in practice simply casting the constant to `NTSTATUS` must work: The whole Windows SDK is doing it: `#define STATUS_TPM_NOSRK                 ((NTSTATUS)0xC0290012L)`: https://github.com/tpn/winsdk-10/blob/9b69fd26ac0c7d0b83d378dba01080e93349c2ed/Include/10.0.16299.0/shared/ntiologc.h So given that the approach by Boost.Process is fine

---
