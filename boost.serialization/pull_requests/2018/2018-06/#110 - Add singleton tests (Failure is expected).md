# #110 Add singleton tests (Failure is expected) [Closed]

> Username: Flamefire  
> Created at: 2018-06-26 11:02:55 UTC  
> Updated at: 2024-12-09 10:29:16 UTC  
> Closed at: 2018-11-08 06:54:28 UTC  
> Url: https://github.com/boostorg/serialization/pull/110  

Spinoff of #105   
  
This adds additional test variants and also builds tests of feature branches on appveyor.  
  
The main contribution is the test of the `singleton::is_destroyed` behaviour:  
  
- Checks that `is_destroyed` returns `true` if, and only if` the singleton is destroyed  
- Checks that the singleton is actually destroyed (no memory leaks)  
  
This should prevent regressions when changing the implementation details of `singleton`

---

## Comment 1

> Username: Flamefire  
> Created_at: 2018-06-26 14:52:00 UTC  
> Url: https://github.com/boostorg/serialization/pull/110#issuecomment-400338415  

@robertramey This is ready for review.  
The failures should all be from the newly added test showing that `is_destroyed` is not working as specified: Object gets destroyed but flag is not set.  
  
Reason: `singleton_wrapper` https://github.com/boostorg/serialization/blob/738695b70733f9d592a570fb17a505d6a029b48a/include/boost/serialization/singleton.hpp#L121 is instantiated and destroyed but `singleton<>` is responsible for setting the flag: https://github.com/boostorg/serialization/blob/738695b70733f9d592a570fb17a505d6a029b48a/include/boost/serialization/singleton.hpp#L157. As `singleton_wrapper` is not related to `singleton<>` the latter is never instantiated and hence never destroyed.  
  
To be completely correct: `singleton<>` is instantiated if the user class inherits from it. See `inheritedSingleton` as opposed to `plainSingleton`. Note also that both types are used in Boost.Serialization.

---

## Comment 2

> Username: res2k  
> Created_at: 2018-08-30 23:42:25 UTC  
> Url: https://github.com/boostorg/serialization/pull/110#issuecomment-417502722  

I made a branch containing a fix: https://travis-ci.org/res2k/serialization/builds/422500391

---

## Comment 3

> Username: Flamefire  
> Created_at: 2018-08-31 07:14:58 UTC  
> Url: https://github.com/boostorg/serialization/pull/110#issuecomment-417574811  

Why? What was wrong with mine: https://github.com/boostorg/serialization/pull/105/commits/e1b68942b50c133432b29068fcd90e96c5f11963 (already in #105 consisting of the 2 tests (this and #111) and the fix in 2 additional commits) It also fixes all those wrong comments and typos and explains what is going on so the next person wanting to change this is aware of it. Only difference I (may) like is that you fully put the wrapper class inside of the function. Might be worth it, but having it in an extra namespace makes it look cleaner. Oh and you did not protect the singleton ctor too leading to potential for misuse.  
I suggest you cherry pick my commit instead. Same for the test cases: Why did you change them/use old ones? You forgot stuff: E.g. use of Boost.PP instead of own macros and the `-Wterminate` fix  
  
Don't get me wrong, if those visibility fixes solve #111 then great! But why redo my work that is already here?

---

## Comment 4

> Username: res2k  
> Created_at: 2018-08-31 09:03:46 UTC  
> Url: https://github.com/boostorg/serialization/pull/110#issuecomment-417601919  

For one, my fix was semi-accidental - I noticed the wrong placement of the `destroyed` flag handling and moved it before realizing there was this PR. And, but this is a matter of taste, I didn't see much point in moving `singleton_wrapper` around; it's not very cluttering as it is now, and changing it in it's current place keeps the diff smaller (and thus more readable).  
  
Though, generally I collected the changes, your tests etc on a branch to have them run through CI, not to have them merge-ready. (For the same reason I didn't bother with pulling the additional changes to the test.)  
For the time being, I'm not finished yet anyway - the visibility stuff is not quite there yet, there are still issues related to the case in #111. IMO after that we can see how to put everything together.  
  
Finally, I'm quite grateful for your test cases - they help me to validate the visibility stuff for some cases the current unit tests don't quite cover. So maybe they didn't result in any net saving for me, but they help with making a better patch ;)

---

## Comment 5

> Username: Flamefire  
> Created_at: 2018-08-31 09:17:20 UTC  
> Url: https://github.com/boostorg/serialization/pull/110#issuecomment-417605442  

:+1: for noticing the wrong placement independently! (Seriously! People have been moving stuff around w/o really knowing what they are doing leading to this mess)    
I also moved the `is_destroyed` flag itself to the sub-class to further reduce the chance of abuse which makes the class (and diff) bigger. The comments explaining it also add some more size and it has been in `detail` before. That's why I felt it should go back there. But I don't mind either way, that's what the review should decide (which hopefully comes one day)  
  
Suggestion: Merge #110 and #111 into your branch just as I did in #105, then you don't have separate changes and the expanded test-matrix to validate your visibility changes.

---

## Comment 6

> Username: Flamefire  
> Created_at: 2018-10-13 17:47:31 UTC  
> Url: https://github.com/boostorg/serialization/pull/110#issuecomment-429561420  

From @robertramey   
  
> Honestly, I never understood your argument that is_destroyed is broken.  
  
There is a function `is_destroyed` for singletons (https://github.com/boostorg/serialization/blob/e1893dd28c408bb25352596e2f91892b3d570164/include/boost/serialization/singleton.hpp#L152) This should return true exactly when the singleton was destroyed and false otherwise. This behaviour is currently never tested and I found that the function returns false in some situations where the singleton is destroyed. In the result the assertions (and possible checks) don't catch this and a dead singleton is accessed.  
  
>  I put a in a test which now runs all the time which verifies that all instances of singleton are destroyed, and that they are destroyed in the inverse order of creation. It's a pretty simple test and quite convincing to me.  
  
This is what I also said on why I removed this test:   
  
- Your test ONLY tests, that those singletons are destroyed in reverse of construction in the context of a **single** executable. This is guaranteed by the C++ standard and hence any failure there would be for the compiler and we could simply shrug it off.  
- My test here goes deeper: It does test the order, but a) also checks `is_destroyed` to return `true` AND `false` correctly and b) that the singletons are eventually destroyed (no mem leaks)  
  
These tests fail as can be seen by CI: ` Assertion failed: boost::serialization::singleton<plainSingleton>::is_destroyed()`

---

## Comment 7

> Username: Flamefire  
> Created_at: 2018-11-08 06:54:27 UTC  
> Url: https://github.com/boostorg/serialization/pull/110#issuecomment-436891955  

Closed in favor of simpler #129

---
