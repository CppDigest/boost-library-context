# #25 Added variadic support [Merged]

> Username: Kojoley  
> Created at: 2018-10-18 19:44:21 UTC  
> Updated at: 2018-11-14 17:22:28 UTC  
> Merged at: 2018-10-28 01:04:01 UTC  
> Closed at: 2018-10-28 01:04:01 UTC  
> Url: https://github.com/boostorg/function/pull/25  

Improves `boost/function.hpp` inclusion by 20%

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2018-10-20 16:56:52 UTC  
> Updated_at: 2018-10-20 19:31:16 UTC  
> Url: https://github.com/boostorg/function/pull/25#issuecomment-431598886  

# [Codecov](https://codecov.io/gh/boostorg/function/pull/25?src=pr&el=h1) Report  
> Merging [#25](https://codecov.io/gh/boostorg/function/pull/25?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/function/commit/453860ff9cfac59c53bd3936e95324b2e6318b97?src=pr&el=desc) will **not change** coverage.  
> The diff coverage is `n/a`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/function/pull/25/graphs/tree.svg?width=650&token=beMMliwLU7&height=150&src=pr)](https://codecov.io/gh/boostorg/function/pull/25?src=pr&el=tree)  
  
```diff  
@@           Coverage Diff            @@  
##           develop      #25   +/-   ##  
========================================  
  Coverage    54.24%   54.24%             
========================================  
  Files            4        4             
  Lines          389      389             
  Branches        98       98             
========================================  
  Hits           211      211             
  Misses          85       85             
  Partials        93       93  
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/function/pull/25?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/boost/function/function\_fwd.hpp](https://codecov.io/gh/boostorg/function/pull/25/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9mdW5jdGlvbi9mdW5jdGlvbl9md2QuaHBw) | `100% <ø> (ø)` | :arrow_up: |  
| [include/boost/function/function\_template.hpp](https://codecov.io/gh/boostorg/function/pull/25/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9mdW5jdGlvbi9mdW5jdGlvbl90ZW1wbGF0ZS5ocHA=) | `64% <ø> (ø)` | :arrow_up: |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/function/pull/25?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/function/pull/25?src=pr&el=footer). Last update [453860f...3ab28db](https://codecov.io/gh/boostorg/function/pull/25?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Review 2 [Commented]

> Username: pdimov  
> Created_at: 2018-10-20 17:39:36 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/function/pull/25#pullrequestreview-166756686  

📁 include/boost/function/function_template.hpp

```diff
 705 |+ 
 706 |+       template <typename T0, typename T1, typename... T>
 707 |+       struct variadic_function_base<T0, T1, T...>
```

> Username: pdimov  
> Created_at: 2018-10-20 17:39:36 UTC  
> Updated_at: 2018-10-20 17:45:28 UTC  
> Url: https://github.com/boostorg/function/pull/25#discussion_r226834294  

`first_argument_type` and `second_argument_type` are only defined for arity 2, not 3 and above.

> Username: Kojoley  
> Created_at: 2018-10-20 17:45:43 UTC  
> Url: https://github.com/boostorg/function/pull/25#discussion_r226834437  

Fixed.


---

## Comment 3

> Username: Kojoley  
> Created_at: 2018-10-20 17:50:48 UTC  
> Url: https://github.com/boostorg/function/pull/25#issuecomment-431603996  

I am not sure about `functionN` aliases. It is a kind of API break and this bothers me. I tried to do this  
```  
    template <typename R, typename... T>                                      \  
    class function##N : public function_n<R, T...>                            \  
    {                                                                         \  
    public:                                                                   \  
        using function_n<R, T...>::function_n;                                \  
        using function_n<R, T...>::operator=;                                 \  
    };  
```  
but MSVC has problems with sfinaeing out constructors.

---

## Comment 4

> Username: pdimov  
> Created_at: 2018-10-20 17:59:03 UTC  
> Url: https://github.com/boostorg/function/pull/25#issuecomment-431604587  

As long as existing code using `functionN` continues to work, it'll be fine, I think. Nobody would use these in new code.

---

## Comment 5

> Username: jeking3  
> Created_at: 2018-10-26 11:44:35 UTC  
> Url: https://github.com/boostorg/function/pull/25#issuecomment-433380835  

Does this need any documentation changes?

---

## Comment 6

> Username: Kojoley  
> Created_at: 2018-10-26 11:52:01 UTC  
> Url: https://github.com/boostorg/function/pull/25#issuecomment-433382489  

> Does this need any documentation changes?  
  
It does not change the interface in any way, someone just may use more than 50 parameter function if he wishes (crazy thing, but who knows how someone uses it). I would be fine to delegate this task to someone else (because I really do not know what to write except `added varadic support` to the changelog) and also to fix the Perl script that generates `maybe_include.hpp` (I do not know Perl :smiley:)

---

## Comment 7

> Username: jeking3  
> Created_at: 2018-10-27 20:27:50 UTC  
> Url: https://github.com/boostorg/function/pull/25#issuecomment-433652733  

This change has made me a little nervous (only because of how the headers are handled, and how much function in used...) so I was trying to run the full boost/status build (which runs all the tests) however I ran into problems, noted on the mailing list.  I'd like to see a full suite of the following pass before I merge:  
  
```  
~/boost/status# ../b2 cxxstd=03,11,14,17,2x toolset=clang,gcc variant=debug,release  
```  
  
The variant may not be as important; and in the docker container this tests with gcc-7.3 and clang-6.0.  But it's a better data point than the current CI because it runs any other module tests that also depend on function.

---

## Comment 8

> Username: Kojoley  
> Created_at: 2018-10-27 22:48:26 UTC  
> Url: https://github.com/boostorg/function/pull/25#issuecomment-433661488  

On my machine 2*2*5 full runs will take a full week. Is not `develop` branch made for this purpose? In the worst case scenario you can revert the commit at any moment.

---

## Comment 9

> Username: jeking3  
> Created_at: 2018-10-28 01:03:57 UTC  
> Url: https://github.com/boostorg/function/pull/25#issuecomment-433667780  

Well it was worth the effort, since I found some other issues not related to this.

---

## Comment 10

> Username: pdimov  
> Created_at: 2018-11-13 14:22:17 UTC  
> Url: https://github.com/boostorg/function/pull/25#issuecomment-438282950  

This change broke `functionN` on C++03, as reported in https://github.com/boostorg/program_options/issues/65

---

## Comment 11

> Username: pdimov  
> Created_at: 2018-11-13 18:02:46 UTC  
> Url: https://github.com/boostorg/function/pull/25#issuecomment-438375089  

It's probably best to revert this change for now.

---

## Comment 12

> Username: pdimov  
> Created_at: 2018-11-13 19:13:43 UTC  
> Url: https://github.com/boostorg/function/pull/25#issuecomment-438399512  

It'd also probably be best if I assume the responsibility of maintaining Function. @jeking3, please remove it from your Appveyor account; I'll add it to mine.

---

## Comment 13

> Username: Kojoley  
> Created_at: 2018-11-13 20:03:05 UTC  
> Updated_at: 2018-11-13 20:33:27 UTC  
> Url: https://github.com/boostorg/function/pull/25#issuecomment-438416598  

> This change broke functionN on C++03  
  
Maybe I am missing something, but it did not broke C++03, it broke portable syntax API compatibility between C++03 and C++11+ and it was expected.  
  
> It's probably best to revert this change for now.  
  
OK. I just should have migrated Spirit to `std::function` without spending time on this...

---

## Comment 14

> Username: jeking3  
> Created_at: 2018-11-13 20:20:43 UTC  
> Url: https://github.com/boostorg/function/pull/25#issuecomment-438422316  

I'd say there was a lack of existing testing in the unit tests to catch the issue that occurred; or a lack of new testing, or a lack of understanding in the code reviews.  Given the code coverage is reasonably low at 54% branch coverage, that should be a focus for improvement.  
  
In any case, the CMT welcomes your help in reducing the burden.  I will disable my AppVeyor build job.  I am no longer able to modify the Trac page that shows the repositories managed by CMT, since trac went read-only:  
  
https://svn.boost.org/trac10/wiki/CommunityMaintenance  
  
Could you update that and remove function from the list?  
  
Thanks.

---

## Comment 15

> Username: pdimov  
> Created_at: 2018-11-13 20:31:23 UTC  
> Url: https://github.com/boostorg/function/pull/25#issuecomment-438425697  

> Maybe I am missing something, but it did not broke C++03, it made C++03 and C++11 libraries ABI incompatible and it was expected.  
  
Yes, you are correct. Now that I reread this thread, you indeed anticipated this incompatibility, but I didn't understand you at the time. Sorry.  
  
It may be possible to reintroduce this change in a compatible way later, but now is not the time.

---

## Comment 16

> Username: pdimov  
> Created_at: 2018-11-13 20:58:04 UTC  
> Url: https://github.com/boostorg/function/pull/25#issuecomment-438433926  

Unfortunately, your idea of deriving `functionN<R, T...>` from `function_n<R, T...>` won't work, because variadic templates are mangled differently from non-variadic ones (except on MSVC, where we don't have a problem.)

---

## Comment 17

> Username: Kojoley  
> Created_at: 2018-11-13 21:58:18 UTC  
> Url: https://github.com/boostorg/function/pull/25#issuecomment-438453815  

`functionN` signature can be non-variadic with a little macro magic, but it will not solve the other problem I did not think about earlier, that `function` will not be convertible to `functionN`.

---

## Comment 18

> Username: pdimov  
> Created_at: 2018-11-13 22:10:14 UTC  
> Url: https://github.com/boostorg/function/pull/25#issuecomment-438457504  

It will be convertible (since all function<> things can be constructed from any compatible function object), but it will not derive from it, which in principle could break some contrived uses. Not sure whether we'll hit any such cases in practice though.

---

## Comment 19

> Username: Kojoley  
> Created_at: 2018-11-14 17:14:32 UTC  
> Url: https://github.com/boostorg/function/pull/25#issuecomment-438742537  

Btw, originally I was thinking about `template <typename R, typename... Args> using function##N = function<R(Args...)>` alias definition, now that solution looks more clever to me, it should have prevented the boostorg/program_options#65 problem.

---

## Comment 20

> Username: pdimov  
> Created_at: 2018-11-14 17:22:28 UTC  
> Url: https://github.com/boostorg/function/pull/25#issuecomment-438745378  

It wouldn't have; libboost_program_options would have had `function<...>` in the signatures and the .exe would have looked for `function1<...>`. You can try it now and see, I added tests that check this case.

---
