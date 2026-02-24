# #38 - Add tests for floats with lightweight_tests [Closed]

> Username: HDembinski  
> Created at: 2018-08-03 20:41:18 UTC  
> Updated at: 2020-06-07 12:44:47 UTC  
> Closed at: 2020-06-07 12:44:47 UTC  
> Url: https://github.com/boostorg/core/issues/38  

It would be nice to have a test for floats. Something like this:  
```  
#define BOOST_TEST_IS_CLOSE(a, b, eps) BOOST_TEST(std::abs(a - b) < eps)  
```  
I am happy to make a PR if that helps.

---

## Comment 1

> Username: breese  
> Created at: 2018-08-03 21:23:02 UTC  
> Url: https://github.com/boostorg/core/issues/38#issuecomment-410379981  

I second that request.  
  
I have created one as an extension for my own projects. It uses a separate implementation function in order to print the argument of failed tests. The tolerance can be specified.  
  
My implementation looks like this (modulo the test macro)  
```  
template <typename T>  
void test_close_impl(char const * expr1,  
                     char const * expr2,  
                     char const * file, int line, char const * function,  
                     const T& lhs, const T& rhs, T tolerance)  
{  
    if (std::fabs(lhs - rhs) <= tolerance)  
    {  
        boost::detail::report_errors_remind();  
    }  
    else  
    {  
        BOOST_LIGHTWEIGHT_TEST_OSTREAM  
            << file << "(" << line << "): test '" << expr1 << " == " << expr2  
            << "' failed in function '" << function << "': "  
            << "'" << lhs << "' != '" << rhs << "'" << std::endl;  
        ++boost::detail::test_errors();  
    }  
}  
```

---

## Comment 2

> Username: HDembinski  
> Created at: 2018-08-04 07:29:11 UTC  
> Url: https://github.com/boostorg/core/issues/38#issuecomment-410430686  

Makes sense to generate a custom message for this case. Maybe even note the tolerance in the message? Something like x != y (tolerance: z).

---

## Comment 3

> Username: breese  
> Created at: 2018-08-04 14:58:41 UTC  
> Url: https://github.com/boostorg/core/issues/38#issuecomment-410455317  

Just to float (pun intended) another idea.  
  
We could define a `with_tolerance` predicate, similar to `std::equal_to` et al, and use that with `BOOST_TEST(p(x,y))`. This would also make `BOOST_TEST_ALL_WITH` slightly easier to use.  
  
We even have a sample implementation of [with_tolerance](https://github.com/breese/core/blob/develop/test/lightweight_test_all_with_test.cpp#L40) in the test suite.

---

## Comment 4

> Username: HDembinski  
> Created at: 2018-08-04 15:16:47 UTC  
> Updated at: 2018-08-04 15:19:31 UTC  
> Url: https://github.com/boostorg/core/issues/38#issuecomment-410456400  

This is also nice. Perhaps all the implementations `test_eq_impl`, `test_lt_impl`, ... could be replaced with one generic backend. The predicate could then tweak the output. Or better we make a traits class that customizes the output based on the type of predicate.  
  
With such a generic approach, one could also implement checks for relative and for absolute tolerance. Both are sometimes useful.  
  
PS: @breese Pun is appreciated ;)

---

## Comment 5

> Username: breese  
> Created at: 2018-08-05 16:20:51 UTC  
> Url: https://github.com/boostorg/core/issues/38#issuecomment-410530984  

