# #31 gcd: revamp [Merged]

> Username: jeremy-murphy  
> Created at: 2016-04-04 23:55:22 UTC  
> Updated at: 2016-05-09 17:18:15 UTC  
> Merged at: 2016-05-09 17:18:15 UTC  
> Closed at: 2016-05-09 17:18:15 UTC  
> Url: https://github.com/boostorg/math/pull/31  

This is a relatively quick rewrite of the gcd algorithms using code from From Mathematics to Generic Programming (more because it was conveniently on hand).

---

## Comment 1

> Username: jzmaddock  
> Created_at: 2016-04-06 18:17:48 UTC  
> Url: https://github.com/boostorg/math/pull/31#issuecomment-206500858  

Pushed to branch gcd, along with many other changes required to get the tests passing.

---

## Comment 2

> Username: jzmaddock  
> Created_at: 2016-04-07 18:40:41 UTC  
> Url: https://github.com/boostorg/math/pull/31#issuecomment-207043688  

Performance test results for various compilers start here: https://htmlpreview.github.io/?https://github.com/boostorg/math/blob/gcd/reporting/performance/html/index.html#special_function_and_distributio.section_gcd_method_comparison_with_Clang_version_3_8_0_trunk_256686_on_linux

---

## Comment 3

> Username: jeremy-murphy  
> Created_at: 2016-04-08 04:49:26 UTC  
> Url: https://github.com/boostorg/math/pull/31#issuecomment-207214471  

Hi John,  
  
thanks for making such an extensive comparison! I regret that I have not  
got into the habit of generating such results myself; is it fairly  
straightforward? I imagine the simple part is running the performance  
executable, but generating the HTML requires the usual docbook magic?  
  
It's curious that the Stein algorithm from boost is better than the  
textbook variant for multiprecision numbers, but for built-in numbers it's  
the other way around. But it reminds me to ask, is it actually worth  
measuring the generic gcd implementations on multiprecision types? I know I  
expressed curiosity about the result, but I seem to recall from your  
earlier comparison that the generic implementations were terrible by  
comparison to the specific ones?  
  
I'm not sure exactly which implementation is in use in each case: does  
"boost" mean the extant implementation by Daryle Walker and Paul Moore? And  
which textbook: TAOCP? What about the Stepanov implementation?  
  
Looks like the mixed Euclidean/Stein algorithm is a clear winner except in  
extreme cases. We could use that by default safely, still allowing for the  
rare possibility of numeric types that are only in one but not the other  
domain. Also, I'm thinking we should probably make a public interface for  
the named algorithms so that users can call them explicitly. That is, "gcd"  
would do the best thing for a numeric type, but a user could call  
"Euclid_gcd" for example if that's what they explicitly want. What do you  
think?  
  
Cheers.  
  
Jeremy  
  
On 8 April 2016 at 04:40, jzmaddock notifications@github.com wrote:  
  
> Performance test results for various compilers start here:  
> https://htmlpreview.github.io/?https://github.com/boostorg/math/blob/gcd/reporting/performance/html/index.html#special_function_and_distributio.section_gcd_method_comparison_with_Clang_version_3_8_0_trunk_256686_on_linux  
>   
> —  
> You are receiving this because you authored the thread.  
> Reply to this email directly or view it on GitHub  
> https://github.com/boostorg/math/pull/31#issuecomment-207043688

---

## Comment 4

> Username: jzmaddock  
> Created_at: 2016-04-08 18:24:52 UTC  
> Url: https://github.com/boostorg/math/pull/31#issuecomment-207546789  

> thanks for making such an extensive comparison! I regret that I have not  
> got into the habit of generating such results myself; is it fairly  
> straightforward? I imagine the simple part is running the performance  
> executable, but generating the HTML requires the usual docbook magic?  
>   
> Once you have you're user-config.jam set up to build documentation (see   
> https://svn.boost.org/trac/boost/wiki/BoostDocs/GettingStarted) then you   
> just cd into libs/math/reporting/performance and invoke bjam.  That will   
> run all the tests and build the single page summary that I linked to.    
> Likewise in the reporting/accuracy directory.  
>   
> It's curious that the Stein algorithm from boost is better than the  
> textbook variant for multiprecision numbers, but for built-in numbers it's  
> the other way around.   
> Other way around I think - but that's because the "textbook" algorithm   
> has been messed around with (see below).  
>   
> But it reminds me to ask, is it actually worth  
> measuring the generic gcd implementations on multiprecision types? I   
> know I  
> expressed curiosity about the result, but I seem to recall from your  
> earlier comparison that the generic implementations were terrible by  
> comparison to the specific ones?  
  
