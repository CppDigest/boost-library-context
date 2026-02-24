# #4 Add explicit dependency on Boost.Test [Merged]

> Username: jhunold  
> Created at: 2015-01-14 15:16:49 UTC  
> Updated at: 2015-01-14 16:36:58 UTC  
> Merged at: 2015-01-14 15:17:50 UTC  
> Closed at: 2015-01-14 15:17:50 UTC  
> Url: https://github.com/boostorg/atomic/pull/4  

Atomic uses the "included" header-only version of Boost.Test. So explicitly add Boost.Test as a dependency via the `<use>` mechanism. This solves the test failures after latest Boost.Test refactorings.

---

## Comment 1

> Username: Lastique  
> Created_at: 2015-01-14 15:24:16 UTC  
> Url: https://github.com/boostorg/atomic/pull/4#issuecomment-69931996  

Could you elaborate why is this needed?

---

## Comment 2

> Username: jhunold  
> Created_at: 2015-01-14 15:31:42 UTC  
> Url: https://github.com/boostorg/atomic/pull/4#issuecomment-69933321  

Boost.Test depends on Boost.Timer which is not header-only at the moment.

---

## Comment 3

> Username: Lastique  
> Created_at: 2015-01-14 16:26:44 UTC  
> Url: https://github.com/boostorg/atomic/pull/4#issuecomment-69943642  

Boost.Test had a header-only mode previously - I assume, the dependency on Boost.Timer was a recent breaking change in Boost.Test? IMHO, that should be a bug in Boost.Test or at least there should be a prominent announcement of the change. Could you raise this issue on the mailing list?  
  
For now I'm inclined to keep the patch to Boost.Atomic applied just to keep the tests running. In the long term I'd rather drop Boost.Test altogether.

---

## Comment 4

> Username: timblechmann  
> Created_at: 2015-01-14 16:30:10 UTC  
> Url: https://github.com/boostorg/atomic/pull/4#issuecomment-69944279  

dropping the dependency for boost.test is definitely not a bad thing, given the bad shape of boost.test in general.  
@Lastique discussing about boost.test on the mainling list seems to be pointless, as bug fixes won't be applied to boost.test/master. in the worst situation, develop will build, but master won't (i had this before).

---

## Comment 5

> Username: jhunold  
> Created_at: 2015-01-14 16:36:58 UTC  
> Url: https://github.com/boostorg/atomic/pull/4#issuecomment-69945584  

There already is a ML list thread. Just helping out with b2 issues. It seems that Boost.Test starts getting the needed maintenance, dropping it right now might be a bit hasty.  
@timblechmann this patch works with master.   
And maybe porting to the latest Boost.Test (linked) using auto test cases would be easier.

---
