# #266 Changes for Embarcadero C++ clang-based compilers [Closed]

> Username: eldiener  
> Created at: 2020-04-19 15:33:06 UTC  
> Updated at: 2022-02-27 19:46:34 UTC  
> Closed at: 2020-05-17 10:04:14 UTC  
> Url: https://github.com/boostorg/test/pull/266  



---

## Comment 1

> Username: raffienficiaud  
> Created_at: 2020-04-19 16:12:38 UTC  
> Url: https://github.com/boostorg/test/pull/266#issuecomment-616172579  

Please do not get angry at the Travis/Appveyor/Codecov on the ML, nothing works right now and I have my own CI.

---

## Comment 2

> Username: eldiener  
> Created_at: 2020-04-20 01:08:41 UTC  
> Url: https://github.com/boostorg/test/pull/266#issuecomment-616258306  

I am sorry if i gave the impression that I am angry in any way, whether that involves code coverage or any other issue. There is no rush on seeing if this PR is valid since, as I say in the title, the change is targeted for Boost 1.74 and that is a long way away. I still have more work to do to try to get the Embarcadero C++ clang-based compilers to work properly with Boost.Test but this PR is at least a good first start, but there is no rush to get this merged right now.

---

## Comment 3

> Username: raffienficiaud  
> Created_at: 2020-05-07 22:51:35 UTC  
> Url: https://github.com/boostorg/test/pull/266#issuecomment-625536400  

Hi,  
  
Sorry for the late answer:   
  
> I am sorry if i gave the impression that I am angry in any way  
  
No worries on my side, I just wanted to point out that some of those CI thinggies are not working as expected. But all is fine on my personal CI, so the PR seems good to go.  
  
I would just like to squash some commits, aggregate some macros and cleanup `execution_monitor.ipp`. Should be quite fast.

---

## Comment 4

> Username: raffienficiaud  
> Created_at: 2020-05-09 14:31:52 UTC  
> Updated_at: 2020-05-09 14:32:06 UTC  
> Url: https://github.com/boostorg/test/pull/266#issuecomment-626184934  

Branch `topic/PR-266-Embarcadero-C++-clang-based-compilers` is ready to get merged to `next`. I made a few (minor/cosmetic) edits, it would be good that you have a look as I have no way to check the compiler.

---

## Comment 5

> Username: eldiener  
> Created_at: 2020-05-09 14:43:52 UTC  
> Url: https://github.com/boostorg/test/pull/266#issuecomment-626186602  

Looks fine. I am not sure what adding the 1.74 label or adding to the 1.74 milestone actually does, but as far as changing the title of the PR I am good with that. I am 100% sure that my PR changes will not affect any other compiler than the Embarcadero C++ clang-based compilers, and I still have work to do to test those compilers with this library, but the PR is a good start.

---

## Comment 6

> Username: raffienficiaud  
> Created_at: 2020-05-09 14:46:57 UTC  
> Url: https://github.com/boostorg/test/pull/266#issuecomment-626187011  

Those labels are for my personal (lack of) organization. Thanks for the quick feedback.

---

## Comment 7

> Username: raffienficiaud  
> Created_at: 2020-05-09 20:08:10 UTC  
> Url: https://github.com/boostorg/test/pull/266#issuecomment-626228947  

In `develop`: I suggest that we monitor the test matrix for some time before it lands to `master`.

---

## Comment 8

> Username: eldiener  
> Created_at: 2020-05-09 20:44:37 UTC  
> Url: https://github.com/boostorg/test/pull/266#issuecomment-626232983  

>   
>   
> In `develop`: I suggest that we monitor the test matrix for some time before it lands to `master`.  
  
There is no necessity for this to go to 'master' until you are ready. I will check the regression tests on 'develop' to make sure nothing is amiss. You should really try to get your CI tests working properly to give you peace of mind whenever there is a PR against the 'test' library.

---

## Comment 9

> Username: raffienficiaud  
> Created_at: 2020-05-10 08:49:16 UTC  
> Url: https://github.com/boostorg/test/pull/266#issuecomment-626294461  

> You should really try to get your CI tests working properly to give you peace of mind whenever there is a PR against the 'test' library.  
  
I have already an excellent coverage on a private CI with ~ 20 machines that covers most of the Appveyor+Travis builds, and which is must faster that those community tools. My CI is green, this is why it has been merged to `develop`. Despite that, whatever you do, this is still less than some exotic compiler from the test matrix.

---

## Comment 10

> Username: raffienficiaud  
> Created_at: 2020-05-17 10:04:14 UTC  
> Url: https://github.com/boostorg/test/pull/266#issuecomment-629772857  

in master, thanks!

---
