# #406 - BOOST_HANA_CONFIG_HAS_CONSTEXPR_LAMBDA and BOOST_HANA_CONSTEXPR_CHECK [Open]

> Username: xiangfan-ms  
> Created at: 2018-05-30 23:05:22 UTC  
> Updated at: 2018-06-04 21:59:52 UTC  
> Url: https://github.com/boostorg/hana/issues/406  

I tried to enable BOOST_HANA_CONFIG_HAS_CONSTEXPR_LAMBDA once and see some errors when building with MSVC and /std:c++17.  
  
I only investigated several failures and one of them seems to be source issue:  
  
test\assert\constexpr.cpp  
    BOOST_HANA_CONSTEXPR_ASSERT(runtime_bool<true>());  
  
runtime_bool isn't a constexpr function.  
  
Looks that this macro is deliberately left as undefined, so this issue is just a FYI.

---

## Comment 1

> Username: xiangfan-ms  
> Created at: 2018-05-31 00:18:25 UTC  
> Url: https://github.com/boostorg/hana/issues/406#issuecomment-393360525  

BTW, clang-cl rejects the following code in test\_include\laws\foldable.hpp:  
  
            BOOST_HANA_CONSTEXPR_CHECK(equal(  
                fold_left(list(1), z, f),  
                f(z, 1)  
            ));  
            BOOST_HANA_CONSTEXPR_CHECK(equal(  
                fold_left(list(1, '2'), z, f),  
                f(f(z, 1), '2')  
            ));  
            BOOST_HANA_CONSTEXPR_CHECK(equal(  
                fold_left(list(1, '2', 3.3), z, f),  
                f(f(f(z, 1), '2'), 3.3)  
            ));  
            BOOST_HANA_CONSTEXPR_CHECK(equal(  
                fold_left(list(1, '2', 3.3, 4.4f), z, f),  
                f(f(f(f(z, 1), '2'), 3.3), 4.4f)  
            ));  
  
  
            BOOST_HANA_CONSTEXPR_CHECK(equal(  
                fold_right(list(1), z, f),  
                f(1, z)  
            ));  
            BOOST_HANA_CONSTEXPR_CHECK(equal(  
                fold_right(list(1, '2'), z, f),  
                f(1, f('2', z))  
            ));  
            BOOST_HANA_CONSTEXPR_CHECK(equal(  
                fold_right(list(1, '2', 3.3), z, f),  
                f(1, f('2', f(3.3, z)))  
            ));  
            BOOST_HANA_CONSTEXPR_CHECK(equal(  
                fold_right(list(1, '2', 3.3, 4.4f), z, f),  
                f(1, f('2', f(3.3, f(4.4f, z))))  
            ));  
  
  
                BOOST_HANA_CONSTEXPR_CHECK(equal(  
                    minimum(list(ord<33>{}, short{45}, long{46})),  
                    ord<33>{}  
                ));  
  
I didn't dig deeper to see whether this is a source issue or a compiler issue, though.

---

## Comment 2

> Username: xiangfan-ms  
> Created at: 2018-05-31 00:20:24 UTC  
> Url: https://github.com/boostorg/hana/issues/406#issuecomment-393360838  

Here is the list of other failed tests (from MSVC):  
  
1>------ Build started: Project: example.misc.infinite_set, Configuration: Debug Win32 ------  
3>------ Build started: Project: test.basic_tuple.laws, Configuration: Debug Win32 ------  
4>------ Build started: Project: test.concept.sequence.iterable, Configuration: Debug Win32 ------  
6>------ Build started: Project: test.ext.boost.tuple.iterable, Configuration: Debug Win32 ------  
7>------ Build started: Project: test.ext.boost.tuple.monad_plus, Configuration: Debug Win32 ------  
8>------ Build started: Project: test.ext.boost.tuple.searchable, Configuration: Debug Win32 ------  
9>------ Build started: Project: test.ext.std.tuple.laws, Configuration: Debug Win32 ------  
10>------ Build started: Project: test.foldable.fold_left_mcd.iterable, Configuration: Debug Win32 ------  
11>------ Build started: Project: test.foldable.iterable_mcd.iterable, Configuration: Debug Win32 ------  
12>------ Build started: Project: test.foldable.unpack_mcd.iterable, Configuration: Debug Win32 ------  
13>------ Build started: Project: test.numeric.minus_mcd, Configuration: Debug Win32 ------  
14>------ Build started: Project: test.numeric.negate_mcd, Configuration: Debug Win32 ------  
15>------ Build started: Project: test.tuple.laws, Configuration: Debug Win32 ------  
  
I didn't investigate the root cause of the failure or try them using clang-cl.

---

## Comment 3

> Username: xiangfan-ms  
> Created at: 2018-05-31 00:38:44 UTC  
> Url: https://github.com/boostorg/hana/issues/406#issuecomment-393364032  

Regarding the issue mentioned in my second post, looks that 'Tracked' is used in 'injection_result' and 'integer' ('_constant' derives from 'integer'). However, 'Tracked' isn't a literal type and neither are the types containing it.  
See 'test\_include\laws\base.hpp' and 'test\_include\support\tracked.hpp' for details.  
This will fail the constexpr evaluation.

---

## Comment 4

> Username: xiangfan-ms  
> Created at: 2018-05-31 18:16:45 UTC  
> Url: https://github.com/boostorg/hana/issues/406#issuecomment-393625722  

The issue in the following two projects are in 'test\numeric\main.hpp':  
  
13>------ Build started: Project: test.numeric.minus_mcd, Configuration: Debug Win32 ------  
14>------ Build started: Project: test.numeric.negate_mcd, Configuration: Debug Win32 ------  
  
The related code:  
  
        auto x = numeric(1);  
        auto y = numeric(2);  
  
Looks that constexpr is missing on these two local variables.

---

## Comment 5

> Username: xiangfan-ms  
> Created at: 2018-05-31 22:05:13 UTC  
> Url: https://github.com/boostorg/hana/issues/406#issuecomment-393695834  

The issue in the following three projects are caused by the fact that boost::tuples::tuple isn't literal type (it doesn't use constexpr):  
  
6>------ Build started: Project: test.ext.boost.tuple.iterable, Configuration: Debug Win32 ------  
7>------ Build started: Project: test.ext.boost.tuple.monad_plus, Configuration: Debug Win32 ------  
8>------ Build started: Project: test.ext.boost.tuple.searchable, Configuration: Debug Win32 ------  
  
In summary, there are 4 kinds of source issues exposed after changing BOOST_HANA_CONSTEXPR_ASSERT to BOOST_HANA_CONSTANT_ASSERT (it is implied by BOOST_HANA_CONFIG_HAS_CONSTEXPR_LAMBDA, even though the issues are not related to constexpr lambda).  
  
1. Incorrect constant assertion on runtime condition  
test\assert\constexpr.cpp  
2. Tracked isn't a literal type  
test_include\laws\base.hpp  
3. Missing constexpr on local variables  
test\numeric\main.hpp  
4. boost::tuples::tuple isn't a literal type  
  
Other failures are likely bugs in MSVC compiler, I'll log bugs to our bug database.

---

## Comment 6

> Username: ldionne  
> Created at: 2018-06-04 21:59:52 UTC  
> Url: https://github.com/boostorg/hana/issues/406#issuecomment-394514015  

I should be able to reproduce this using any version of Clang that implements C++17 (constexpr lambdas). I'll take a look at this as soon as I get the time. Once that is fixed, I can add a Travis job that enables `BOOST_HANA_CONFIG_HAS_CONSTEXPR_LAMBDA` to make sure this configuration option works.
