# #55 Add constexpr to operators (w/ basic docs, tests) [Merged]

> Username: tonyelewis  
> Created at: 2018-12-21 10:35:55 UTC  
> Updated at: 2020-04-12 15:58:17 UTC  
> Merged at: 2020-04-12 11:07:25 UTC  
> Closed at: 2020-04-12 11:07:25 UTC  
> Url: https://github.com/boostorg/utility/pull/55  

Following on from #54, this adds `constexpr` support to Boost.Operators.

---

## Comment 1

> Username: d-frey  
> Created_at: 2018-12-21 22:01:22 UTC  
> Url: https://github.com/boostorg/utility/pull/55#issuecomment-449509078  

While this PR *should* be fine, several compilers have problems with the non-comparison operators. Namely GCC 5 upto GCC 7 and MSVC.  
  
Can you either reduce the PR to comparison operators or find a way to fix the issues? The latter would also make the documentation slightly more complicated - it's not just C++14 anymore, it's more like "It C++14 support for `constexpr` good enough". And then one needs to decide whether this configuration should be local to Boost.Operators or if it belongs to Boost.Config.

---

## Comment 2

> Username: tonyelewis  
> Created_at: 2018-12-22 15:45:10 UTC  
> Url: https://github.com/boostorg/utility/pull/55#issuecomment-449579040  

