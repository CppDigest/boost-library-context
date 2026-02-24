# #310 sequential_or: Fixed random order execution of underlying parsers [Merged]

> Username: Kojoley  
> Created at: 2017-12-04 14:41:23 UTC  
> Updated at: 2017-12-08 16:48:28 UTC  
> Merged at: 2017-12-08 16:48:24 UTC  
> Closed at: 2017-12-08 16:48:24 UTC  
> Url: https://github.com/boostorg/spirit/pull/310  

After two hours of fighting with the optimizer, I happily drew attention to  
this little nifty bitwise operator in `any_if_ns` function.  
  
Explanation of the bug: bitwise inclusive OR operator is not a sequence point  
(per chapter §5.13 of C++14 standard), that's why at compiling the expression  
`a() | b() | ... | z()` optimizer is allowed to rearrange the execution order  
of the functions `a`, `b` ... `z`.  
  
There is high chance that a lot of people were misguided by the bug and chose  
not to use `sequential_or`.  
  
I vaguely remember how about three years ago I thought that I am dumb and/or  
the documentation is wrong when I tried to use the `sequential_or` but ended  
with some workaround.  
  
There are three possible fixes:  
  - This one  
  - Make the original `any_ns` and `any_if_ns` strict ordered  
    (could potentially make permutations operator slower)  
  - Break the `any_ns` and `any_if_ns` API and somehow generalize the code

---

## Comment 1

> Username: djowel  
> Created_at: 2017-12-04 14:56:56 UTC  
> Url: https://github.com/boostorg/spirit/pull/310#issuecomment-348986052  

Very interesting insights! I honestly haven't thought about this until now! Do we have tests for this?

---

## Comment 2

> Username: Kojoley  
> Created_at: 2017-12-04 15:34:43 UTC  
> Url: https://github.com/boostorg/spirit/pull/310#issuecomment-348998130  

There cannot be a test for this particular problem. For me it happened on `sequential_or` tests. The bug depends on compiler optimization strategy, inlining aggressiveness and phase of the moon .

---

## Comment 3

> Username: Kojoley  
> Created_at: 2017-12-04 16:15:53 UTC  
> Updated_at: 2017-12-04 16:19:16 UTC  
> Url: https://github.com/boostorg/spirit/pull/310#issuecomment-349012038  

Or if someone know a test for `i++ + ++i` problem - I can adopt it for our case.  
  
Example:  
```cpp  
#include <cstdio>  
  
int main()  
{  
    return printf("Hello") | printf("World");  
}  
```  
  
The example produces "WorldHello" for me on MSVC on any optimization strategy except `Od` and with any inlining strategy, but it prints "HelloWorld" on GCC and Clang with everything I have tried.  
  
From my point of view if such a test exists - it requires some preprocessor pragma or compiler argument. Actually this is a good idea for an in-compiler-fuzzer, but again, the bug will appear on already existing tests and does not require a dedicated test.

---

## Comment 4

> Username: djowel  
> Created_at: 2017-12-04 22:39:03 UTC  
> Url: https://github.com/boostorg/spirit/pull/310#issuecomment-349130633  

OK, well, I haven't studied your solution yet, but could you give me a quick explanation on how the solution works?

---

## Comment 5

> Username: Kojoley  
> Created_at: 2017-12-05 13:53:59 UTC  
> Url: https://github.com/boostorg/spirit/pull/310#issuecomment-349310304  

It is very simple, the crucial part is b1c74d04022ac532d7760175929448724456b92f.

---

## Comment 6

> Username: Kojoley  
> Created_at: 2017-12-07 16:56:31 UTC  
> Url: https://github.com/boostorg/spirit/pull/310#issuecomment-350028784  

I cannot setup Appveyor on the repository until the problem is fixed. Should I remove `sequence_or` tests from the test suit on CI?

---

## Comment 7

> Username: djowel  
> Created_at: 2017-12-08 02:47:19 UTC  
> Url: https://github.com/boostorg/spirit/pull/310#issuecomment-350157291  

I understand now. Sorry for the delay. Please go ahead.

---
