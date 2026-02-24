# #28 develop merged into fusion_adapters branch [Merged]

> Username: daminetreg  
> Created at: 2014-10-28 21:42:28 UTC  
> Updated at: 2014-11-09 12:34:45 UTC  
> Merged at: 2014-10-29 01:42:55 UTC  
> Closed at: 2014-10-29 01:42:55 UTC  
> Url: https://github.com/boostorg/fusion/pull/28  

Hi @djowel and @K-ballo,  
  
As told in #25 I merged the develop branch into fusion_adapters to ease you the review. The merge was done without conflict, and all tests passed for g++-4.6,g++-4.8.1 and g++-4.8.1-c++11 with :   
1. bjam -a cxxflags="-DBOOST_PP_VARIADICS"  
2. bjam -a  
  
Cheers,

---

## Comment 1

> Username: djowel  
> Created_at: 2014-10-29 00:34:58 UTC  
> Url: https://github.com/boostorg/fusion/pull/28#issuecomment-60856227  

I'll inspect/study the code this weekend. Thank you very much for working on this, Damien!

---

## Comment 2

> Username: daminetreg  
> Created_at: 2014-10-29 00:39:40 UTC  
> Url: https://github.com/boostorg/fusion/pull/28#issuecomment-60856642  

Cool :) I need to plan time to handle your comments then :laughing:  
  
On 10/29/2014 01:34 AM, Joel de Guzman wrote:  
  
> I'll inspect/study the code this weekend. Thank you very much for  
> working on this, Damien!  
>   
> —  
> Reply to this email directly or view it on GitHub  
> https://github.com/boostorg/fusion/pull/28#issuecomment-60856227.

---

## Comment 3

> Username: djowel  
> Created_at: 2014-10-30 00:34:30 UTC  
> Url: https://github.com/boostorg/fusion/pull/28#issuecomment-61028915  

One thing that you could do is check out the Fusion tickets: https://svn.boost.org/trac/boost/report/14?asc=1&USER=anonymous&page=9 for anything related to the fusion adaptors and see if our cool new facility can help.

---

## Comment 4

> Username: daminetreg  
> Created_at: 2014-10-30 21:24:16 UTC  
> Updated_at: 2014-10-30 21:24:58 UTC  
> Url: https://github.com/boostorg/fusion/pull/28#issuecomment-61172093  

Sadly the only ticket that it solves is the one opened for the task :laughing: : [#9516](https://svn.boost.org/trac/boost/ticket/9516)

---

## Comment 5

> Username: djowel  
> Created_at: 2014-11-08 23:50:02 UTC  
> Updated_at: 2014-11-09 00:03:06 UTC  
> Url: https://github.com/boostorg/fusion/pull/28#issuecomment-62283845  

I inspected the code and it is very clean. I like it. Unfortunately, I am getting errors when running the tests on clang. We'll need to 1) Make sure all tests pass on all major compilers, 2) Proper documentation. I'll open a new ticket for the fusion adapted integration.   
  
(EDIT: Hmmm... seems like we can't have "issues" tracking here in github (for boost). I can't seem to reopen this PR either. Not sure what to do. In the meantime, we can probably discuss here.)

---

## Comment 6

> Username: daminetreg  
> Created_at: 2014-11-09 12:34:39 UTC  
> Url: https://github.com/boostorg/fusion/pull/28#issuecomment-62301894  

Thank you for your inspection. :smile:  
  
> 1) Make sure all tests pass on all major compilers,  
  
I'll check it out with clang, I tested with it often but not since my last changes, so I expect it to be something small. I also need to check with MSVC that I didn't for long.  
  
> 2) Proper documentation. I'll open a new ticket for the fusion adapted integration.  
  
I adapted the documentation while finishing each of the macro changes, normally all should be covered, in my opinion it should be enough, but perhaps you want me to add a specific chapter on the change ?  
  
In case I just uploaded the current version here, possibly you need to clear your cache, as it's the same URL as for 1 year : http://www.lecbna.org/~sys_lecbna/boost_fusion_type_deduction_for_adapt_struct_doc/, it's sadly missing the CSS and images link. I'll try to fix this for a better viewing :)  
  
I'll check with clang and MSVC the 18th November, I won't be able before, but I already should have.

---