Thanks for this. Yep - I raised [an issue](https://developercommunity.visualstudio.com/content/problem/414193/rejects-valid-constexpr-marked-friend-function-def.html#) with Microsoft about this yesterday.  
  
Just for future reference, this code demonstrates the issue:  
  
~~~cpp  
template <typename... Ts> void ignore_unused(Ts &&...) {}  
  
template <typename TheType>  
struct function_adder_for_type {  
   friend constexpr void function(const TheType &value) {  
      TheType copy_of_value( value );  
      ignore_unused( copy_of_value );  
   }  
};  
  
struct non_literal_thing : private function_adder_for_type<non_literal_thing> {  
   non_literal_thing() {}  
};  
  
int main() {  
   function( 0 );  
   return 0;  
}  
~~~  
  
Yes - maybe I should just fall back to the C++11 `constexpr` parts for now and then we can think about the C++14 `constexpr` parts after. I'm wondering if the right approach might be to drop the multi-statement code in the functions that's attempting to secure NRVO. I'm not sure if that's actually giving any benefit. What do you think?

---

## Comment 3

> Username: tonyelewis  
> Created_at: 2018-12-22 16:13:07 UTC  
> Url: https://github.com/boostorg/utility/pull/55#issuecomment-449580726  

Well that's a disappointing result. It looks like the MSVC issues affect the C++11 `constexpr` too. So maybe we need to figure out the plan for dealing with all of them. Maybe we need to disable this whole feature for MSVC and then figure out how much (if any) of the NRVO stuff we can drop to expand the C++14 `constexpr` for GCC. What do you think?

---

## Comment 4

> Username: tonyelewis  
> Created_at: 2018-12-24 06:42:31 UTC  
> Url: https://github.com/boostorg/utility/pull/55#issuecomment-449693206  

To be more explicit about my NRVO comments&hellip;  
  
The code is currently organised to accept a cost of complexity (and now of impacting constexpr implementation) to provide separate NRVO and non-NRVO versions in the hope that the NRVO version will improve copy elision. I'm not convinced about the value of this because:  
* AFAIU, compilers have been legally eliding return-value copies (where possible) both with and without names for a long time.  
* The Boost.Operators functions are very simple and short and the compiler will always know their definition at the point of use so I'd expect most vaguely-recent compilers to have little trouble with them.  
  
So I'm wondering whether we can provide any clear justification for having two separate versions.  
  
For example, fiddling with an example like the following in Godbolt (see https://godbolt.org/z/7KDpky ), it's pretty hard to find an example where the compiler generates substantially different code for the NRVO version (under `-O2`).  
  
~~~cpp  
template <typename T>  
struct nrvo_fn_adder {  
    friend T nrvo_fn(const T &prm_lhs, const T &prm_rhs) {  
        T result( prm_lhs );  
        result += prm_rhs;  
        return result;  
    }  
};  
  
template <typename T>  
struct by_value_fn_adder {  
    friend T by_value_fn(T prm_lhs, const T &prm_rhs) {  
        return prm_lhs += prm_rhs;  
    }  
};  
  
struct thing : private nrvo_fn_adder<thing>, by_value_fn_adder<thing> {  
    thing & operator+=(const thing &);  
  
    // thing();  
    // thing(const int &);  
    // thing(const thing &);  
    // thing(thing &&) noexcept;  
    // thing & operator=(const thing &);  
    // thing & operator=(thing &&) noexcept;  
  
    int val;  
    thing(const int &prm_val) : val( prm_val ) {}  
};  
  
int f1() {  
    return nrvo_fn( thing( 3 ), thing( 5 ) ).val;  
}  
  
int f2() {  
    return by_value_fn( thing( 3 ), thing( 5 ) ).val;  
}  
~~~

---

## Comment 5

> Username: tonyelewis  
> Created_at: 2018-12-24 07:52:40 UTC  
> Url: https://github.com/boostorg/utility/pull/55#issuecomment-449698918  

I've stripped this PR back to non-MSVC C++11 constexpr and it's now passing the tests.

---

## Comment 6

> Username: cdglove  
> Created_at: 2020-04-11 16:30:09 UTC  
> Url: https://github.com/boostorg/utility/pull/55#issuecomment-612458653  

I just ran into this. It's been over a year. Any movement?

---

## Comment 7

> Username: d-frey  
> Created_at: 2020-04-11 17:03:07 UTC  
> Url: https://github.com/boostorg/utility/pull/55#issuecomment-612466055  

Sorry, I should have replied sooner (I _thought_ I did, but apparently that is not true 😬).  
  
As I said earlier, I'm fine with `constexpr` for the comparison operators. But your patch does contain `constexpr` even for non-comparison operators and it does not provide any test for them. (Plus the patch towards the documentation contradicts this). If you drop the `constexpr` on the non-comparison operators, I will accept this PR.  
  
I also ran quiet a few experiments on `constexpr` myself for non-comparison operators in the past (also on https://github.com/taocpp/operators) and it never really worked consistently on the major compilers. Maybe the situation improved by now, but that would be a seperate PR.

---

## Comment 8

> Username: tonyelewis  
> Created_at: 2020-04-11 17:36:22 UTC  
> Url: https://github.com/boostorg/utility/pull/55#issuecomment-612473351  

Thanks. I've removed the `constexpr` that I'd erroneously left on some of the non-comparison operators (apologies). I've also attempted to permit constexpr for the newer versions of MSVC (that contain a fix for the bug that was previously blocking us).  
  
I should update the test to ensure the newer MSVCs run the `constexpr` tests&hellip;

---

## Comment 9

> Username: tonyelewis  
> Created_at: 2020-04-11 17:40:13 UTC  
> Url: https://github.com/boostorg/utility/pull/55#issuecomment-612474154  

&hellip;and I've now updated the tests. Ready for review please. Thanks.

---

## Comment 10

> Username: tonyelewis  
> Created_at: 2020-04-11 18:03:08 UTC  
> Url: https://github.com/boostorg/utility/pull/55#issuecomment-612478967  

BTW, is it just me that has Sade's "Smooth Operator" run through my mind when I type `<boost/operators.hpp>`?

---

## Comment 11

> Username: tonyelewis  
> Created_at: 2020-04-12 10:40:36 UTC  
> Url: https://github.com/boostorg/utility/pull/55#issuecomment-612595253  

From what I can see, it looks like the tests have all passed but that isn't updating the status in GitHub.

---

## Comment 12

> Username: tonyelewis  
> Created_at: 2020-04-12 11:58:21 UTC  
> Url: https://github.com/boostorg/utility/pull/55#issuecomment-612603494  

Thanks very much.

---

## Review 13 [Commented]

> Username: glenfe  
> Created_at: 2020-04-12 15:17:50 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/utility/pull/55#pullrequestreview-391867239  

📁 include/boost/operators.hpp

```diff
 119 |+ #else
 120 |+ #define BOOST_OPS_CONSTEXPR BOOST_CONSTEXPR
 121 |+ #endif
```

> Username: glenfe  
> Created_at: 2020-04-12 15:13:50 UTC  
> Updated_at: 2020-04-12 15:17:50 UTC  
> Url: https://github.com/boostorg/utility/pull/55#discussion_r407211784  

Should probably be simplified as:  
```  
#if BOOST_WORKAROUND(BOOST_MSVC, < 1922)  
#define BOOST_OPS_CONSTEXPR  
#else  
#define BOOST_OPS_CONSTEXPR BOOST_CONSTEXPR  
#endif  
```


📁 test/operators_test.cpp

```diff
 958 |+ #endif
 959 |+ #endif
 960 |+ 
```

> Username: glenfe  
> Created_at: 2020-04-12 15:17:36 UTC  
> Updated_at: 2020-04-12 15:17:50 UTC  
> Url: https://github.com/boostorg/utility/pull/55#discussion_r407212353  

Ideally these should go into a separate test source file (e.g. `operators_constexpr_test.cpp`) so that any defects in implementations that do not support constexpr (or if Boost.Config gets C++11 constexpr detection wrong on certain implementations) that the failures don't fail the current `operators_test.cpp` run.


---

## Comment 14

> Username: tonyelewis  
> Created_at: 2020-04-12 15:30:07 UTC  
> Url: https://github.com/boostorg/utility/pull/55#issuecomment-612633649  

I like @glenfe's suggested changes. Given that this PR has now been merged, how do you want to play it @d-frey?

---

## Comment 15

> Username: glenfe  
> Created_at: 2020-04-12 15:41:03 UTC  
> Url: https://github.com/boostorg/utility/pull/55#issuecomment-612635188  

@tonyelewis Don't worry, I'll make those changes.

---

## Comment 16

> Username: d-frey  
> Created_at: 2020-04-12 15:58:17 UTC  
> Url: https://github.com/boostorg/utility/pull/55#issuecomment-612637549  

Good suggestions and thanks Glen for taking care of it!

---