Not much point other than curiosity, multiprecision types need special   
handling in general.  
  
> I'm not sure exactly which implementation is in use in each case: does  
> "boost" mean the extant implementation by Daryle Walker and Paul   
> Moore? And  
> which textbook: TAOCP? What about the Stepanov implementation?  
>   
> Boost is your re-written code.  
  
Textbook is basically some code I threw together in order to have   
something to experiment with, it's pretty much the "wikipedia" version   
since I couldn't be bothered to walk upstairs to fetch Knuth ;)  
  
It's been messed about with though, for example whenever you see   
something like:  
  
while(x&1) x >>=1;  
  
which is common to all the binary algorithm variants, it's more   
efficient on most platforms to write:  
  
x >>= lsb(x);  
  
where lsb(x) finds the least-significant bit of x.  On many platforms   
this is a compiler intrinsic so this reduces to 2 opcodes if we ignore   
possible load and stores.  It's also hugely more efficient for   
multiprecision types.  
  
> Looks like the mixed Euclidean/Stein algorithm is a clear winner except in  
> extreme cases. We could use that by default safely, still allowing for the  
> rare possibility of numeric types that are only in one but not the other  
> domain. Also, I'm thinking we should probably make a public interface for  
> the named algorithms so that users can call them explicitly. That is,   
> "gcd"  
> would do the best thing for a numeric type, but a user could call  
> "Euclid_gcd" for example if that's what they explicitly want. What do you  
> think?  
  
I suspect there's less between them than we think, but yes, the mixed   
algorithm looks best in general.  
  
A public interface sounds OK to me, I think we also need a traits class   
for things like  
- which algorithm to use.  
- functions odd/even.  
- function lsb and whether it's available.  
  
etc.  
  
I'll look into that shortly.  
  
John.

---

## Comment 5

> Username: jeremy-murphy  
> Created_at: 2016-04-15 07:30:33 UTC  
> Url: https://github.com/boostorg/math/pull/31#issuecomment-210328489  

Just a note that I clear forgot to implement lcm.

---

## Comment 6

> Username: jzmaddock  
> Created_at: 2016-04-17 10:55:48 UTC  
> Url: https://github.com/boostorg/math/pull/31#issuecomment-211000668  

> Just a note that I clear forgot to implement lcm.  
>   
> Yes, I saw that and put it back in for the gcd branch.

---

## Comment 7

> Username: jzmaddock  
> Created_at: 2016-04-18 12:53:13 UTC  
> Url: https://github.com/boostorg/math/pull/31#issuecomment-211368473  

Just a note to say I've pushed some changes to the "gcd" branch to include:  
- Mixed binary algorithm.  
- Traits class support.  
- Bitscanning support.  
  
Can you see if this traits class provides what polynomial needs to be able to use gcd?  
  
See https://github.com/boostorg/math/commit/1ef4bb64901f76084689b97f4546b3e294a84147

---

## Comment 8

> Username: jeremy-murphy  
> Created_at: 2016-04-20 21:41:40 UTC  
> Url: https://github.com/boostorg/math/pull/31#issuecomment-212619084  

I'll try to get around to it on the weekend.  
On 18 Apr 2016 10:53 PM, "jzmaddock" notifications@github.com wrote:  
  
> Just a note to say I've pushed some changes to the "gcd" branch to include:  
> - Mixed binary algorithm.  
> - Traits class support.  
> - Bitscanning support.  
>   
> Can you see if this traits class provides what polynomial needs to be able  
> to use gcd?  
>   
> See 1ef4bb6  
> https://github.com/boostorg/math/commit/1ef4bb64901f76084689b97f4546b3e294a84147  
>   
> —  
> You are receiving this because you authored the thread.  
> Reply to this email directly or view it on GitHub  
> https://github.com/boostorg/math/pull/31#issuecomment-211368473

---
