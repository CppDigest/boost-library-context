# #39 - regression: compile error with pair [Closed]

> Username: vinniefalco  
> Created at: 2017-10-29 15:06:58 UTC  
> Updated at: 2018-10-29 20:46:24 UTC  
> Closed at: 2018-10-29 20:46:24 UTC  
> Url: https://github.com/boostorg/optional/issues/39  

https://travis-ci.org/vinniefalco/beast/jobs/294451968#L1450  
  
@pdimov >apparently `optional<>` tries to use `boost::has_trivial_default_constructor` which doesn't compile for `pair<X, Y>` where X has a deleted default constructor

---

## Comment 1

> Username: pdimov  
> Created at: 2017-10-29 15:45:15 UTC  
> Url: https://github.com/boostorg/optional/issues/39#issuecomment-340271707  

This is hard to reproduce. `type_traits` uses intrinsics where available, which do not fail this case. We can disable the intrinsics for the test with `#define BOOST_TT_DISABLE_INTRINSICS`, but then `optional` fails in another way, as it uses `is_base_and_derived<typed_in_place_factory_base, D>` with an incomplete `typed_in_place_factory_base`.  
  
```  
#define BOOST_TT_DISABLE_INTRINSICS  
#include <boost/optional.hpp>  
  
template<class T1, class T2> struct pair  
{  
	T1 first;  
	T2 second;  
  
	pair(): first(), second()  
	{  
	}  
};  
  
struct A  
{  
	A() = delete;  
};  
  
int main()  
{  
	boost::optional< pair<A, int> > opt, opt2;  
	opt = opt2;  
}  
```

---

## Comment 2

> Username: pdimov  
> Created at: 2017-10-29 15:47:42 UTC  
> Url: https://github.com/boostorg/optional/issues/39#issuecomment-340271898  

I've enabled Travis so that we can get the PRs tested. https://travis-ci.org/boostorg/optional

---

## Comment 3

> Username: pdimov  
> Created at: 2017-10-29 16:22:54 UTC  
> Url: https://github.com/boostorg/optional/issues/39#issuecomment-340274572  

Actually the problem is even subtler; it's caused by  
  
```  
#if (defined(__GNUC__) && (__GNUC__ * 100 + __GNUC_MINOR__ >= 409)) || defined(BOOST_CLANG) || (defined(__SUNPRO_CC) && defined(BOOST_HAS_TRIVIAL_CONSTRUCTOR))  
#include <boost/type_traits/is_default_constructible.hpp>  
#define BOOST_TT_TRIVIAL_CONSTRUCT_FIX && is_default_constructible<T>::value  
```  
  
so intrinsics have to be enabled, and the compiler has to be `g++` or `clang`, and it's then `is_default_constructible` that fails, apparently not on all versions.

---

## Comment 4

> Username: vinniefalco  
> Created at: 2017-10-29 16:26:03 UTC  
> Url: https://github.com/boostorg/optional/issues/39#issuecomment-340274771  

I'm having no luck writing a test for this

---

## Comment 5

> Username: pdimov  
> Created at: 2017-10-29 17:00:43 UTC  
> Url: https://github.com/boostorg/optional/issues/39#issuecomment-340277132  

I had no luck reproducing it either with https://github.com/boostorg/optional/pull/40.

---

## Comment 6

> Username: vinniefalco  
> Created at: 2017-10-29 18:54:24 UTC  
> Url: https://github.com/boostorg/optional/issues/39#issuecomment-340285152  

Looks like a problem in Boost.TypeTraits, see https://github.com/boostorg/type_traits/pull/52

---

## Comment 7

> Username: pdimov  
> Created at: 2017-10-29 22:08:49 UTC  
> Url: https://github.com/boostorg/optional/issues/39#issuecomment-340306047  

My second attempt in #40 has reproduced the error.

---

## Comment 8

> Username: akrzemi1  
> Created at: 2017-10-31 11:05:16 UTC  
> Url: https://github.com/boostorg/optional/issues/39#issuecomment-340729856  

Do you still see the error after the recent commit?

---

## Comment 9

> Username: vinniefalco  
> Created at: 2017-10-31 13:45:53 UTC  
> Url: https://github.com/boostorg/optional/issues/39#issuecomment-340766657  

Fewer errors, but still failing:  
https://travis-ci.org/vinniefalco/beast/jobs/295136254

---

## Comment 10

> Username: akrzemi1  
> Created at: 2017-11-01 05:43:12 UTC  
> Url: https://github.com/boostorg/optional/issues/39#issuecomment-340989600  

But I guess, this is some different case now? The isolated test case from this thread is passing tests in test matrix in GCC 6.

---

## Comment 11

> Username: vinniefalco  
> Created at: 2017-11-01 07:01:02 UTC  
> Updated at: 2017-11-01 07:01:07 UTC  
> Url: https://github.com/boostorg/optional/issues/39#issuecomment-341007626  

>this is some different case now  
  
Yes

---

## Comment 12

> Username: vinniefalco  
> Created at: 2017-11-01 07:53:35 UTC  
> Url: https://github.com/boostorg/optional/issues/39#issuecomment-341025473  

I tried to reproduce it but no luck:  
https://github.com/vinniefalco/optional/commit/f938473ba848bf83c33120741f9edc9b1d49b731  
  
There was a failure, but unrelated to my test:  
https://travis-ci.org/vinniefalco/optional/jobs/295629944

---

## Comment 13

> Username: vinniefalco  
> Created at: 2017-11-01 16:52:56 UTC  
> Url: https://github.com/boostorg/optional/issues/39#issuecomment-341166957  

This fails to compile on some gcc with C++11, it reproduces the error I am seeing  
```  
struct NotDefaultConstructible  
{  
	NotDefaultConstructible() = delete;  
};  
  
void test_tc_base()  
{  
  boost::optional<NotDefaultConstructible> o;  
    
  BOOST_TEST(boost::none == o);  
}  
  
int main()  
{  
  test_tc_base();  
  return boost::report_errors();  
}  
```

---

## Comment 14

> Username: akrzemi1  
> Created at: 2018-10-29 20:46:24 UTC  
> Url: https://github.com/boostorg/optional/issues/39#issuecomment-434073167  

I added this test case to boost::optional tests. After reverting changes this bug is gone.
