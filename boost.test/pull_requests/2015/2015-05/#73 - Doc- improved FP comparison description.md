# #73 Doc: improved FP comparison description [Merged]

> Username: akrzemi1  
> Created at: 2015-05-29 16:50:59 UTC  
> Updated at: 2015-06-02 21:50:51 UTC  
> Merged at: 2015-06-02 21:48:30 UTC  
> Closed at: 2015-06-02 21:48:30 UTC  
> Url: https://github.com/boostorg/test/pull/73  



---

## Comment 1

> Username: raffienficiaud  
> Created_at: 2015-05-29 18:58:07 UTC  
> Url: https://github.com/boostorg/test/pull/73#issuecomment-106903648  

Please update the jamfile as well

---

## Comment 2

> Username: akrzemi1  
> Created_at: 2015-05-29 21:19:32 UTC  
> Url: https://github.com/boostorg/test/pull/73#issuecomment-106938585  

I have.

---

## Comment 3

> Username: raffienficiaud  
> Created_at: 2015-05-29 22:32:56 UTC  
> Updated_at: 2015-06-02 20:52:45 UTC  
> Url: https://github.com/boostorg/test/pull/73#discussion_r31370579  

I added this part there when I fixed the support for multiprecision, but now I think it should go to another section since we are under "theory".

---

## Comment 4

> Username: raffienficiaud  
> Created_at: 2015-05-29 22:40:06 UTC  
> Updated_at: 2015-06-02 20:52:45 UTC  
> Url: https://github.com/boostorg/test/pull/73#discussion_r31371051  

I think it is misleading to say "1%" here, because a percentage does not have any unit. The epsilon in the equation does have the same unit as u or v

---

## Comment 5

> Username: raffienficiaud  
> Created_at: 2015-05-29 22:41:15 UTC  
> Updated_at: 2015-06-02 20:52:45 UTC  
> Url: https://github.com/boostorg/test/pull/73#discussion_r31371103  

Equ4&5 are doing exactly the opposite: they are removing the scale from u and v rather than scaling epsilon

---

## Comment 6

> Username: raffienficiaud  
> Created_at: 2015-05-29 22:41:46 UTC  
> Updated_at: 2015-06-02 20:52:45 UTC  
> Url: https://github.com/boostorg/test/pull/73#discussion_r31371128  

I do not know if the Knuth is the first one presenting this

---

## Comment 7

> Username: raffienficiaud  
> Created_at: 2015-05-29 22:46:01 UTC  
> Updated_at: 2015-06-02 20:52:45 UTC  
> Url: https://github.com/boostorg/test/pull/73#discussion_r31371335  

"too close to 0" works, the problem is when one of the operand is really 0 (the Equations are not defined).

---

## Comment 8

> Username: raffienficiaud  
> Created_at: 2015-05-29 22:48:38 UTC  
> Updated_at: 2015-06-02 20:52:45 UTC  
> Url: https://github.com/boostorg/test/pull/73#discussion_r31371467  

"the minimum tolerance required to check positively ": this is incorrect and this is not what "the tolerance to set to make the test successful" says:  
- the tolerance to set depends on the equation currently under check  
- it also handles the inequality, while you are saying "check positively the closeness of `u` and `v`"

---

## Comment 9

> Username: raffienficiaud  
> Created_at: 2015-05-29 22:50:33 UTC  
> Updated_at: 2015-06-02 20:52:45 UTC  
> Url: https://github.com/boostorg/test/pull/73#discussion_r31371551  

Are we required to type `T(0)` ? This is the question I asked you for this test `0 < fpt` in another PR

---

## Comment 10

> Username: raffienficiaud  
> Created_at: 2015-05-29 22:51:56 UTC  
> Updated_at: 2015-06-02 20:52:45 UTC  
> Url: https://github.com/boostorg/test/pull/73#discussion_r31371623  

you mean with `BOOST_TEST(rel, tolerance)` right?

---

## Comment 11

> Username: raffienficiaud  
> Created_at: 2015-05-29 22:52:36 UTC  
> Updated_at: 2015-06-02 20:52:45 UTC  
> Url: https://github.com/boostorg/test/pull/73#discussion_r31371662  

and cannot be used in eg. `std::map`

---

## Comment 12

> Username: raffienficiaud  
> Created_at: 2015-05-29 22:53:42 UTC  
> Updated_at: 2015-06-02 20:52:45 UTC  
> Url: https://github.com/boostorg/test/pull/73#discussion_r31371719  

I would have said "but still too close"

---

## Comment 13

