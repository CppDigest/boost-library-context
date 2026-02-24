# #236 Stack overflow fix [Closed]

> Username: cmazakas  
> Created at: 2024-12-12 19:09:32 UTC  
> Updated at: 2024-12-18 18:07:24 UTC  
> Closed at: 2024-12-18 17:09:14 UTC  
> Url: https://github.com/boostorg/regex/pull/236  

Blocked on: https://github.com/boostorg/regex/pull/235  
  
Fuzzing found an interesting pathological case in the regex creator. In the backstep calculation, we can introduce unbounded recursion which causes a stack overflow (in this case, only on clang).  
  
I tried coming up with better iterative solutions but they weren't particularly satisfactory and I'm not overwhelmingly satisfied with my approach here either so I'm looking for a lot of feedback.  
  
The shape of the regex here is something like `||||||...` repeated ad nauseum. It was hard to reliably reproduce without just including the actual fuzzing data so I added it to the corpus.  
  
I also took the liberty of modernizing the fuzzing jamfiles because they seemed outdated to me. libFuzzer was superceded by Centipede which became part of the official Google repos but it has a different API, and nowadays you can just use: `clang -fsanitize=fuzzer` anyway which does exactly what we want.

---

## Comment 1

> Username: jzmaddock  
> Created_at: 2024-12-15 11:54:40 UTC  
> Url: https://github.com/boostorg/regex/pull/236#issuecomment-2543843366  

Self contained test case:  
  
```  
#include <boost/regex.hpp>  
  
int main()  
{  
   char const s[] = "(?<=(||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||u||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||))";  
   boost::regex rx(s);  
}  
  
```

---

## Comment 2

> Username: jzmaddock  
> Created_at: 2024-12-15 12:19:54 UTC  
> Url: https://github.com/boostorg/regex/pull/236#issuecomment-2543851762  

All of the alternatives are unpleasant - on the one hand your fix is perfectly reasonable - I can think of no really good reason for a very large number of alternatives in a look-behind.  My only worry, is that - like you I'm sure - I dislike hard coded limits, what happens for example if we're on embedded hardware with a very small stack?  Removing the second recursive call is not too hard (it's almost tail recursion after all), but still leaves us open to infinite recursion on the first call via nested alternatives such as:  
  
```  
(?<=((|(|(|(|(|(|))))))  
```  
  
Let me have a think.... there will be much much harder cases than this BTW!!

---

## Comment 3

> Username: cmazakas  
> Created_at: 2024-12-16 16:09:17 UTC  
> Url: https://github.com/boostorg/regex/pull/236#issuecomment-2546039080  

One of my first-pass thoughts was to simply turn this from recursion into iteration, but mapping the control flow proved difficult.  
  
I think we need a sane limit regardless, because even if we did avoid stack overflows, we still want to prevent an every-growing usage of memory.  
  
Maybe I should just give it another shot to make the solution iterative here.

---

## Comment 4

> Username: jzmaddock  
> Created_at: 2024-12-16 17:16:12 UTC  
> Url: https://github.com/boostorg/regex/pull/236#issuecomment-2546196587  

Like this: https://github.com/boostorg/regex/pull/237 ?  It removes the recursion, and interestingly the stack never actually grows large unless you start having nested alternatives.

---

## Comment 5

> Username: cmazakas  
> Created_at: 2024-12-17 15:28:52 UTC  
> Url: https://github.com/boostorg/regex/pull/236#issuecomment-2548760955  

> Like this: #237 ? It removes the recursion, and interestingly the stack never actually grows large unless you start having nested alternatives.  
  
Ha, yeah, exactly like that.  
  
I think we need both a reasonable implementation limit and we also need a way of avoiding an ever-growing stack. Let's see if we can't spruce up the existing PR.

---

## Comment 6

> Username: cmazakas  
> Created_at: 2024-12-17 23:24:15 UTC  
> Url: https://github.com/boostorg/regex/pull/236#issuecomment-2549878377  

@jzmaddock I took a closer look at the PR you had created, and it looked perfect to me. Seems to solve the issue too, the code seems to pass with the corpus entry plucked from oss-fuzz.  
  
If you wanna merge, you should feel free to do so. I tried to do something similar to yours, but my approach was way less clean so I'm very impressed by your technique.  
  
Btw, when do you think we'll start merging these fixes to the master branch?

---

## Comment 7

> Username: jzmaddock  
> Created_at: 2024-12-18 17:08:37 UTC  
> Url: https://github.com/boostorg/regex/pull/236#issuecomment-2551857883  

OK merged.  Thanks.  
  
I'm quite bad about merging to master in good time... might be an idea to merge sooner rather than later if only to check that the Fuzzer issues get auto-closed.

---

## Comment 8

> Username: jzmaddock  
> Created_at: 2024-12-18 17:09:14 UTC  
> Url: https://github.com/boostorg/regex/pull/236#issuecomment-2551859011  

Closing in favour of #237

---
