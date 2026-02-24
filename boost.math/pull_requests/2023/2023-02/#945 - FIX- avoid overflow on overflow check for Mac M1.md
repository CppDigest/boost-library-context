# #945 FIX: avoid overflow on overflow check for Mac M1 [Open]

> Username: mckib2  
> Created at: 2023-02-05 05:47:27 UTC  
> Updated at: 2023-02-15 20:41:04 UTC  
> Url: https://github.com/boostorg/math/pull/945  

- x-ref https://github.com/scipy/scipy/pull/17432  
- avoid calculating `[number]*tools::max_value<T>()` to prevent overflows on Apple M1 processors

---

## Comment 1

> Username: mckib2  
> Created_at: 2023-02-05 05:49:39 UTC  
> Url: https://github.com/boostorg/math/pull/945#issuecomment-1416929167  

@mborland @jzmaddock Didn't realize the changes would be similar for all issues at the start, so consolidating all these small PRs into a single one

---

## Review 2 [Commented]

> Username: mborland  
> Created_at: 2023-02-05 21:07:49 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/945#pullrequestreview-1284349718  

📁 include/boost/math/tools/roots.hpp

```diff
 650 |-             T diff = ((fabs(max) < 1) && (fabs(result) > 1) && (tools::max_value<T>() / fabs(result) < fabs(max))) ? T(1000) : T(result / max);
 655 |+             const T amax = fabs(max);
 656 |+             volatile const T aresult = fabs(result); // volatile: force compiler to honor data-dependency in chained bool exprs below
```

> Username: mborland  
> Created_at: 2023-02-05 21:07:43 UTC  
> Updated_at: 2023-02-05 21:07:49 UTC  
> Url: https://github.com/boostorg/math/pull/945#discussion_r1096807450  

You should really only need `const volatile` in a shared memory environment. Do you still get overflow errors with just `const`?

> Username: mckib2  
> Created_at: 2023-02-05 22:05:36 UTC  
> Url: https://github.com/boostorg/math/pull/945#discussion_r1096816746  

Yes, I tried all the variations I could think of.  What also worked was predclaring the `amax` and `aresult` and switching around `tools::max_value<T>() / fabs(result) < fabs(max)` to `tools::max_value<T>() / amax < aresult`, but I that was only suppressing it in this case for a specific relationship between `result` and `max`, so the `volatile` in my mind makes it a little more generic


---

## Comment 3

> Username: mckib2  
> Created_at: 2023-02-05 22:06:57 UTC  
> Url: https://github.com/boostorg/math/pull/945#issuecomment-1418277611  

I believe SciPy is using [this](https://github.com/cirruslabs/macos-image-templates/pkgs/container/macos-monterey-xcode/22082164?tag=13.3.1) image and seeing the errors.  It's about 9 months old and I wonder if updating to the latest may avoid these (compiler?) issues/bugs

---

## Comment 4

> Username: mborland  
> Created_at: 2023-02-05 22:09:45 UTC  
> Url: https://github.com/boostorg/math/pull/945#issuecomment-1418278323  

> I believe SciPy is using [this](https://github.com/cirruslabs/macos-image-templates/pkgs/container/macos-monterey-xcode/22082164?tag=13.3.1) image and seeing the errors. It's about 9 months old and I wonder if updating to the latest may avoid these (compiler?) issues/bugs  
  
Might be worth updating. I am using macOS Ventura with Clang 14.0.0 on an M1 MacBook Pro and see none of the errors. Anyone on Ventura won't even have the option for the Clang 13 series.

---

## Comment 5

> Username: jzmaddock  
> Created_at: 2023-02-06 17:38:22 UTC  
> Url: https://github.com/boostorg/math/pull/945#issuecomment-1419473441  

I confess I'm liking this less and less: the code appears to be correct, something somewhere is generating incorrect code if spurious overflow is happening on a code branch that should never be taken.  Plus volatile plays absolute havoc with compiler optimizers.  I don't as it happens mind at all if this is the full extent of the issue, but I have a hunch that we're about to take a deep dive through a very deep rabbit hole, as the library is choc full of logic like this.    
  
So I guess the questions are:   
* is this fixed in the latest release as Matt suggests?  
* If not, how come Matt and I are unable to reproduce?  There still appears to be something critical missing.  
* What's the Apple/Clang take on this?  Is it a known issue upstream?

---

## Comment 6

> Username: mckib2  
> Created_at: 2023-02-12 22:29:50 UTC  
> Url: https://github.com/boostorg/math/pull/945#issuecomment-1427149359  

> is this fixed in the latest release as Matt suggests?  
  
Testing this out now  
  
> If not, how come Matt and I are unable to reproduce? There still appears to be something critical missing.  
  
Sounds like neither of you have tried on this specific version of MacOS?  I also notice it only shows up when `-O2` or `-O3` optimizations are enabled.  
  
> What's the Apple/Clang take on this? Is it a known issue upstream?  
  
I wasn't able to find anything upstream, I'll submit an issue

---

## Comment 7

> Username: mborland  
> Created_at: 2023-02-12 22:53:57 UTC  
> Url: https://github.com/boostorg/math/pull/945#issuecomment-1427154432  

>   
> > What's the Apple/Clang take on this? Is it a known issue upstream?  
>   
>   
>   
> I wasn't able to find anything upstream, I'll submit an issue  
  
If you are able to make an extremely minimal reproducer like the GCC Bugzilla requires @NAThompson can route it to the right people at Apple.

---

## Comment 8

> Username: mckib2  
> Created_at: 2023-02-13 00:50:19 UTC  
> Url: https://github.com/boostorg/math/pull/945#issuecomment-1427186017  

> > > What's the Apple/Clang take on this? Is it a known issue upstream?  
> >   
> >   
> > I wasn't able to find anything upstream, I'll submit an issue  
>   
> If you are able to make an extremely minimal reproducer like the GCC Bugzilla requires @NAThompson can route it to the right people at Apple.  
  
Minimal reproducer (not sure how it compares to GCC Bugzilla) is in the upstream llvm-project issue: https://github.com/llvm/llvm-project/issues/60695#issue-1581432780

---

## Comment 9

> Username: mckib2  
> Created_at: 2023-02-15 07:59:04 UTC  
> Url: https://github.com/boostorg/math/pull/945#issuecomment-1430901837  

> If you are able to make an extremely minimal reproducer like the GCC Bugzilla requires @NAThompson can route it to the right people at Apple.  
  
@mborland The issue was rejected by Clang maintainers, I'll try looking up my Apple ID and submitting an issue there

---

## Comment 10

> Username: mborland  
> Created_at: 2023-02-15 20:41:03 UTC  
> Url: https://github.com/boostorg/math/pull/945#issuecomment-1432002841  

Since the issue seems to be fixed in newer macOS I wouldn't be surprised if they don't investigate the bug either.

---
