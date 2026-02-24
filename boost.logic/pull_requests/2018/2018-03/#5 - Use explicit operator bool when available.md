# #5 Use explicit operator bool when available [Merged]

> Username: pdimov  
> Created at: 2018-03-31 18:24:56 UTC  
> Updated at: 2018-11-03 04:02:28 UTC  
> Merged at: 2018-08-20 12:18:36 UTC  
> Closed at: 2018-08-20 12:18:36 UTC  
> Url: https://github.com/boostorg/logic/pull/5  



---

## Comment 1

> Username: pdimov  
> Created_at: 2018-04-01 11:48:02 UTC  
> Url: https://github.com/boostorg/logic/pull/5#issuecomment-377781272  

This is unfortunately a breaking change, due to the way explicit operator bool works. The three cases that break are initializing a bool from a tribool, returning a tribool from a function returning bool, and passing a tribool to a function taking bool. None of those were tested, but they are probably in use.

---

## Comment 2

> Username: eldiener  
> Created_at: 2018-04-21 00:38:46 UTC  
> Url: https://github.com/boostorg/logic/pull/5#issuecomment-383254333  

Are you saying that this PR is a breakling change to the way tribool now interacts with bool in the three cases you mentioned ? In that case why are we creating this PR ?

---

## Comment 3

> Username: pdimov  
> Created_at: 2018-04-21 02:02:13 UTC  
> Url: https://github.com/boostorg/logic/pull/5#issuecomment-383259282  

I created the PR before realizing the implications. It may still be worth applying, or not, I'm not entirely sure. This breakage is actually known as it applies everywhere we switched to explicit operator bool; but here of course the type is kind-of-bool itself so things are not so clear.  
  
The upside of this change is that the conversion to bool is now properly constexpr on g++, whereas it wasn't before.

---

## Comment 4

> Username: eldiener  
> Created_at: 2018-04-21 15:42:31 UTC  
> Url: https://github.com/boostorg/logic/pull/5#issuecomment-383306609  

I assume that 'explicit operator bool' means that the compiler will not look for an implicit conversion to operator bool through the safe_bool construct. If that is correct then I can not see that this PR should go through even if it does allow the conversion to bool to be a constexpr, since as you point out it disallows the implicit conversion from a tribool to a bool used by end-users to pass tribool when a bool is expected. Of course we could notify users in the next release of this potential change, in order to prepare them to use an explicit static_cast from a tribool to a bool when needed, and then make the change in the release after that. But i am still not sure whether usage in constexpr is worth the removal of the implicit conversion.

---

## Comment 5

> Username: robertramey  
> Created_at: 2018-05-15 17:58:27 UTC  
> Url: https://github.com/boostorg/logic/pull/5#issuecomment-389257874  

I'm still conflicted with this.  
  
Upsides of making this change:  
  
Without this change, my safe numeric library breaks in a bunch of places with gcc.  Some are due to conversion to bool not being constexpr.  But there are other places where things break that seem unrelated to the constexpr issue.  At least the error messages don't mention constexpr.  Worse yet, some of the failures occur only in release mode, which makes it hard to track them down with the debugger. All this is resolved by this PR.  Things work as I expect.  
  
Downside of making this change.  
  
If this PR is merged, programs which use implicit conversion to bool will break with a compile error. These errors are easy to fix with a static cast.  So I don't see it as breaking any applications. Indeed,  I have to explicitly convert to bool in a few places.  
  
Another downside is that the API for pre-C++11 compilers would be different from that of post C++11 compilers.  This seems to me to be a very bad idea and indeed a recipe for disaster.  
  
The library is passing all  its tests (except the constpr test with gcc) .  But looking at the test suite, it's hard to verify that the tests are exhaustive.  I'm really confused by all this.

---

## Comment 6

> Username: robertramey  
> Created_at: 2018-05-17 00:47:07 UTC  
> Url: https://github.com/boostorg/logic/pull/5#issuecomment-389709570  

This video explains what problems arise when replacing safe_bool with explicit conversion. https://www.youtube.com/watch?v=7uIyl_tDMkM

---

## Comment 7

