# #21 - Cppcheck Travis job does nothing in a verbose way [Closed]

> Username: pdimov  
> Created at: 2018-09-23 12:23:13 UTC  
> Updated at: 2018-10-16 00:27:07 UTC  
> Closed at: 2018-10-16 00:27:07 UTC  
> Url: https://github.com/boostorg/function/issues/21  

The Cppcheck job (https://travis-ci.org/boostorg/function/jobs/432021217) fills the Travis log with warnings about a function not being inlined, then concludes with the final blow of:  
```  
cppcheck: error: could not find or open any of the paths given.  
```  
and succeeds.  
  
I can't help but notice that Function had a perfectly serviceable .travis.yml configuration which I had put in place, that admittedly needed to be brought up to date but nevertheless provided much more platform coverage (important for a widely used library such as Function.) This has been deleted. Consider reinstating it.

---

## Comment 1

> Username: jeking3  
> Created at: 2018-09-24 12:44:21 UTC  
> Url: https://github.com/boostorg/function/issues/21#issuecomment-423961778  

This was done to get coverage and static analysis builds.  We can expand the matrix (however, extended platform testing is done on the test matrix systems).  If the builds don't take too long we can add more platforms.

---

## Comment 2

> Username: pdimov  
> Created at: 2018-09-24 12:52:58 UTC  
> Url: https://github.com/boostorg/function/issues/21#issuecomment-423964050  

For an old and critical piece of infrastructure such as `function`, widely used in legacy projects, it would be good to have more platform coverage, including f.ex. g++ 4.4 and 4.6 in addition to the newer ones. Like in https://github.com/boostorg/system/blob/develop/.travis.yml#L31 say. But in general I of course prefer the full matrix here as well. New pull requests often break old platforms because people only test them on newer compilers.

---

## Comment 3

> Username: jeking3  
> Created at: 2018-10-15 00:52:30 UTC  
> Url: https://github.com/boostorg/function/issues/21#issuecomment-429678577  

I will pull the CI from assign here to improve CI.

---

## Comment 4

> Username: pdimov  
> Created at: 2018-10-15 01:11:20 UTC  
> Url: https://github.com/boostorg/function/issues/21#issuecomment-429680308  

Thanks!  
  
Have you looked at the Cppcheck job by the way, per the issue title? It seemed to just emit an error and not actually check anything. If you follow the link in the first comment, then open the raw log (https://api.travis-ci.org/v3/job/432021217/log.txt) because the many warnings exceed the displayed log size, you'll see that the invocation prints  
```  
cppcheck: error: could not find or open any of the paths given.  
```  
and there's no indication that it analyzed anything.

---

## Comment 5

> Username: jeking3  
> Created at: 2018-10-15 03:31:22 UTC  
> Url: https://github.com/boostorg/function/issues/21#issuecomment-429698043  

I will take a look.  Thanks.  
  
On Sun, Oct 14, 2018, 9:11 PM Peter Dimov <notifications@github.com> wrote:  
  
> Thanks!  
>  
> Have you looked at the Cppcheck job by the way, per the issue title? It  
> seemed to just emit an error and not actually check anything. If you follow  
> the link in the first comment, then open the raw log (  
> https://api.travis-ci.org/v3/job/432021217/log.txt) because the many  
> warnings exceed the displayed log size, you'll see that the invocation  
> prints  
>  
> cppcheck: error: could not find or open any of the paths given.  
>  
> and there's no indication that it analyzed anything.  
>  
> —  
> You are receiving this because you commented.  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/function/issues/21#issuecomment-429680308>,  
> or mute the thread  
> <https://github.com/notifications/unsubscribe-auth/ALOdbYFKfR0gQ1nEIKbNJqYGUMfVILPIks5uk-C6gaJpZM4W1rid>  
> .  
>

---

## Comment 6

> Username: jeking3  
> Created at: 2018-10-15 13:07:34 UTC  
> Url: https://github.com/boostorg/function/issues/21#issuecomment-429845931  

@pdimov found it, fixed it, you'll see it in the next PR.