> Username: raffienficiaud  
> Created_at: 2015-05-29 22:56:55 UTC  
> Updated_at: 2015-06-02 20:52:45 UTC  
> Url: https://github.com/boostorg/test/pull/73#discussion_r31371898  

I believe that MSVC >= 1900 uses two digits format natively now (other platforms use that format)

---

## Comment 14

> Username: akrzemi1  
> Created_at: 2015-05-30 19:58:30 UTC  
> Url: https://github.com/boostorg/test/pull/73#issuecomment-107079745  

Ok, I applied most of your suggestions.  
  
I ignored:  
  
> I believe that MSVC >= 1900 uses two digits format natively now (other platforms use that format)  
  
(I do not understand what I am expected to do here)  
  
> Are we required to type `T(0)` ? ...  
  
(This will be addressed separately, but the docs right now say the truth about the implementation)

---

## Comment 15

> Username: raffienficiaud  
> Created_at: 2015-05-30 20:06:35 UTC  
> Updated_at: 2015-05-30 20:07:41 UTC  
> Url: https://github.com/boostorg/test/pull/73#issuecomment-107080031  

By the comment "I believe that MSVC >= 1900 uses two digits format natively now", I was saying that MSVC is displaying something like "1.0000000000000001e-05" and not "1.0000000000000001e-005" (05 vs 005) starting MSVC>=1900.  
  
See  
http://stackoverflow.com/questions/9226400/portable-printing-of-exponent-of-a-double-to-c-iostreams

---

## Comment 16

> Username: akrzemi1  
> Created_at: 2015-05-30 21:49:27 UTC  
> Url: https://github.com/boostorg/test/pull/73#issuecomment-107092669  

Is the PR acceptable in the current form?

---

## Comment 17

> Username: raffienficiaud  
> Created_at: 2015-05-31 08:43:59 UTC  
> Updated_at: 2015-06-02 20:52:45 UTC  
> Url: https://github.com/boostorg/test/pull/73#discussion_r31388248  

I would say "instead we would like to compare u, v and epsilon on the same scale". Or "for the comparison to be meaningful, u, v and epsilon should be expressed on the same scale".

---

## Comment 18

> Username: raffienficiaud  
> Created_at: 2015-05-31 08:46:38 UTC  
> Updated_at: 2015-06-02 20:52:45 UTC  
> Url: https://github.com/boostorg/test/pull/73#discussion_r31388267  

This is not the "minimum", this is the tolerance. Minimum depends on the comparison that is in use, for close_at_tolerance it would be the minimum, for !close_at_tolerance it would be the maximum. I would just drop the minimum word here.

---

## Comment 19

> Username: raffienficiaud  
> Created_at: 2015-05-31 08:47:49 UTC  
> Updated_at: 2015-06-02 20:52:45 UTC  
> Url: https://github.com/boostorg/test/pull/73#discussion_r31388274  

This is only for ==, for !=, it uses the negated version of close_at_tolerance (involving with Equ5). The operation in use is not precised in this paragraph

---

## Comment 20

> Username: raffienficiaud  
> Created_at: 2015-05-31 08:53:22 UTC  
> Updated_at: 2015-06-02 20:52:45 UTC  
> Url: https://github.com/boostorg/test/pull/73#discussion_r31388314  

I am mitigated here. I liked the "Theory" name better here, I would make another section "Practice" or  "Design in the **UTF**" or "Implementation design", containing the "Implementation part", but not contained in this section and visible in the TOC

---

## Comment 21

> Username: raffienficiaud  
> Created_at: 2015-05-31 08:55:58 UTC  
> Url: https://github.com/boostorg/test/pull/73#issuecomment-107146855  

Thanks, I made some additional remarks. Also please use 2 digits exponents.  
I do not know if you are doing that already, but please use new commits (I can only see one commit). It is hard to follow otherwise.

---

## Comment 22

> Username: akrzemi1  
> Created_at: 2015-06-01 07:04:50 UTC  
> Url: https://github.com/boostorg/test/pull/73#issuecomment-107331892  

About the three-digit vs. two-digit exponent display: the output that is enclosed in the examples, it the output I get when I am running the example programs (I am using MinGW on Windows with GCC 4.8.2). I believe it is the right approach, to give the real life output.  
  
The way I understand your suggestion is that I should manually tweak the results, so that they show something else than what I am getting. I may have misunderstood your suggestion, though?

---

## Comment 23

> Username: raffienficiaud  
> Created_at: 2015-06-01 08:10:46 UTC  
> Url: https://github.com/boostorg/test/pull/73#issuecomment-107354425  

Yes, as the output that you have is different from mine. When I run it, I have the following (clang/darwin):  
  