> Username: robertramey  
> Created_at: 2018-05-17 18:34:32 UTC  
> Updated_at: 2018-05-17 19:12:26 UTC  
> Url: https://github.com/boostorg/logic/pull/5#issuecomment-389965834  

Recapping: certain tribool functions fail under gcc when used in a constexpr environment. There is no problem with the same code under clang. Consider the following test case:  
```  
    #include <boost/logic/tribool.hpp>  
    #include <cassert>  
    int main(){  
        constexpr boost::tribool tb_t{true};  
        static_assert(tb_t, "tb_t");  
        assert(static_cast<bool>(tb_t));  
        constexpr boost::tribool tb_f{false};  
        static_assert(! tb_f, "tb_f");  
        assert(! static_cast<bool>(tb_f));  
        return 0;  
    }  
```  
With any version of gcc, compilation fails with the following error message:  
```  
    test_z.cpp:634:32: error: ‘(boost::logic::tribool::dummy::nonnull != 0u)’ is not a constant expression  
```  
So I modify the offending line 77 of tribool.hpp to read:  
```  
BOOST_CONSTEXPR void nonnull() BOOST_NOEXCEPT {};  
```  
and recompile.  Now the static asserts fail:  
```  
    test_z.cpp:634:5: error: static assertion failed: tb_t  
     static_assert(tb_t, "tb_t");  
     ^~~~~~~~~~~~~  
    test_z.cpp:637:5: error: static assertion failed: tb_f  
     static_assert(! tb_f, "tb_f");  
     ^~~~~~~~~~~~~  
```  
So clearly we have a problem.  This was a major bitch to isolate.  I'm continuing to search for a way to work around this but I'm really stuck.  Anyone knowing anything about this is welcome to chime in. I don't know who is responsible for maintaining this very important (for me) module.  
  
FYI - everything works as expected when it's not a constexpr expression.

---

## Comment 8

> Username: mclow  
> Created_at: 2018-05-17 19:07:39 UTC  
> Url: https://github.com/boostorg/logic/pull/5#issuecomment-389975884  

I'll pass this on to the gcc devs. (maybe as a reduced test case) and see what they say.

---

## Comment 9

> Username: jwakely  
> Created_at: 2018-05-17 19:32:03 UTC  
> Url: https://github.com/boostorg/logic/pull/5#issuecomment-389982460  

It works if I change:  
  
````  
  constexpr operator safe_bool() const noexcept  
  {  
    return value == true_value? &dummy::nonnull : 0;  
  }  
````  
to:  
  
````  
  constexpr explicit operator bool() const noexcept  
  {  
    return value == true_value;  
  }  
````  
  
If you're relying on `constexpr` then you don't need the safe bool idiom.  
  
Anyway, I've reported it as https://gcc.gnu.org/bugzilla/show_bug.cgi?id=85823

---

## Comment 10

> Username: jwakely  
> Created_at: 2018-05-17 19:33:45 UTC  
> Url: https://github.com/boostorg/logic/pull/5#issuecomment-389983022  

Oh I should have read the earlier comments not just the last two, I see using an explicit conversion to bool is the main topic anyway.

---

## Comment 11

> Username: jeking3  
> Created_at: 2018-07-02 00:37:32 UTC  
> Url: https://github.com/boostorg/logic/pull/5#issuecomment-401644600  

@pdimov could you add the test in https://github.com/boostorg/logic/issues/4 to this PR?  Thanks.

---

## Comment 12

> Username: jeking3  
> Created_at: 2018-07-02 00:38:11 UTC  
> Url: https://github.com/boostorg/logic/pull/5#issuecomment-401644656  

I assume at this point it's too late to merge this for 1.68.0, correct?

---

## Comment 13

> Username: mclow  
> Created_at: 2018-07-02 03:31:00 UTC  
> Url: https://github.com/boostorg/logic/pull/5#issuecomment-401662382  

Not yet, but soon.

---

## Comment 14

> Username: pdimov  
> Created_at: 2018-07-02 10:46:04 UTC  
> Url: https://github.com/boostorg/logic/pull/5#issuecomment-401762276  

The equivalent of #4 is already in `tribool_test.cpp`, commented out. This PR uncomments it.

