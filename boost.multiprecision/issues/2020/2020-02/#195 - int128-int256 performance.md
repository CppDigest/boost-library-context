# #195 - int128/int256 performance [Closed]

> Username: pdimov  
> Created at: 2020-02-15 20:06:27 UTC  
> Updated at: 2020-02-16 19:03:02 UTC  
> Closed at: 2020-02-16 15:05:19 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/195  

Arthur O'Dwyer has written a blog post on the performance of various int128_t implementations, including his own, here: https://quuxplusone.github.io/blog/2020/02/13/wide-integer-proof-of-concept/ (Reddit discussion at https://www.reddit.com/r/cpp/comments/f3mgun/the_abstraction_penalty_for_wide_integer_math_on/)  
  
In a somewhat inflammatory and not entirely justified tone he claims that Boost.Multiprecision performs badly. :-)  
  
The example he cites is https://gcc.godbolt.org/z/mrFXgG, which in fact does not demonstrate much of a difference. The only reason Boost.MP is penalized in this example (multiplication of two uint128_t numbers) is that Boost.MP's uint128_t has a nontrivial destructor, and is therefore not eligible for being passed or returned in registers.  
  
However, when I modified the example slightly to look at uint256_t addition, the difference became more pronounced: https://gcc.godbolt.org/z/cYqSTu  
  
Here Arthur's class yields  
  
```  
add(Wider<Wider<unsigned long> >, Wider<Wider<unsigned long> >):                 # @add(Wider<Wider<unsigned long> >, Wider<Wider<unsigned long> >)  
        mov     rax, rdi  
        mov     rcx, qword ptr [rsp + 40]  
        add     rcx, qword ptr [rsp + 8]  
        mov     rdx, qword ptr [rsp + 16]  
        adc     rdx, qword ptr [rsp + 48]  
        mov     rsi, qword ptr [rsp + 24]  
        adc     rsi, qword ptr [rsp + 56]  
        mov     rdi, qword ptr [rsp + 32]  
        adc     rdi, qword ptr [rsp + 64]  
        mov     qword ptr [rax], rcx  
        mov     qword ptr [rax + 8], rdx  
        mov     qword ptr [rax + 16], rsi  
        mov     qword ptr [rax + 24], rdi  
        ret  
```  
  
which is optimal code for adding two 256 bit numbers. Boost.MP is way behind.  
  
This is just a heads-up; I don't know how actionable all this information is, because Boost.MP is obviously much more generic and full-featured. However, by a quick look, it seems to me that special-cased backends for 128/256/512/1024 bit numbers, along the lines of Arthur's divide-by-2 implementation, might be possible and result in similar performance.

---

## Comment 1

> Username: jzmaddock  
> Created at: 2020-02-16 11:41:43 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/195#issuecomment-586696478  

>The only reason Boost.MP is penalized in this example (multiplication of two uint128_t numbers) is that Boost.MP's uint128_t has a nontrivial destructor, and is therefore not eligible for being passed or returned in registers.  
  
Actually the destructor *is* trivial, there must be something else tripping us up there.  Copy/move constructors are non trivial could that be it?  
  
> it seems to me that special-cased backends for 128/256/512/1024 bit numbers, along the lines of Arthur's divide-by-2 implementation, might be possible and result in similar performance.  
  
For sure: the 2 libraries use rather different approaches, and I can see a performance difference which appears to mostly be down to smaller code and better inlining.  There is a long standing TODO for the library which is to add 2's compliment support to fixed precision cpp_int's which would then look very similar to this approach.

---

## Comment 2

> Username: pdimov  
> Created at: 2020-02-16 13:01:41 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/195#issuecomment-586703753  

Yes, you're right, I was looking at the primary `cpp_int_base` template but the specialization has a trivial destructor. Yes, copy/move constructors also need to be defaulted for a type to be eligible to be passed in registers.

---

## Comment 3

> Username: ckormanyos  
> Created at: 2020-02-16 13:19:06 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/195#issuecomment-586705361  

>> it seems to me that special-cased backends for 128/256/512/1024 bit numbers, along the lines of Arthur's divide-by-2 implementation, might be possible and result in similar performance.  
  
> For sure: the 2 libraries use rather different approaches, and I can see a performance difference which appears to mostly be down to smaller code and better inlining.  
  
Yes. In fact, there are several regions of performance being addressed by different issues even at the moment as we write this. Some of these have been noted in raising actual issues or PRs. We are, for instance, looking into extending and improving high-precision performance and also providing backend support for fast low-digit counts of floating-point types. The issue John mentioned addresses clasic small integer ranges.  
  
I think the library's authors actually do respond well to user interest and change requests, if that is relevant in the long-term for this issue.  
  
Kind regards, Chris

---

## Comment 4

> Username: pdimov  
> Created at: 2020-02-16 15:05:19 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/195#issuecomment-586717053  

> I think the library's authors actually do respond well to user interest and change requests  
  
Oh of course, no implication to the contrary has been made. In fact it is precisely because I know MP is being actively developed and maintained I decided to not take Arthur's claim that it's "absymally awful" (sic) on face value and check for myself.

---

## Comment 5

> Username: ckormanyos  
> Created at: 2020-02-16 19:02:13 UTC  
> Updated at: 2020-02-16 19:03:02 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/195#issuecomment-586740747  

> Oh of course, no implication to the contrary has been made.  
  
Yes, I know. Thank you. I was, in fact, addressing the strong wording present in the review that you have pointed out with this issue. If the performance can be significantly improved in some areas, I suspect we will be definitely interested in addressing these issues as time allows.  
  
Thanks for pointing out this issue.  
  
Kind regards, Chris
