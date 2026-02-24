# #756 Add class template argument deduction guides for distributions [Merged]

> Username: jamesfolberth  
> Created at: 2022-02-05 22:15:34 UTC  
> Updated at: 2022-02-12 19:09:40 UTC  
> Merged at: 2022-02-12 19:09:40 UTC  
> Closed at: 2022-02-12 19:09:40 UTC  
> Url: https://github.com/boostorg/math/pull/756  

Fixes #754 by adding deduction guides for the case where the class template arguments are not given explicitly.  
Follows the demonstration by @jzmaddock.  
  
I didn't put in `static_assert` for silly cases, e.g., where the user writes `chi_squared_distribution<int>`.  
If that's what they write, so be it.

---

## Comment 1

> Username: jamesfolberth  
> Created_at: 2022-02-06 06:18:23 UTC  
> Url: https://github.com/boostorg/math/pull/756#issuecomment-1030760444  

Putzing around in Godbolt, it looks like GCC-8 supports pre-final C++17 deduction guides (`__cpp_deduction_guides==201611`) and variadic templates, but they're not playing together well in `test_dist_deduction_guides.cpp`. Manually providing an overload of `test_deduction_guide` for the no arg case should hopefully do the trick.

---

## Comment 2

> Username: jamesfolberth  
> Created_at: 2022-02-06 07:15:54 UTC  
> Url: https://github.com/boostorg/math/pull/756#issuecomment-1030767612  

I'm not sure what's going on with `naive_monte_carlo_test` (case 5?) failing during the CI run ...

---

## Comment 3

> Username: jamesfolberth  
> Created_at: 2022-02-06 21:08:42 UTC  
> Url: https://github.com/boostorg/math/pull/756#issuecomment-1030914728  

Well, clang 6-8 are now unhappy with the "no arg" case. These use the default template arg, not the deduction guide, right? So I think we can just get rid of those tests.  
  
I believe I've got some of the CI running locally, and removing the "no arg" tests seems to be working.

---

## Comment 4

> Username: mborland  
> Created_at: 2022-02-08 09:56:02 UTC  
> Updated_at: 2022-02-08 10:33:07 UTC  
> Url: https://github.com/boostorg/math/pull/756#issuecomment-1032419473  

@jamesfolberth I approved your PR to run against our CI so you should start seeing results.  
  
Edit: You don't need to add the platform tags unless there is one specific one you need. The system can't process multiple tags so the default is run everything.

---

## Comment 5

> Username: jamesfolberth  
> Created_at: 2022-02-08 15:08:09 UTC  
> Url: https://github.com/boostorg/math/pull/756#issuecomment-1032711856  

Whoops! That broke it, sorry. I was trying to avoid MSVC, which appeared to me to be failing over the weekend for reasons unrelated to this PR. I'll remove those tags from the commit message. Thanks for your help!

---

## Comment 6

> Username: mborland  
> Created_at: 2022-02-10 08:17:32 UTC  
> Url: https://github.com/boostorg/math/pull/756#issuecomment-1034615377  

This PR looks good to me. Do you have anything else to add, and does it solve your problem?

---

## Comment 7

> Username: NAThompson  
> Created_at: 2022-02-10 16:53:18 UTC  
> Url: https://github.com/boostorg/math/pull/756#issuecomment-1035158914  

Looks good to me as well. Might want to let @jzmaddock weigh in though; I've pwned myself with argument deduction subtleties before . . .

---

## Comment 8

> Username: jamesfolberth  
> Created_at: 2022-02-10 18:11:52 UTC  
> Url: https://github.com/boostorg/math/pull/756#issuecomment-1035263793  

This was all I had planned for this PR. It does indeed solve the problem I  
originally encountered.  
  
Thanks for taking a look. Getting a few of the older compilers to be happy  
with the test was a bit delicate.  
  
On Thu, Feb 10, 2022 at 9:53 AM Nick ***@***.***> wrote:  
  
> Looks good to me as well. Might want to let @jzmaddock  
> <https://github.com/jzmaddock> weigh in though; I've pwned myself with  
> argument deduction subtleties before . . .  
>  
> —  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/math/pull/756#issuecomment-1035158914>, or  
> unsubscribe  
> <https://github.com/notifications/unsubscribe-auth/AAY4FGFNRVCXCMI4VEZE2ZDU2PUQTANCNFSM5NUUTMSQ>  
> .  
> Triage notifications on the go with GitHub Mobile for iOS  
> <https://apps.apple.com/app/apple-store/id1477376905?ct=notification-email&mt=8&pt=524675>  
> or Android  
> <https://play.google.com/store/apps/details?id=com.github.android&referrer=utm_campaign%3Dnotification-email%26utm_medium%3Demail%26utm_source%3Dgithub>.  
>  
> You are receiving this because you were mentioned.Message ID:  
> ***@***.***>  
>

---

## Review 9 [Commented]

> Username: jzmaddock  
> Created_at: 2022-02-10 18:37:26 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/756#pullrequestreview-879288804  

📁 include/boost/math/distributions/fisher_f.hpp

```diff
  57 |+ #ifdef __cpp_deduction_guides
  58 |+ template <class RealType>
  59 |+ fisher_f_distribution(RealType)->fisher_f_distribution<typename boost::math::tools::promote_args<RealType>::type>;
```

> Username: jzmaddock  
> Created_at: 2022-02-10 18:37:26 UTC  
> Url: https://github.com/boostorg/math/pull/756#discussion_r803997589  

fisher_f doesn't have a single-arg constructor?


---

## Comment 10

> Username: jzmaddock  
> Created_at: 2022-02-10 18:44:08 UTC  
> Url: https://github.com/boostorg/math/pull/756#issuecomment-1035322926  

Other than the single comment on the fisher_f this looks good to go to me - many thanks to @jamesfolberth for seeing this through!

---

## Comment 11

> Username: jamesfolberth  
> Created_at: 2022-02-11 05:29:10 UTC  
> Url: https://github.com/boostorg/math/pull/756#issuecomment-1035895245  

Oh dang nice catch! Thanks all for the help. Happy to contribute!

---

## Comment 12

> Username: jamesfolberth  
> Created_at: 2022-02-12 15:16:33 UTC  
> Url: https://github.com/boostorg/math/pull/756#issuecomment-1037254023  

FWIW looks like CI is failing waiting on a runner.

---

## Comment 13

> Username: jzmaddock  
> Created_at: 2022-02-12 16:37:07 UTC  
> Url: https://github.com/boostorg/math/pull/756#issuecomment-1037293092  

Ugh, that's just silly.  Anyone any objections to merging anyway?  All looks good to me.

---

## Comment 14

> Username: ckormanyos  
> Created_at: 2022-02-12 18:35:22 UTC  
> Url: https://github.com/boostorg/math/pull/756#issuecomment-1037378943  

I've had the runner farm _go_ _on_ _strike_ a few times. I'm not exactly sure what causes that. Short of stopping and restarting the _entire_ run on GHA that run is toasted. So unless we restart entirely, which seems uwaranted, content gets the nod.

---

## Comment 15

> Username: jamesfolberth  
> Created_at: 2022-02-12 18:56:10 UTC  
> Url: https://github.com/boostorg/math/pull/756#issuecomment-1037392754  

I think we're safe, so I've no problem with merging anyway.  
  
After my 3rd commit (6bd06cf77277f97ba53ca063a87e18d33aa77a7a) I ran locally `test_dist_deduction_guides` with all the compilers and language versions for `ubuntu-focal` and `ubuntu-bionic`. Given that that passed and that CI ran on my 2nd commit, I think we're in the clear.

---