```  
../doc/examples/tolerance_03.cpp:26: error: in "failing": check x - d < x has failed [10 - 9.9999999999999995e-07 >= 10]. Relative difference is within tolerance [1e-07 < 0.0001]  
../doc/examples/tolerance_03.cpp:27: error: in "failing": check x - d != x has failed [10 - 9.9999999999999995e-07 == 10]. Relative difference is within tolerance [1e-07 < 0.0001]  
../doc/examples/tolerance_03.cpp:29: error: in "failing": check d > .0 has failed [9.9999999999999995e-07 <= 0]. Absolute value is within tolerance [|1e-06| < 0.0001]  
../doc/examples/tolerance_03.cpp:30: error: in "failing": check d < .0 has failed [9.9999999999999995e-07 >= 0]  
```  
  
If the compilers do not agree, I would go for the unified solution. Either 2 or 3 digits, but all floating point representation should be the same. I believe we are using 2 digits now, also in the unit tests.

---

## Comment 24

> Username: akrzemi1  
> Created_at: 2015-06-01 08:17:24 UTC  
> Url: https://github.com/boostorg/test/pull/73#issuecomment-107355846  

> I am mitigated here. I liked the "Theory" name better here, I would make another section "Practice" or "Design in the UTF" or "Implementation design", containing the "Implementation part", but not contained in this section and visible in the TOC  
  
Perhaps we can reach some compromise. Can you tell me what you need section "Theory" for and what you want to communicate with it? I found it confusing that it described how components like  `close_at_tolerance` are implemented (e.g., what they return and how they are initialized).  
  
The current state proposed with this PR is not clean either. My goal, however, is to clearly and in short, describe what is actually checked when I type `BOOST_TEST(v == u, tt:tolerance(eps))`. There is some overlap, but perhaps this needs two pages: one saying what is computed, the other saying why? (This why is what i would call the "theory", but it could also be the "rationale", explaining why you prefer to implement it this way or the other, and I do not necessarily see it in the section "Writing unit tests".  
  
Please tell me what you think about this.

---

## Comment 25

> Username: raffienficiaud  
> Created_at: 2015-06-01 08:52:09 UTC  
> Url: https://github.com/boostorg/test/pull/73#issuecomment-107371124  

'design rational' is ok as well (but I prefer theory better), and you are right in saying that it is confusing to describe `close_at_tolerance` there. Also, `close_at_tolerance` is part of a public but not readily accessible API.  
  
In this "Theory", I see everything related to the equations and the references. I think it is nice to have it directly in the documentation. Putting it too far from the topic would make it less accessible as well: the idea is to help ppl writing better tests with UTF and floating points. What about referencing theory in the "floating point comparisons" part, putting the section "theory" as a final subsection of it, and removing everything related to the code from this part?  
  
A possible TOC would be:  
  
```  
x- floating point comparison  
  x.1 - Enabling tolerance for user-defined types  
  x.2 - Design rational (where close_at_tolerance is described)  
  x.3 - Theory behind floating point comparisons  
```  
  
Would that be better?

---

## Comment 26

> Username: akrzemi1  
> Created_at: 2015-06-01 09:07:35 UTC  
> Url: https://github.com/boostorg/test/pull/73#issuecomment-107376259  

> x- floating point comparison  
>   x.1 - Enabling tolerance for user-defined types  
>   x.2 - Design rational (where close_at_tolerance is described)  
>   x.3 - Theory behind floating point comparisons  
  
Where do I put the equations: in x.2 or x.3? Also, I need a place to say "`BOOST_TEST(v == u, tt:tolerance(eps))` uses 'this comparison'" -- where should it go?  
  
Can I change it to:  
  
x- floating point comparison  
  x.1 - Enabling tolerance for user-defined types  
  x.2 - Tolerance-based comparison (no mention of close_at_tolerance)  
  x.3 - Theory behind floating point comparisons

---

## Comment 27

> Username: akrzemi1  
> Created_at: 2015-06-02 21:25:36 UTC  
> Url: https://github.com/boostorg/test/pull/73#issuecomment-108103075  

Ok, I added a new commit. It should address all the remarks.

---

## Comment 28

> Username: raffienficiaud  
> Created_at: 2015-06-02 21:48:27 UTC  
> Url: https://github.com/boostorg/test/pull/73#issuecomment-108108736  

Thanks

---

## Comment 29

> Username: raffienficiaud  
> Created_at: 2015-06-02 21:50:51 UTC  
> Url: https://github.com/boostorg/test/pull/73#issuecomment-108109165  

I just checked and the reported tolerance by close_at_tolerance is described in the reference documentation. Should be enough.

---