We can handle relative and absolute tolerances in the same check (formula taken from [Python PEP 485](https://www.python.org/dev/peps/pep-0485/)) but name changed to better match `std::equal_to`:  
```  
#include <cmath> // std::abs  
#include <algorithm> // std::max  
#include <limits> // std::numeric_limits  
  
template <typename T>  
struct close_to  
{  
    close_to(T relative_tolerance = std::numeric_limits<T>::epsilon(),  
             T absolute_tolerance = {})  
        : relative(relative_tolerance),  
          absolute(absolute_tolerance)  
    {}  
  
    bool operator() (const T& lhs, const T& rhs)  
    {  
        return std::abs(lhs - rhs) <= std::max(relative * std::max(std::abs(lhs), std::abs(rhs)), absolute);  
    }  
  
private:  
    T relative;  
    T absolute;  
};  
```  
This can the be used like this:  
```  
auto where = std::adjacent_find(numbers.begin(), numbers.end(), close_to<float>());  
```  
If we only want relative tolerance, initialize absolute tolerance to 0, and likewise for absolute tolerance.

---

## Comment 6

> Username: pdimov  
> Created at: 2018-08-08 13:51:03 UTC  
> Updated at: 2018-08-08 13:51:30 UTC  
> Url: https://github.com/boostorg/core/issues/38#issuecomment-411412561  

The `T` parameter is a bit of a problem because we probably don't want separate `BOOST_TEST_CLOSE` macros for each type. (And we can't use `decltype`.)  
  
One option is to make `close_to` non-templated, with the usual templated `operator()`, and express relative tolerance in units of `epsilon` (ulp) and absolute tolerance in units of `min`. (The default for absolute tolerance needs to be `min` IMO as per https://en.cppreference.com/w/cpp/types/numeric_limits/epsilon .) This way the parameters can be integers, independent of `T`.  
  
The other option is to define `BOOST_TEST_CLOSE(x, y)` to call a helper function `get_close_to(x-y)` that returns the predicate.  
  
```  
template<class T> close_to<T> get_close_to( T ) { return close_to<T>(); }  
```  
  
suitably extended for the custom tolerance version.  
  
The zero case, and the `BOOST_TEST_CLOSE(x, -x)` case for very small `x`, need some thought.

---

## Comment 7

> Username: HDembinski  
> Created at: 2018-08-08 14:03:18 UTC  
> Url: https://github.com/boostorg/core/issues/38#issuecomment-411416966  

I think expressing the tolerances in integral units of `epsilon` and `min` is not so convenient, so I would prefer the second option with a generating function.  
  
I think the zero case and the case with very small `x` is already handled by the option to give an absolute tolerance in addition to a relative tolerance, or am I missing something?

---

## Comment 8

> Username: pdimov  
> Created at: 2018-08-08 14:11:31 UTC  
> Url: https://github.com/boostorg/core/issues/38#issuecomment-411419713  

One additional difficulty is that we can't use variadic macros either. So while the constructor of `close_to<T>` may have default arguments for ease of use, we can't make the macro behave the same way.

---

## Comment 9

> Username: pdimov  
> Created at: 2018-08-08 14:16:42 UTC  
> Url: https://github.com/boostorg/core/issues/38#issuecomment-411421505  

Either way, I think we need the absolute tolerance first, as it would be more common to specify it.

---

## Comment 10

> Username: breese  
> Created at: 2018-08-08 14:37:53 UTC  
> Url: https://github.com/boostorg/core/issues/38#issuecomment-411429085  

I usually provide my own tolerances when there may be accumulating errors (or portability issues) that I have to account for.  
  
The reason for having the tolerances as member variables is to be able to create a `close_to` object with non-standard tolerances that can be reused throughout my test suite. If the tolerances are specified as parameters to `operator()` then I need to add the tolerances to each test check (which is what I am doing today, and I really would like to avoid that.)  
  
My use cases are probably better served by a (hypothetical) `BOOST_TEST_WITH(x, y, p)` and the (existing) `BOOST_TEST_ALL_WITH(<insert iterators here>, p)` than by a `BOOST_TEST_CLOSE` macro.

---

## Comment 11

> Username: breese  
> Created at: 2018-08-08 14:38:46 UTC  
> Url: https://github.com/boostorg/core/issues/38#issuecomment-411429383  

Good point about `min` being the default for absolute tolerance.

---

## Comment 12

> Username: breese  
> Created at: 2018-08-10 13:30:16 UTC  
> Url: https://github.com/boostorg/core/issues/38#issuecomment-412082581  

We did not really solve the problem @HDembinski originally brought up.  
  
After some deliberation, I believe that we are best of forgetting about the `close_to` predicate (even though I still consider it useful) and simply specify two macros, `BOOST_TEST_ABSOLUTELY_CLOSE` and `BOOOST_TEST_RELATIVELY_CLOSE`, and let the predicates be an implementation detail.  
  
Bikeshedding welcome, we should especially consider names that do not collide with those in Boost.Test.

---

## Comment 13

> Username: HDembinski  
> Created at: 2018-08-10 13:53:13 UTC  
> Url: https://github.com/boostorg/core/issues/38#issuecomment-412088978  

Why is it important that the names do not clash? I think you will not include both Boost.Test headers and lightweight_test headers at the same time?

---

## Comment 14

> Username: HDembinski  
> Created at: 2018-08-10 14:13:08 UTC  
> Url: https://github.com/boostorg/core/issues/38#issuecomment-412094778  

Ah, maybe you meant it in the other way, the macro names should be consistent...

---

## Comment 15

> Username: Lastique  
> Created at: 2018-08-10 14:26:11 UTC  
> Url: https://github.com/boostorg/core/issues/38#issuecomment-412098558  

On 8/10/18 4:53 PM, Hans Dembinski wrote:  
> Why is it important that the names do not clash? I think you will not   
> include both Boost.Test headers and lightweight_test headers at the same   
> time?  
  
Even if unlikely, I would strongly prefer to not introduce the naming   
conflict, if possible.

---

## Comment 16

> Username: HDembinski  
> Created at: 2018-08-10 14:39:32 UTC  
> Url: https://github.com/boostorg/core/issues/38#issuecomment-412102601  

I think it is already there. Both define `BOOST_TEST`

---

## Comment 17

> Username: Lastique  
> Created at: 2018-08-10 14:44:03 UTC  
> Url: https://github.com/boostorg/core/issues/38#issuecomment-412103972  

On 8/10/18 5:39 PM, Hans Dembinski wrote:  
> I think it is already there. Both define |BOOST_TEST|  
  
Then let's not make it worse.

---

## Comment 18

> Username: HDembinski  
> Created at: 2018-08-10 14:49:07 UTC  
> Url: https://github.com/boostorg/core/issues/38#issuecomment-412105514  

For reference:  
https://www.boost.org/doc/libs/1_66_0/libs/test/doc/html/boost_test/utf_reference/testing_tool_ref/assertion_boost_level_close.html  
It is called `BOOST_TEST_CLOSE` in Boost.Test and it accepts a relative tolerance in percentage points. That is not intuitive.  
  
After all, I like `BOOST_TEST_WITH(a, b, pred)` best, where you give the predicate yourself. It is not more typing than `BOOST_TEST_ABSOLUTELY_CLOSE` or `BOOST_TEST_RELATIVELY_CLOSE` and one can use defaults for the arguments.

---

## Comment 19

> Username: HDembinski  
> Created at: 2018-08-10 14:55:55 UTC  
> Updated at: 2018-08-10 14:56:03 UTC  
> Url: https://github.com/boostorg/core/issues/38#issuecomment-412107907  

> Then let's not make it worse.  
  
That sounds good, but what's the harm if it is already broken? Right now, you cannot use Boost.Test and lightweight_test in the same cpp file. So, I don't see why we should worry about conflicts at this point.

---

## Comment 20

> Username: Lastique  
> Created at: 2018-08-10 15:02:07 UTC  
> Url: https://github.com/boostorg/core/issues/38#issuecomment-412109839  

On 8/10/18 5:55 PM, Hans Dembinski wrote:  
>     Then let's not make it worse.  
>     That sounds good, but what's the harm if it is already broken? Right  
>     now, you cannot use Boost.Test and lightweight_test in the same cpp  
>     file. So, I don't see why we should worry about conflicts at this point.  
  
We may decide to fix this in the future, adding more clashes would make   
this more difficult.  
  
Personally, I think the current conflict is an unfortunate oversight of   
the original maintainer of lightweight_test and is now ours (Boost.Core   
maintainers). No name clashes or other conflicts between libraries in   
Boost is a rule of thumb, something users have come to expect. I   
consider that lightweight_test uses Boost.Test namespace (in macro   
names) a bug.

---

## Comment 21

> Username: breese  
> Created at: 2018-08-10 17:18:50 UTC  
> Url: https://github.com/boostorg/core/issues/38#issuecomment-412148514  

Besides `BOOST_TEST_CLOSE` there is also [`BOOST_TEST_CLOSE_FRACTION`](https://www.boost.org/doc/libs/test/doc/html/boost_test/utf_reference/testing_tool_ref/assertion_boost_level_close_fraction.html)

---

## Comment 22

> Username: HDembinski  
> Created at: 2018-08-13 07:54:18 UTC  
> Url: https://github.com/boostorg/core/issues/38#issuecomment-412436844  

Boost.Test does not have BOOST_TEST_WITH (they have a similar macro, which is called differently), so if we only implement that instead of BOOST_TEST_CLOSE, there is no additional name macro conflict.

---

## Comment 23

> Username: mloskot  
> Created at: 2020-03-19 23:20:51 UTC  
> Url: https://github.com/boostorg/core/issues/38#issuecomment-601460636  

Is there any agreement and PR coming soon?

---

## Comment 24

> Username: pdimov  
> Created at: 2020-03-19 23:45:27 UTC  
> Url: https://github.com/boostorg/core/issues/38#issuecomment-601466701  

We already internally implement BOOST_TEST_EQ et al with predicates, so BOOST_TEST_WITH would be trivial to add.

---

## Comment 25

> Username: breese  
> Created at: 2020-03-20 00:50:44 UTC  
> Url: https://github.com/boostorg/core/issues/38#issuecomment-601481559  

The `test_with_impl` function now takes a special predicate with a member function to return the operator name. This means that we would need to wrap the user-provided predicate somehow.

---

## Comment 26

> Username: pdimov  
> Created at: 2020-03-20 01:02:53 UTC  
> Url: https://github.com/boostorg/core/issues/38#issuecomment-601483874  

Or the user could just provide a member function in his predicate.

---

## Comment 27

> Username: breese  
> Created at: 2020-03-20 09:48:29 UTC  
> Url: https://github.com/boostorg/core/issues/38#issuecomment-601613517  

Then our interfaces for `BOOST_TEST_WITH` and `BOOST_TEST_ALL_WITH` would differ. The former taking a home-brewed predicate concept and the latter taking the well-known BinaryPredicate concept.
