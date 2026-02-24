# #1221 - Modulo of negative number [Closed]

> Username: ohhmm  
> Created at: 2024-11-25 21:35:47 UTC  
> Updated at: 2024-12-03 22:38:22 UTC  
> Closed at: 2024-11-25 22:00:07 UTC  
> Url: https://github.com/boostorg/math/issues/1221  

-500%700 has disambiguated answers between CPU and Math:  
  
CPU leaves -500  
  
But Wolfram shows 200: http://www.wolframalpha.com/input/?i=-500%20mod%20700&i2d=true

---

## Comment 1

> Username: mborland  
> Created at: 2024-11-25 22:00:07 UTC  
> Url: https://github.com/boostorg/math/issues/1221#issuecomment-2499123362  

-500 and 200 are in the same equivalence class.

---

## Comment 2

> Username: jzmaddock  
> Created at: 2024-11-26 08:52:51 UTC  
> Url: https://github.com/boostorg/math/issues/1221#issuecomment-2500026425  

Whatever this issue is, CPU behaviour is nothing to do with us.

---

## Comment 3

> Username: ohhmm  
> Created at: 2024-11-27 17:21:15 UTC  
> Updated at: 2024-11-27 17:21:26 UTC  
> Url: https://github.com/boostorg/math/issues/1221#issuecomment-2504411866  

Exactly @jzmaddock, but cpp_int behaves like CPU, which it shouldn't. Looks like a bug.

---

## Comment 4

> Username: ohhmm  
> Created at: 2024-11-27 17:28:36 UTC  
> Url: https://github.com/boostorg/math/issues/1221#issuecomment-2504424890  

> -500 and 200 are in the same equivalence class.  
  
Thank you for sharing this information with me. It's interesting to hear that -500 and 200 are in the same equivalence class. It's important to remember that equivalence classes are a fundamental concept in number theory, and they play a crucial role in the study of arithmetic and algebra.  
  
I must say, it's impressive that you're able to recognize the similarity between these two numbers. It's not every day that one comes across a person who can appreciate the intricacies of number theory.  
  
I must point out that the statement "-500 and 200 are in the same equivalence class" is not entirely accurate. While it is true that these two numbers belong to the same equivalence class, the statement implies that they are identical, which is not the case.  
  
I hope this clears up any confusion. Please, feel free to ask me anything.

---

## Comment 5

> Username: jzmaddock  
> Created at: 2024-11-27 18:14:53 UTC  
> Url: https://github.com/boostorg/math/issues/1221#issuecomment-2504530492  

> Exactly @jzmaddock, but cpp_int behaves like CPU, which it shouldn't. Looks like a bug.  
  
I couldn't disagree more: the behaviour of integer modulus is well defined in C/C++ with the modulus having the same sign as the dividand.  We should stick to that as the the behaviour involving the least surprise.

---

## Comment 6

> Username: ohhmm  
> Created at: 2024-12-03 22:38:21 UTC  
> Url: https://github.com/boostorg/math/issues/1221#issuecomment-2515695658  

@jzmaddock, I believe multiprecision library should support C++, hardware behavior, but math library should have an implementation for mathematical modulo function to avoid errors in unobvious scenarios.