---

## Comment 15

> Username: robertramey  
> Created_at: 2018-07-02 15:34:52 UTC  
> Url: https://github.com/boostorg/logic/pull/5#issuecomment-401845124  

FYI - there was thread on the list which dealt with this. This raised an objection that this was a "narrowing conversion" analogous to implicit conversion of a long to an int.  I found this convincing at the time.   My thinking has evolved that conversion from tribool to bool should be implicit.  I've come to believe that conversion of tribool to bool is not analogous to (silent) conversion of a long to an int.  It's correct conversion of a value of one type - tribool - to a value of another type - bool.  So the "narrowing" argument doesn't apply since tribool and bool don't really reflect underlying mathematical types.  (long and int both model real world integers).  
  
So my preference would be permitting implicit "conversion" of tribool to bool.  On the other hand, I'm also aware that these kinds of implicit conversions may make code look cleaner - but sometimes hide surprising behavior.   So I don't see a clean argument for one way or the other.

---

## Comment 16

> Username: jeking3  
> Created_at: 2018-07-05 01:30:53 UTC  
> Url: https://github.com/boostorg/logic/pull/5#issuecomment-402581572  

Please rebase on develop so a full CI suite can run on the change.  Thanks.

---

## Comment 17

> Username: pdimov  
> Created_at: 2018-07-07 17:52:04 UTC  
> Url: https://github.com/boostorg/logic/pull/5#issuecomment-403232653  

OK, done.

---

## Comment 18

> Username: codecov[bot]  
> Created_at: 2018-07-07 18:17:04 UTC  
> Updated_at: 2018-07-07 18:18:18 UTC  
> Url: https://github.com/boostorg/logic/pull/5#issuecomment-403234061  

