# #251 - cpp_bin_float rounding after addition bug [Closed]

> Username: already5chosen  
> Created at: 2020-06-23 19:32:23 UTC  
> Updated at: 2020-07-20 18:47:17 UTC  
> Closed at: 2020-07-20 18:47:17 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/251  

I got back to cpp_bin_float after 3 years pause and immediately encountered a bug that is hanging here since about the time I left.  
In fact, bug was already present in development branch of 1.63 that I had on my PC back in 2017, but somehow I overlooked it.  
But officially it appears first in 1.64.0  
Looks to me like rather typical "off by one" case in comparison of difference of exponents against mantissa range.  
  
Reproduction:  
[boost_add_bug.zip](https://github.com/boostorg/multiprecision/files/4821492/boost_add_bug.zip)

---

## Comment 1

> Username: jzmaddock  
> Created at: 2020-06-24 17:16:51 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/251#issuecomment-648952215  

Confirmed, just don't see the cause right now...

---

## Comment 2

> Username: already5chosen  
> Created at: 2020-06-24 18:11:53 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/251#issuecomment-648982163  

Did you pay attention that only values of a with odd signifacand are affected?

---

## Comment 3

> Username: already5chosen  
> Created at: 2020-06-24 20:18:34 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/251#issuecomment-649048391  

I looked at the code (cpp_bin_float.hpp).  
It seems to me the whole 'else if'  block at line 780 and its mirror copy at line 804 is a mistake.  
There should be just if and else. The case of abs(exp_a-exp_b)==bit_count+1 is *not* special.

---

## Comment 4

> Username: jzmaddock  
> Created at: 2020-06-24 21:00:42 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/251#issuecomment-649068936  

> I looked at the code (cpp_bin_float.hpp).  
>It seems to me the whole 'else if' block at line 780 and its mirror copy at line 804 is a mistake.  
>There should be just if and else. The case of abs(exp_a-exp_b)==bit_count+1 is not special.  
  
That was my first thought, but it causes quite a few test failures.  I need to examine the bit patterns and see what the difference is...

---

## Comment 5

> Username: already5chosen  
> Created at: 2020-06-25 17:28:24 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/251#issuecomment-649717596  

Is do_eval_subtract() used only for implementation of operators + and - or there are additional uses?  
I am asking because for + and - I see no problems with removal of 'else if'.

---

## Comment 6

> Username: jzmaddock  
> Created at: 2020-06-25 17:32:10 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/251#issuecomment-649719646  

> Is do_eval_subtract() used only for implementation of operators + and - or there are additional uses?  
I am asking because for + and - I see no problems with removal of 'else if'.  
  
Like I said, if you do that and run test_cpp_bin_float.cpp you will see several add/subract failures.  But see PR referenced above.

---

## Comment 7

> Username: already5chosen  
> Created at: 2020-06-25 18:36:59 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/251#issuecomment-649749913  

May be, I will your test_cpp_bin_float, but before that I want to understand.  
You additional condition guarantees that "eval_left_shift(1) etc..." part will be executed only when lsb of the bigger mantissa == 0.  
Which, in turn, guarantee, that at least for "round to nearest with tie broken to even" the effect of additional processing will be "back to the same mantissa".  
Since "round to nearest with tie broken to even" is the only rounding mode that I personally care about that could be the reason why I don't see a problem with just deleting "else if".  
Do you happen to care about other rounding modes?

---

## Comment 8

> Username: already5chosen  
> Created at: 2020-06-25 19:06:51 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/251#issuecomment-649763834  

o.k. Got it.  
"else if" has an effect when the bigger operand is power of 2. I wonder if this very corner case can be specified with simpler/faster conditions.

---

## Comment 9

> Username: already5chosen  
> Created at: 2020-06-25 19:27:42 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/251#issuecomment-649773346  

To correct my previous post, an exact condition for effective decrement is not quite as simple as I suggested above.  
The full condition is "the bigger operand is a power of 2 && the smaller operand is not a power of 2".

---

## Comment 10

> Username: already5chosen  
> Created at: 2020-06-25 20:04:21 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/251#issuecomment-649789448  

So, more elegant condition would be:  
         auto bit_count = cpp_bin_float<Digits, DigitBase, Allocator, Exponent, MinE, MaxE>::bit_count;  
         if ((eval_lsb(b.bits()) != bit_count - 1) && (eval_lsb(a.bits()) == bit_count - 1)) {

---

## Comment 11

> Username: already5chosen  
> Created at: 2020-06-25 20:07:13 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/251#issuecomment-649790756  

Even better if evaluated in opposite order:  
  
auto bit_count = cpp_bin_float<Digits, DigitBase, Allocator, Exponent, MinE, MaxE>::bit_count;  
if ((eval_lsb(a.bits()) == bit_count - 1) && (eval_lsb(b.bits()) != bit_count - 1)) {

---

## Comment 12

> Username: jzmaddock  
> Created at: 2020-06-26 10:50:58 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/251#issuecomment-650116453  

Yes that's much better, thanks for persevering with this!  
Proposed PR updated.
