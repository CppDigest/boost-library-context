# #33 Feature/x3 confix [Merged]

> Username: vtnerd  
> Created at: 2014-05-31 13:45:50 UTC  
> Updated at: 2014-06-26 22:19:15 UTC  
> Merged at: 2014-06-02 01:01:07 UTC  
> Closed at: 2014-06-02 01:01:07 UTC  
> Url: https://github.com/boostorg/spirit/pull/33  

Ported the confix parser. Seems like it could be pretty useful now that auto can be used.  
  
I also used `#include "test.hpp"` like I did with the seek test (seek.cpp). Tongari thought it should be `#include <test.hpp>` since the file is one up one directory and is being included via the `-I` switch. Let me know what the preferred style is for that.

---

## Comment 1

> Username: djowel  
> Created_at: 2014-05-31 21:35:20 UTC  
> Url: https://github.com/boostorg/spirit/pull/33#issuecomment-44760340  

I adjusted seek.cpp to use  #include "../test.hpp". That's the way it works for me. Is there a problem with that?

---

## Comment 2

> Username: djowel  
> Created_at: 2014-05-31 21:39:09 UTC  
> Url: https://github.com/boostorg/spirit/pull/33#issuecomment-44760425  

I was about to merge this, but: "We can’t automatically merge this pull request". Seems there are some conflicts.

---

## Comment 3

> Username: vtnerd  
> Created_at: 2014-06-01 17:45:46 UTC  
> Url: https://github.com/boostorg/spirit/pull/33#issuecomment-44783804  

I needed to pull in the repeat parser changes, that has been done now. I mucked up the history a little (a total of 6 commits now!). If you want a more clean history reject this merge and I can roll everything into a new branch to collapse the history.  
  
I also changed confix to use a relative include. I think its generally considered bad practice because moving files around can be problematic and confusing with relative directories. This is isolated to a test case, so it doesn't seem like a huge issue.

---

## Comment 4

> Username: K-ballo  
> Created_at: 2014-06-01 18:08:14 UTC  
> Url: https://github.com/boostorg/spirit/pull/33#issuecomment-44784421  

> I needed to pull in the repeat parser changes, that has been done now. I mucked up the history a little (a total of 6 commits now!). If you want a more clean history reject this merge and I can roll everything into a new branch to collapse the history.  
  
I for one would like to see a clean history. There's no need to abandon the pull request, you should be able to combine all the commits and then issue a forceful push.

---

## Comment 5

> Username: vtnerd  
> Created_at: 2014-06-01 19:50:36 UTC  
> Url: https://github.com/boostorg/spirit/pull/33#issuecomment-44787264  

Finally got the history squashed correctly.

---

## Comment 6

> Username: djowel  
> Created_at: 2014-06-02 00:03:00 UTC  
> Url: https://github.com/boostorg/spirit/pull/33#issuecomment-44793437  

that test looks good! indeed it does look very useful with auto! i love it!

---