# [Codecov](https://codecov.io/gh/boostorg/logic/pull/5?src=pr&el=h1) Report  
> Merging [#5](https://codecov.io/gh/boostorg/logic/pull/5?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/logic/commit/5d622865d399380b81702e0f2cc7f85974bb4ee2?src=pr&el=desc) will **not change** coverage.  
> The diff coverage is `n/a`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/logic/pull/5/graphs/tree.svg?width=650&height=150&src=pr&token=2RCqoPxHln)](https://codecov.io/gh/boostorg/logic/pull/5?src=pr&el=tree)  
  
```diff  
@@           Coverage Diff            @@  
##           develop       #5   +/-   ##  
========================================  
  Coverage    65.11%   65.11%             
========================================  
  Files            2        2             
  Lines          129      129             
  Branches        68       68             
========================================  
  Hits            84       84             
  Misses           2        2             
  Partials        43       43  
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/logic/pull/5?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/boost/logic/tribool.hpp](https://codecov.io/gh/boostorg/logic/pull/5/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9sb2dpYy90cmlib29sLmhwcA==) | `91.07% <ø> (ø)` | :arrow_up: |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/logic/pull/5?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/logic/pull/5?src=pr&el=footer). Last update [5d62286...05c2e57](https://codecov.io/gh/boostorg/logic/pull/5?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Comment 19

> Username: jeking3  
> Created_at: 2018-07-13 15:26:30 UTC  
> Url: https://github.com/boostorg/logic/pull/5#issuecomment-404866867  

@robertramey @pdimov it might be a good idea to satisfy yourselves that the unit tests cover the areas you are concerned with by looking at the codecov.io results.  
  
It would also be helpful if you could explicitly state whether you approve or reject the pull request as-is.  
  
I'm going to set a milestone on this for the 1.69.0 release.

---

## Comment 20

> Username: jeking3  
> Created_at: 2018-08-01 13:15:19 UTC  
> Url: https://github.com/boostorg/logic/pull/5#issuecomment-409570440  

The current (master, develop) behavior allows tribool to be implicitly converted to bool where ("true" == "true") and also ("indeterminate" or "false") == "false".  
  
Is the root cause of this issue that someone may want the indeterminate value to behave differently than that, for example there are four choices I see:  
  
1. indeterminate to bool = false (current behavior)  
2. indeterminate to bool = true (optional new behavior)  
3. indeterminate to bool = exception (optional new behavior)  
4. indeterminate to bool = execute a lambda to decide (optional new behavior)  
  
What if the constructor for a tribool allowed the behavior to be changed to suit the needs of the implementer, and therefore the conversion could always safely be implicit?

---

## Comment 21

> Username: robertramey  
> Created_at: 2018-08-01 16:17:02 UTC  
> Url: https://github.com/boostorg/logic/pull/5#issuecomment-409632026  

1. OK - this maps to current behavior where anything not true is converted to false. I'm happy with this.  
2. Nooooo!!!!  This would break any previous code which uses it.  I don't think it make any sense either.  
3. Nooooo!!!  This would change the semantics AND introduce an exception - which would break the simplicity and require documentation update.  
4. What's a lambda?  
  
the beauty of tribool is it's simplicity and obviousness.  Let's not even think of breaking this. The only question really to be discussed is whether the conversion should be implicit or explicit.  As of now I'm leaning toward permitting implicit conversion.  But I'm happy to defer to anyone who is willing to take long term responsibility for maintaining this valuable simple non-trivial component.  BTW this would include updating the documentation with the rationale which includes the points raised here so we don't have to go down the long path again.

---

## Comment 22

> Username: jeking3  
> Created_at: 2018-08-01 16:26:51 UTC  
> Url: https://github.com/boostorg/logic/pull/5#issuecomment-409635213  

I was simply suggesting that it could be up to the consumer of the class to override the default behavior and therefore have something that works for all possible cases, and thus allow for implicit conversion without fear.  Items 2 through 4 would be optional behavior one could leverage if needed.

---

## Comment 23

> Username: robertramey  
> Created_at: 2018-08-01 16:42:39 UTC  
> Url: https://github.com/boostorg/logic/pull/5#issuecomment-409640616  

I get this.  I know that you've got the best of intentions.  And there is much precedent in doing things like this to get a compromise to "please everyone".  And it works.  But there is a very real cost.  That is increased complexity and the cost is not nothing.  Were we to make such changes, then everyone who uses tribool in source code (or even looks at the source code with tribool in it) now would have to track down the documentation to figure out what it does.  Since no one can remember such minutia, this would have to be done again and again.  It's a significant cost and it hurts source code transparency.  It's "API bloat/Brain bloat".

---

## Comment 24

> Username: jeking3  
> Created_at: 2018-08-01 16:44:30 UTC  
> Url: https://github.com/boostorg/logic/pull/5#issuecomment-409641266  

Fair enough.  Thanks for the input.

---

## Comment 25

> Username: robertramey  
> Created_at: 2018-08-16 19:51:37 UTC  
> Url: https://github.com/boostorg/logic/pull/5#issuecomment-413664474  

looks like this was checked but never merged into the develop/master branches

---

## Review 26 [Commented]

> Username: jeking3  
> Created_at: 2018-08-20 12:20:17 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/logic/pull/5#pullrequestreview-147618156  

📁 test/tribool_test.cpp

```diff
 133 |-   // constexpr tribool xxx = (tribool(true) || tribool(indeterminate));
 134 |-   // static_assert(xxx, "Must be true!");
 128 |+   constexpr bool res_safe_bool = static_cast<bool>( tribool(true) );
```

> Username: jeking3  
> Created_at: 2018-08-20 12:20:17 UTC  
> Url: https://github.com/boostorg/logic/pull/5#discussion_r211241215  

res_safe_bool isn't used by anything else - is this test sufficient?  The result is not checked.

> Username: pdimov  
> Created_at: 2018-08-20 12:28:43 UTC  
> Updated_at: 2018-08-20 12:28:44 UTC  
> Url: https://github.com/boostorg/logic/pull/5#discussion_r211243339  

The fact that is `constexpr` is a test in itself, but we could in principle static_assert it.


---

## Comment 27

> Username: jeking3  
> Created_at: 2018-11-03 04:02:28 UTC  
> Url: https://github.com/boostorg/logic/pull/5#issuecomment-435558394  

I could use some help resolving:  
  
https://github.com/boostorg/interval/issues/15  
  
It looks like this is the cause.

---
