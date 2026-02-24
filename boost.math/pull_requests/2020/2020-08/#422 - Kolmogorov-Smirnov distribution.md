# #422 Kolmogorov-Smirnov distribution [Merged]

> Username: evanmiller  
> Created at: 2020-08-19 13:46:06 UTC  
> Updated at: 2020-09-26 19:21:48 UTC  
> Merged at: 2020-09-04 12:48:52 UTC  
> Closed at: 2020-09-04 12:48:52 UTC  
> Url: https://github.com/boostorg/math/pull/422  

Add a new distribution, `kolmogorov_smirnov_distribution`, which takes a parameter that represents the number of observations used in a Kolmogorov-Smirnov test. (The K-S test is a popular test for comparing two CDFs, but the test statistic is not implemented here.)  
  
This implementation includes Kolmogorov's original 1st order Taylor expansion, which itself is an infinite sum. There is a literature on the distribution's other mathematical properties (higher order terms and exact version); this literature is summarized in the main header file for anyone who may want to expand the implementation later.  
  
The CDF is implemented using a Jacobi theta function, and the PDF is a hand-rolled derivative of that function. Quantiles plug the CDF and PDF into a Newton-Raphson iteration. The mean, variance, skewness, and kurtosis have nice closed-form expressions, and the mode uses a dumb run-time maximizer.  
  
This commit includes graphs, a ULP plotter for the PDF, and the usual compilation and numerical tests. The test file is on the small side, but it verifies the first four moments by integrating the entire distribution, and also covers the quantiles pretty well. As of now the numerical tests only verify self-consistency (e.g. distribution moments and CDF-quantile relations), so there's room to add some external checks.  
  
I will add user-facing documentation after the API is approved and the implementation is finalized. (Hence WIP)  
  
See #421 for previous discussion and below for relevant graphs.  
  
<img width="754" alt="pdf" src="https://user-images.githubusercontent.com/134711/90641877-b9e62800-e1ff-11ea-9d79-bacf2f4e0e3e.png">  
<img width="790" alt="ulp" src="https://user-images.githubusercontent.com/134711/90641946-ca969e00-e1ff-11ea-9d46-a626f5ff7320.png">

---

## Review 1 [Commented]

> Username: NAThompson  
> Created_at: 2020-08-19 14:04:49 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/422#pullrequestreview-470491802  

📁 test/compile_test/dist_kolmogorov_smirnov_incl_test.cpp

```diff
   1 |+ //  Copyright John Maddock 2006.
```

> Username: NAThompson  
> Created_at: 2020-08-19 14:04:49 UTC  
> Updated_at: 2020-09-02 17:04:14 UTC  
> Url: https://github.com/boostorg/math/pull/422#discussion_r473055052  

Your name here.


---

## Review 2 [Commented]

> Username: NAThompson  
> Created_at: 2020-08-19 14:08:21 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/422#pullrequestreview-470495283  

📁 reporting/accuracy/plot_kolmogorov_smirnov_pdf.cpp

```diff
  18 |+     auto pdf_precise = [&, dist_precise](PreciseReal x) {
  19 |+         return boost::math::pdf(dist_precise, x);
  20 |+     };
```

> Username: NAThompson  
> Created_at: 2020-08-19 14:08:21 UTC  
> Updated_at: 2020-09-02 17:04:14 UTC  
> Url: https://github.com/boostorg/math/pull/422#discussion_r473057677  

Haven't read the implementation, but if both of these are using a first-order approximation, is this a very meaningful ULPs plot? The vision for an ULPs plot is that the precise function is for all intents and purposes "correct".

> Username: evanmiller  
> Created_at: 2020-08-19 14:19:48 UTC  
> Updated_at: 2020-09-02 17:04:14 UTC  
> Url: https://github.com/boostorg/math/pull/422#discussion_r473066152  

The "first order" is still an infinite sum with a convergence criterion, so I think the ULP plot will be useful for identifying aberrations.


---

## Comment 3

> Username: evanmiller  
> Created_at: 2020-08-19 16:48:39 UTC  
> Url: https://github.com/boostorg/math/pull/422#issuecomment-676540026  

The build failures indicate that `skewness` and `kurtosis` are required functions, but they are currently not implemented.

---

## Comment 4

> Username: evanmiller  
> Created_at: 2020-08-20 18:12:05 UTC  
> Url: https://github.com/boostorg/math/pull/422#issuecomment-677819349  

I've added a closed form expression for `skewness`, but I'm having trouble deriving / implementing kurtosis. It should yield a nice expression, but my numbers just aren't adding up when I try to implement it. A couple of questions to that end:  
  
1. Can kurtosis be optional?  
2. Is it okay if kurtosis is computed numerically at run-time?  
  
If not I'll take another stab at a closed form this weekend.

---

## Comment 5

> Username: evanmiller  
> Created_at: 2020-08-21 02:41:15 UTC  
> Url: https://github.com/boostorg/math/pull/422#issuecomment-678005988  

Found my mistake! Closed-form kurtosis is implemented now. The latest commit does not [CI SKIP] so I will check the build result tomorrow.

---

## Comment 6

> Username: evanmiller  
> Created_at: 2020-08-22 16:57:47 UTC  
> Url: https://github.com/boostorg/math/pull/422#issuecomment-678664848  

Can someone assist me with the multiprecision Travis errors? I am pulling my hair out trying to figure them out.  
  
```  
../../../boost/math/distributions/kolmogorov_smirnov.hpp:123:14: error: no matching function for call to ‘boost::multiprecision::detail::expression<boost::multiprecision::detail::subtract_immediates, int, boost::multiprecision::number<boost::multiprecision::backends::cpp_dec_float<50u> >, void, void>::expression()’  
1791     RealType k;  
1792              ^  
1793In file included from ../../../boost/multiprecision/traits/is_variable_precision.hpp:10:0,  
1794                 from ../../../boost/multiprecision/detail/precision.hpp:9,  
1795                 from ../../../boost/multiprecision/number.hpp:25,  
1796                 from ../../../boost/multiprecision/cpp_dec_float.hpp:29,  
1797                 from multiprc_concept_check_1.cpp:23:  
1798../../../boost/multiprecision/detail/number_base.hpp:897:29: note: candidate: constexpr boost::multiprecision::detail::expression<tag, Arg1, Arg2, void, void>::expression(const boost::multiprecision::detail::expression<tag, Arg1, Arg2, void, void>&) [with tag = boost::multiprecision::detail::subtract_immediates; Arg1 = int; Arg2 = boost::multiprecision::number<boost::multiprecision::backends::cpp_dec_float<50u> >]  
1799    BOOST_MP_CXX14_CONSTEXPR expression(const expression& e) : arg1(e.arg1), arg2(e.arg2) {}  
1800                             ^~~~~~~~~~  
1801../../../boost/multiprecision/detail/number_base.hpp:897:29: note:   candidate expects 1 argument, 0 provided  
1802../../../boost/multiprecision/detail/number_base.hpp:896:29: note: candidate: constexpr boost::multiprecision::detail::expression<tag, Arg1, Arg2, void, void>::expression(const Arg1&, const Arg2&) [with tag = boost::multiprecision::detail::subtract_immediates; Arg1 = int; Arg2 = boost::multiprecision::number<boost::multiprecision::backends::cpp_dec_float<50u> >]  
1803    BOOST_MP_CXX14_CONSTEXPR expression(const Arg1& a1, const Arg2& a2) : arg1(a1), arg2(a2) {}  
1804                             ^~~~~~~~~~  
1805../../../boost/multiprecision/detail/number_base.hpp:896:29: note:   candidate expects 2 arguments, 0 provided  
1806../../../boost/multiprecision/detail/number_base.hpp: In instantiation of ‘constexpr boost::multiprecision::detail::expression<tag, Arg1, Arg2, void, void>& boost::multiprecision::detail::expression<tag, Arg1, Arg2, void, void>::operator=(const Other&) [with Other = boost::multiprecision::detail::expression<boost::multiprecision::detail::minus, double, boost::multiprecision::detail::expression<boost::multiprecision::detail::multiplies, int, boost::multiprecision::detail::expression<boost::multiprecision::detail::minus, int, boost::multiprecision::detail::expression<boost::multiprecision::detail::subtract_immediates, int, boost::multiprecision::number<boost::multiprecision::backends::cpp_dec_float<50u> >, void, void>, void, void>, void, void>, void, void>; tag = boost::multiprecision::detail::subtract_immediates; Arg1 = int; Arg2 = boost::multiprecision::number<boost::multiprecision::backends::cpp_dec_float<50u> >]’:  
1807../../../boost/math/distributions/kolmogorov_smirnov.hpp:126:11:   required from ‘RealType boost::math::detail::kolmogorov_smirnov_quantile_guess(RealType) [with RealType = boost::multiprecision::detail::expression<boost::multiprecision::detail::subtract_immediates, int, boost::multiprecision::number<boost::multiprecision::backends::cpp_dec_float<50u> >, void, void>]’  
1808../../../boost/math/distributions/kolmogorov_smirnov.hpp:402:58:   required from ‘RealType boost::math::quantile(const boost::math::complemented2_type<boost::math::kolmogorov_smirnov_distribution<RealType, Policy>, RealType>&) [with RealType = boost::multiprecision::number<boost::multiprecision::backends::cpp_dec_float<50u> >; Policy = boost::math::policies::policy<boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy>]’  
1809../../../boost/math/concepts/distributions.hpp:138:19:   required from ‘void boost::math::concepts::DistributionConcept<Distribution>::constraints() [with Distribution = boost::math::kolmogorov_smirnov_distribution<boost::multiprecision::number<boost::multiprecision::backends::cpp_dec_float<50u> >, boost::math::policies::policy<boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy> >]’  
1810../../../boost/concept/detail/has_constraints.hpp:32:62:   required by substitution of ‘template<class Model> boost::concepts::detail::yes boost::concepts::detail::has_constraints_(Model*, boost::concepts::detail::wrap_constraints<Model, (& Model:: constraints)>*) [with Model = boost::math::concepts::DistributionConcept<boost::math::kolmogorov_smirnov_distribution<boost::multiprecision::number<boost::multiprecision::backends::cpp_dec_float<50u> >, boost::math::policies::policy<boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy> > >]’  
1811../../../boost/concept/detail/has_constraints.hpp:42:5:   required from ‘const bool boost::concepts::not_satisfied<boost::math::concepts::DistributionConcept<boost::math::kolmogorov_smirnov_distribution<boost::multiprecision::number<boost::multiprecision::backends::cpp_dec_float<50u> >, boost::math::policies::policy<boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy> > > >::value’  
1812../../../boost/concept/detail/has_constraints.hpp:45:51:   required from ‘struct boost::concepts::not_satisfied<boost::math::concepts::DistributionConcept<boost::math::kolmogorov_smirnov_distribution<boost::multiprecision::number<boost::multiprecision::backends::cpp_dec_float<50u> >, boost::math::policies::policy<boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy> > > >’  
1813../../../boost/concept/detail/general.hpp:51:8:   required from ‘struct boost::concepts::requirement_<void (*)(boost::math::concepts::DistributionConcept<boost::math::kolmogorov_smirnov_distribution<boost::multiprecision::number<boost::multiprecision::backends::cpp_dec_float<50u> >, boost::math::policies::policy<boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy> > >)>’  
1814../../../boost/concept_check.hpp:50:7:   required from ‘void boost::function_requires(Model*) [with Model = boost::math::concepts::DistributionConcept<boost::math::kolmogorov_smirnov_distribution<boost::multiprecision::number<boost::multiprecision::backends::cpp_dec_float<50u> >, boost::math::policies::policy<boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy> > >]’  
1815compile_test/instantiate.hpp:79:87:   required from ‘void instantiate(RealType) [with RealType = boost::multiprecision::number<boost::multiprecision::backends::cpp_dec_float<50u> >]’  
1816multiprc_concept_check_1.cpp:33:27:   required from here  
1817../../../boost/multiprecision/detail/number_base.hpp:908:7: error: static assertion failed: You can not assign to a Boost.Multiprecision expression template: did you inadvertantly store an expression template in a "auto" variable?  Or pass an expression to a template function with deduced temnplate arguments?  
1818       static_assert(sizeof(Other) == INT_MAX, "You can not assign to a Boost.Multiprecision expression template: did you inadvertantly store an expression template in a \"auto\" variable?  Or pass an expression to a template function with deduced temnplate arguments?");  
1819       ^~~~~~~~~~~~~  
```

---

## Comment 7

> Username: jzmaddock  
> Created_at: 2020-08-23 09:58:34 UTC  
> Url: https://github.com/boostorg/math/pull/422#issuecomment-678754151  

This is a template argument deduction issue, somewhere (if you follow the instantiation stack back up you will find where, but looks like kolmogorov_smirnov.hpp:402) you have:  
  
```  
detail::kolmogorov_smirnov_quantile_guess(1-p)  
```  
  
But `1-p` is an expression template for some multiprecision types, so template argument deduction for `kolmogorov_smirnov_quantile_guess` instantiates it with something that is not a number, but an expression.  Use:  
  
```  
detail::kolmogorov_smirnov_quantile_guess(RealType(1-p))  
```  
  
Or:  
  
```  
detail::kolmogorov_smirnov_quantile_guess<RealType>(1-p)  
```  
  
The two ultimately end up the same.

---

## Comment 8

> Username: evanmiller  
> Created_at: 2020-08-23 11:15:25 UTC  
> Url: https://github.com/boostorg/math/pull/422#issuecomment-678761251  

@jzmaddock Thanks, I'll try it out.

---

## Comment 9

> Username: evanmiller  
> Created_at: 2020-08-23 17:44:41 UTC  
> Url: https://github.com/boostorg/math/pull/422#issuecomment-678803426  

I'm now seeing some C++03-related errors originating in `exp_sinh.hpp`; are these a problem?  
  
```  
In file included from ../../../boost/math/quadrature/exp_sinh.hpp:21,  
                 from test_kolmogorov_smirnov.cpp:14:  
../../../boost/math/quadrature/detail/exp_sinh_detail.hpp:44:5: warning: ‘auto’ changes meaning in C++11; please remove it [-Wc++11-compat]  
     auto integrate(const F& f, Real* error, Real* L1, const char* function, Real tolerance, std::size_t* levels)->decltype(std::declval<F>()(std::declval<Real>())) const;  
     ^~~~  
     ----  
../../../boost/math/quadrature/detail/exp_sinh_detail.hpp:44:115: error: expected type-specifier before ‘decltype’  
     auto integrate(const F& f, Real* error, Real* L1, const char* function, Real tolerance, std::size_t* levels)->decltype(std::declval<F>()(std::declval<Real>())) const;  
                                                                                                                   ^~~~~~~~  
../../../boost/math/quadrature/detail/exp_sinh_detail.hpp:44:115: error: expected initializer before ‘decltype’  
../../../boost/math/quadrature/detail/exp_sinh_detail.hpp:126:41: error: ‘>>’ should be ‘> >’ within a nested template argument list  
     mutable std::vector<std::vector<Real>> m_abscissas;  
                                         ^~  
                                         > >  
../../../boost/math/quadrature/detail/exp_sinh_detail.hpp:127:41: error: ‘>>’ should be ‘> >’ within a nested template argument list  
     mutable std::vector<std::vector<Real>> m_weights;  
                                         ^~  
                                         > >  
```

---

## Comment 10

> Username: NAThompson  
> Created_at: 2020-08-23 18:03:27 UTC  
> Updated_at: 2020-08-23 18:03:52 UTC  
> Url: https://github.com/boostorg/math/pull/422#issuecomment-678805448  

@evanmiller : None of the quadrature stuff works in C++-03 mode; in addition C++03 support is being dropped so I wouldn't worry about supporting it in new code.

---

## Comment 11

> Username: evanmiller  
> Created_at: 2020-08-23 18:05:38 UTC  
> Url: https://github.com/boostorg/math/pull/422#issuecomment-678805678  

@NAThompson Understood. How should I deal with the failing Travis test then? (Quadrature is only used in the K-S test code, not in the implementation itself.)

---

## Comment 12

> Username: NAThompson  
> Created_at: 2020-08-23 18:14:28 UTC  
> Url: https://github.com/boostorg/math/pull/422#issuecomment-678806670  

@evanmiller : Change  
  
```  
 [ run test_kolmogorov_smirnov.cpp ../../test/build//boost_unit_test_framework ]  
```  
  
to  
  
```  
 [ run test_kolmogorov_smirnov.cpp ../../test/build//boost_unit_test_framework [ requires cxx11_hdr_initializer_list cxx11_auto_declarations cxx11_lambdas cxx11_unified_initialization_syntax cxx11_smart_ptr ] ]  
```  
  
(Check that syntax before kicking off a build!)

---

## Comment 13

> Username: evanmiller  
> Created_at: 2020-08-23 18:18:02 UTC  
> Url: https://github.com/boostorg/math/pull/422#issuecomment-678806994  

@NAThompson I think it needs a few colons but I will try it out.

---

## Comment 14

> Username: evanmiller  
> Created_at: 2020-08-25 10:57:48 UTC  
> Url: https://github.com/boostorg/math/pull/422#issuecomment-679954657  

The tests are now a sea of green (modulo a couple of apt-get errors on Travis and pre-existing failures on Appveyor).

---

## Comment 15

> Username: NAThompson  
> Created_at: 2020-08-25 12:10:44 UTC  
> Url: https://github.com/boostorg/math/pull/422#issuecomment-679985879  

> (modulo a couple of apt-get errors on Travis and pre-existing failures on Appveyor)  
  
Oh it's a scourge isn't it? I'll try to take a look soon.

---

## Comment 16

> Username: pabristow  
> Created_at: 2020-08-28 15:48:16 UTC  
> Url: https://github.com/boostorg/math/pull/422#issuecomment-682746803  

Much of Boost.Math has working examples in the /example folder.  I believe that they are very valuable to readers, many of whom are novice programmers but experts in other fields.  There are many complexities in Boost.Math that allow use different floating-point types etc that have caused these users trouble.   The /test folder provides usage examples, but are often obfuscated by the use of Boost.Test, and are deliberately obscure to test corner and edge cases.  What may seem obvious and trivially simple is often puzzling to newcomers.    
  
Real-life data is often available and should make the example more realistic.  
  
So I feel it would be very useful to try to continue to provide simple working examples of functions, distributions etc, heavily commented and with typical output, and having a link from the docs (and perhaps also providing code snippets in the docs).    
  
IMO, the K-S distribution (and the similar Anderson-Darling) would benefit from this type of example.

---

## Comment 17

> Username: pabristow  
> Created_at: 2020-08-28 15:52:21 UTC  
> Url: https://github.com/boostorg/math/pull/422#issuecomment-682757421  

I feel it is time to get the K-S prototype into its own branch off develop/.  
  
It would probably save @evanmiller a lot of time if I take his documentation comments in code, references etc and convert them to Quickbook format as a prototype that can then be refined and expanded.  But to do this I would like to work on a ks branch.

---

## Comment 18

> Username: NAThompson  
> Created_at: 2020-08-28 17:04:16 UTC  
> Url: https://github.com/boostorg/math/pull/422#issuecomment-682913841  

@pabristow   
  
> But to do this I would like to work on a ks branch.  
  
You can do this with [github cli](https://cli.github.com/); do:  
  
```  
$ gh pr checkout 422  
```

---

## Comment 19

> Username: evanmiller  
> Created_at: 2020-08-28 17:05:09 UTC  
> Url: https://github.com/boostorg/math/pull/422#issuecomment-682916549  

@pabristow I agree that fleshed-out examples are very useful to practitioners. In the case of K-S, it would probably also be useful to provide a function for computing the test statistic *D*. This requires a vector of data and a theoretical distribution to compare the data against. (It's relatively easy to calculate - just the supremum of the CDF differences.) Then the example could walk through constructing a theoretical distribution, computing the test statistic, and generating a critical value and p-value for the null hypothesis. Doing that for Anderson-Darling would be useful as well.  
  
Just so you know where I'm coming from, my interest in Boost.Math is on the software development end - i.e. providing functions and documentation useful to professional statistical application developers rather than novice end users. So while I do see value in extended examples, they will be less of a priority to me compared to the basic documentation and meeting Boost's other minimum requirements for inclusion.  
  
Barring a special branch here you're welcome to send mini-PRs to the branch on my account. I did the Quickbook docs for my Jacobi Theta work and can do the same here - was just waiting for a general OK on the API first.

---

## Comment 20

> Username: pabristow  
> Created_at: 2020-08-30 16:04:49 UTC  
> Url: https://github.com/boostorg/math/pull/422#issuecomment-683438276  

One of the curious features of writing for Boost is that you have little idea who your 'customers' are.  The only clues come from when they find bugs, often surprisingly obscure, or when they ask questions on the Boost lists, often quite elementary.  Providing both examples and links to the example code has reduced the number of cries for help.  
  
Another clue to users is provided indirectly by Google.  I'm pleased by seeing how high Boost.Math often comes in the Google suggested links, and interpret this to mean that other people have clicked on these links  (and arrogantly assumed that they liked them too ;-) ).  
  
But that isn't to say that 'professional' statisticians are not important customers, especially as a USP of Boost.Math is that it meshes well with Boost.Multiprecision allowing much higher precision when this is vital.  (Though I do wonder if pro statisticians don't prefer to use less picky languages like R and Python).  An important attraction of using Boost.Math is that one can add to an existing program without writing data out and reading it into another statistics tool.  
  
So I'm sure we agree that there is a place for both novice and expert examples.  If I can help providing novice examples, please tell me.  (In the past this has also smoked out some buglets in the process).

---

## Comment 21

> Username: evanmiller  
> Created_at: 2020-08-30 17:06:36 UTC  
> Url: https://github.com/boostorg/math/pull/422#issuecomment-683445110  

Thanks for the offer @pabristow. I think after the main documentation is done we can think about useful examples and workflows.  
  
Does anyone object to the current API? If not I will take a crack at a Quickbook.

---

## Comment 22

> Username: NAThompson  
> Created_at: 2020-08-30 17:12:04 UTC  
> Url: https://github.com/boostorg/math/pull/422#issuecomment-683445718  

@evanmiller : I just read through your unit tests and think the API looks good.  
  
Also, you might want to rebase on master to get a clean build.

---

## Comment 23

> Username: evanmiller  
> Created_at: 2020-08-30 22:29:34 UTC  
> Url: https://github.com/boostorg/math/pull/422#issuecomment-683478625  

Okay, I've added some user documentation, let me know if it's intelligible! It's not nearly as comprehensive as the code comments, but I don't think it's really worth enumerating all of the ways the function is *not* implemented (which is what the comments do).  
  
@pabristow It might be a useful exercise to see if the docs give you enough rope to write an example program. If not then it means there are some holes.  
  
I do think a complete tutorial will necessitate additional functions provided by Boost e.g. computing the K-S sample statistic, which isn't out-of-this-world difficult, but which is not totally trivial. I'm not sure what the right API for that function would be – and it's a bit further afield than what I'm trying to achieve right now.

---

## Comment 24

> Username: evanmiller  
> Created_at: 2020-08-31 00:03:36 UTC  
> Url: https://github.com/boostorg/math/pull/422#issuecomment-683489275  

@NAThompson I just rebased but I'm not sure what you meant exactly by "clean build". Were you referring to CI?

---

## Comment 25

> Username: NAThompson  
> Created_at: 2020-08-31 01:47:19 UTC  
> Url: https://github.com/boostorg/math/pull/422#issuecomment-683509655  

Yeah, the CI was broken a couple days ago . . .

---

## Comment 26

> Username: evanmiller  
> Created_at: 2020-08-31 12:14:47 UTC  
> Url: https://github.com/boostorg/math/pull/422#issuecomment-683741768  

`distribution_tests` is now passing on all CI platforms. From my perspective this is now a complete submission, so I am ceremoniously removing the WIP label. Happy of course to field additional suggestions etc.

---

## Comment 27

> Username: pabristow  
> Created_at: 2020-09-02 08:05:32 UTC  
> Url: https://github.com/boostorg/math/pull/422#issuecomment-685429078  

@evanmiller I'm on the job (carefully including RTFM - nice) ;-)

---

## Review 28 [Commented]

> Username: NAThompson  
> Created_at: 2020-09-02 13:48:26 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/422#pullrequestreview-480837714  

📁 doc/distributions/kolmogorov_smirnov.qbk

```diff
  55 |+ 
  56 |+ Note that while the exact distribution only has support over \[0, 1\], this
  57 |+ limiting form has positive mass above unity, particlularly for small /n/. The
```

> Username: NAThompson  
> Created_at: 2020-09-02 13:48:26 UTC  
> Updated_at: 2020-09-02 17:04:14 UTC  
> Url: https://github.com/boostorg/math/pull/422#discussion_r482082770  

Typo: particlularly

> Username: evanmiller  
> Created_at: 2020-09-02 14:49:05 UTC  
> Updated_at: 2020-09-02 17:04:14 UTC  
> Url: https://github.com/boostorg/math/pull/422#discussion_r482129387  

Fixed


---

## Comment 29

> Username: NAThompson  
> Created_at: 2020-09-02 14:10:54 UTC  
> Url: https://github.com/boostorg/math/pull/422#issuecomment-685763079  

@evanmiller : When I try to run the tests I get:  
  
```  
make test/test_kolmogorov_smirnov.x  
g++-10 -g -Wall -Wextra --std=gnu++17 -fno-finite-math-only -march=native -Wfatal-errors -MMD -O2 -I./include_private -I../../ -I/usr/local/include -o test/test_kolmogorov_smirnov.x test/test_kolmogorov_smirnov.cpp -L/usr/local/lib -pthread -lbenchmark -lbenchmark_main -lmpfr -lquadmath -lprimesieve -lboost_unit_test_framework -lboost_regex  
test/test_kolmogorov_smirnov.cpp:7:10: fatal error: pch_light.hpp: No such file or directory  
    7 | #include <pch_light.hpp>  
      |          ^~~~~~~~~~~~~~~  
compilation terminated.  
make: *** [test/test_kolmogorov_smirnov.x] Error 1  
```  
  
Needs to be in quotes to look in the same directory. Also:  
  
```  
test/test_kolmogorov_smirnov.cpp: In instantiation of 'void test_spots(RealType) [with RealType = float]':  
test/test_kolmogorov_smirnov.cpp:94:21:   required from here  
test/test_kolmogorov_smirnov.cpp:17:26: warning: unused parameter 'T' [-Wunused-parameter]  
   17 | void test_spots(RealType T)  
      |                 ~~~~~~~~~^  
```  
  
I think you're following a pattern that was designed to work around compiler template bugs; no need to do it now.  
  
I have built the docs locally and read them; all figures showed up and look good.

---

## Review 30 [Commented]

> Username: NAThompson  
> Created_at: 2020-09-02 14:12:02 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/422#pullrequestreview-480861523  

📁 include/boost/math/distributions/kolmogorov_smirnov.hpp

```diff
  91 |+ // polynomial or rational approximation, since the only meaningful argument is
  92 |+ // x * sqrt(n). But that is left as an exercise for the next maintainer.
  93 |+ //
```

> Username: NAThompson  
> Created_at: 2020-09-02 14:12:02 UTC  
> Updated_at: 2020-09-02 17:04:14 UTC  
> Url: https://github.com/boostorg/math/pull/422#discussion_r482100793  

I love these header line docs; really useful for more detail than is appropriate for end-user docs.


---

## Review 31 [Commented]

> Username: NAThompson  
> Created_at: 2020-09-02 14:13:05 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/422#pullrequestreview-480862482  

📁 include/boost/math/distributions/kolmogorov_smirnov.hpp

```diff
 104 |+ //
 105 |+ #ifndef BOOST_STATS_KOLMOGOROV_ASYMPTOTIC_HPP
 106 |+ #define BOOST_STATS_KOLMOGOROV_ASYMPTOTIC_HPP
```

> Username: NAThompson  
> Created_at: 2020-09-02 14:13:05 UTC  
> Updated_at: 2020-09-02 17:04:14 UTC  
> Url: https://github.com/boostorg/math/pull/422#discussion_r482101526  

I think this follows a pattern which was inherited from some very old code. I'd use `#ifndef BOOST_MATH_DISTRIBUTIONS_KOLMOGOROV_SMIRNOV_HPP` to match our current practice.

> Username: evanmiller  
> Created_at: 2020-09-02 14:48:51 UTC  
> Updated_at: 2020-09-02 17:04:14 UTC  
> Url: https://github.com/boostorg/math/pull/422#discussion_r482129200  

Got it, done


---

## Review 32 [Commented]

> Username: NAThompson  
> Created_at: 2020-09-02 14:13:11 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/422#pullrequestreview-480862585  

📁 include/boost/math/distributions/kolmogorov_smirnov.hpp

```diff
 114 |+ #include <boost/math/tools/minima.hpp> // For the mode
 115 |+ 
 116 |+ // #include <utility>
```

> Username: NAThompson  
> Created_at: 2020-09-02 14:13:11 UTC  
> Updated_at: 2020-09-02 17:04:14 UTC  
> Url: https://github.com/boostorg/math/pull/422#discussion_r482101612  

Remove?

> Username: evanmiller  
> Created_at: 2020-09-02 14:48:38 UTC  
> Updated_at: 2020-09-02 17:04:14 UTC  
> Url: https://github.com/boostorg/math/pull/422#discussion_r482129049  

Done


---

## Review 33 [Commented]

> Username: NAThompson  
> Created_at: 2020-09-02 14:15:13 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/422#pullrequestreview-480864583  

📁 include/boost/math/distributions/kolmogorov_smirnov.hpp

```diff
 126 |+     if (p < 0.3)
 127 |+         return p + RealType(0.45);
 128 |+     return p + RealType(0.3);
```

> Username: NAThompson  
> Created_at: 2020-09-02 14:15:13 UTC  
> Updated_at: 2020-09-02 17:04:14 UTC  
> Url: https://github.com/boostorg/math/pull/422#discussion_r482103106  

I suppose it's low priority, but casting a double precision number like 0.3 to `RealType` is a little inelegant since it's not representable. Not really a big deal, but I'd use `RealType(3)/RealType(10)`.

> Username: evanmiller  
> Created_at: 2020-09-02 14:29:11 UTC  
> Updated_at: 2020-09-02 17:04:14 UTC  
> Url: https://github.com/boostorg/math/pull/422#discussion_r482113941  

Well, the whole function is inelegant – it's basically me drawing three straight lines by hand to fit the curve for an initial guess. So I'm not too worried about epsilon-sized round-off errors.


---

## Review 34 [Commented]

> Username: NAThompson  
> Created_at: 2020-09-02 14:17:17 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/422#pullrequestreview-480866637  

📁 include/boost/math/distributions/kolmogorov_smirnov.hpp

```diff
 209 |+     private:
 210 |+ 
 211 |+     RealType n_obs; // positive integer
```

> Username: NAThompson  
> Created_at: 2020-09-02 14:17:17 UTC  
> Updated_at: 2020-09-02 17:04:14 UTC  
> Url: https://github.com/boostorg/math/pull/422#discussion_r482104678  

We generally use either a suffixed underscore or `m_` prefix for private member variables; I'm do `n_obs_` here.


---

## Comment 35

> Username: evanmiller  
> Created_at: 2020-09-02 14:24:47 UTC  
> Url: https://github.com/boostorg/math/pull/422#issuecomment-685771601  

> Needs to be in quotes to look in the same directory  
  
Happy to change it, but note that many/most of the tests in that folder include `<pch_light.hpp>` rather than `"pch_light.hpp"`

---

## Comment 36

> Username: pabristow  
> Created_at: 2020-09-02 16:42:37 UTC  
> Updated_at: 2020-09-02 16:48:19 UTC  
> Url: https://github.com/boostorg/math/pull/422#issuecomment-685858979  

Passes K-S test on msvc 14.2  using Visual Studio IDE OK with minor moaning about  
I:\boost\libs\math\test\test_kolmogorov_smirnov.cpp(24,56): warning C4244: 'argument': conversion from 'int' to 'RealType', possible loss of data  
1>        with  
1>        [  
1>            RealType=float  
1>        ]  
1>I:\boost\libs\math\test\test_kolmogorov_smirnov.cpp(94): message : see reference to function template instantiation 'void test_spots<float>(RealType)' being compiled  
1>        with  
1>        [  
1>            RealType=float  
1>        ]  
1>I:\boost\libs\math\test\test_kolmogorov_smirnov.cpp(26,24): warning C4244: 'initializing': conversion from 'double' to 'RealType', possible loss of data  
1>        with  
1>        [  
1>            RealType=float  
1>        ]  
1>I:\boost\libs\math\test\test_kolmogorov_smirnov.cpp(35,24): warning C4244: 'initializing': conversion from 'double' to 'RealType', possible loss of data  
1>        with  
1>        [  
1>            RealType=float  
1>        ]  
  
test_kolmogorov_smirnov.cpp(17): warning C4100: 'T': unreferenced formal parameter  
  
looks spurious.  
  
(The curious passing of zero type dates from 2005 when compilers templates didn't yet work properly).  
  
and needed to add a include to math/test where the annoying pch_light.hpp lives to find it from #include <pch_light>.  
  
(Aside I note that you are not yet up-to-date with Boost 1.75, so I had to build library for unit_test_framework for 1.74.  But updating is always tiresome.)  
  
I also added a multiprecision test (this usually smokes out any potential conversion problems)  and this mostly passed but appears to fail these two tests  
  
  BOOST_TEST_CHECK(pdf(dist, mode) >= pdf(dist, mode - 100 * tol));  
  BOOST_TEST_CHECK(pdf(dist, mode) >= pdf(dist, mode + 100 * tol));  
    
However the difference is only a bit to two, so test is just too tight.  
  
[16.8683936202546321115046170516878736 <  
 16.8683936202546321115046170516878798]  
  
We haven't run multiprecision math tests routinely as they take too long (~30 sec for me), but it is nice to see that it works OK.  
  
1>I:/Cpp/math/test_kolmogorov_smirnov/test_kolmogorov_smirnov_mp/test_kolmogorov_smirnov_mp.cpp(61): error : in "test_main": check pdf(dist, mode) >= pdf(dist, mode - 100 * tol) has failed [16.8683936202546321115046170516878736 < 16.8683936202546321115046170516878798]  
1>I:/Cpp/math/test_kolmogorov_smirnov/test_kolmogorov_smirnov_mp/test_kolmogorov_smirnov_mp.cpp(62): error : in "test_main": check pdf(dist, mode) >= pdf(dist, mode + 100 * tol) has failed [16.8683936202546321115046170516878736 < 16.8683936202546321115046170516878767]  
  
Using the B2 jamfile, I get failure to link and run the test and will investigate tomorrow.  
  
WIP but looking good.  Will start on an example of what I am told is called the 'Vodka Test' by some novice statisticians.

---

## Comment 37

> Username: evanmiller  
> Created_at: 2020-09-02 17:00:45 UTC  
> Url: https://github.com/boostorg/math/pull/422#issuecomment-685869154  

Thanks for the comments @pabristow. There's some overlap with @NAThompson's feedback, so some of the minor issues have already been addressed.  
  
I plan to change `100*tol` to `sqrt(eps)` – the purpose is just to move the `x` needle enough to demonstrate we're at some kind of maximum. This would be more reliable with an analytic expression for the mode, but I haven't come across one.  
  
I'll take a look into updating to Boost 1.75, and will plan to kick off a CI build later today to confirm the latest round of fixes hasn't broken anything.

---

## Comment 38

> Username: evanmiller  
> Created_at: 2020-09-02 17:30:05 UTC  
> Url: https://github.com/boostorg/math/pull/422#issuecomment-685886826  

> (Aside I note that you are not yet up-to-date with Boost 1.75, so I had to build library for unit_test_framework for 1.74. But updating is always tiresome.)  
  
Can someone clarify this comment? I'm not sure what's out of date exactly or how I update my repos to the mythical Boost 1.75.

---

## Comment 39

> Username: jzmaddock  
> Created_at: 2020-09-02 17:44:53 UTC  
> Url: https://github.com/boostorg/math/pull/422#issuecomment-685894649  

>Happy to change it, but note that many/most of the tests in that folder include <pch_light.hpp> rather than "pch_light.hpp"  
  
If it's built from libs/multiprecision/test/Jamfile.v2 then libs/multiprecision/test will be in the include path automatically.

---

## Comment 40

> Username: evanmiller  
> Created_at: 2020-09-02 19:17:53 UTC  
> Url: https://github.com/boostorg/math/pull/422#issuecomment-685944306  

> If it's built from libs/multiprecision/test/Jamfile.v2 then libs/multiprecision/test will be in the include path automatically.  
  
@NAThompson Are you running the test via `../../../b2 test_kolmogorov_smirnov`?

---

## Comment 41

> Username: NAThompson  
> Created_at: 2020-09-02 22:54:02 UTC  
> Url: https://github.com/boostorg/math/pull/422#issuecomment-686069711  

@evanmiller : I don't use `b2`; I just have a Makefile that I use locally. Don't worry about fixing that one.

---

## Comment 42

> Username: pabristow  
> Created_at: 2020-09-03 08:49:08 UTC  
> Url: https://github.com/boostorg/math/pull/422#issuecomment-686349098  

I have git pulled the boost /develop branch to get all the libraries fully up-to-date. I think you will have been testing against this using Travis.  I don't think this will cause trouble.  
  
FWIW I use  
  
cd i:/boost  
  
git checkout develop  
git pull --recurse-submodules  <<<<< git pulls all submodules in turn.  
git submodule update --init   <<<<<<<<<<  This gets any new libraries (unusual)

---

## Comment 43

> Username: pabristow  
> Created_at: 2020-09-04 11:02:12 UTC  
> Url: https://github.com/boostorg/math/pull/422#issuecomment-687077091  

I have now run the tests OK on Windows with mingw64 using Visual studio IDE and MSVC latest preview, and also using Codeblocks IDE with Clang 10.0.0 and GCC 10.0.1, and with b2 using the jamfile (except that Clang doesn't work quite right failing to link with unit_test_framework, a bug in Clang jamfiles that I have yet to fix).  
  
So all looking good so far :-)  
  
You might like to follow @jzmaddock view in https://github.com/boostorg/math/issues/431 in the tests_spots.  But that's a cosmetic issue.  
  
A working example is WIP.

---

## Comment 44

> Username: NAThompson  
> Created_at: 2020-09-04 14:30:22 UTC  
> Url: https://github.com/boostorg/math/pull/422#issuecomment-687181293  

@pabristow : I merged this because I thought it looked good; could you open up a new PR for the example?  
  
@evanmiller : Nice work. I guess the next phase is extracting a p-value from the test statistic? This was something I think I got bogged down with on the Anderson-Darling test, IIRC.

---

## Comment 45

> Username: evanmiller  
> Created_at: 2020-09-04 14:32:10 UTC  
> Url: https://github.com/boostorg/math/pull/422#issuecomment-687182336  

Thanks for the report @pabristow – it looks like we're merged in, so `test_spots` is out of my hands now.  
  
Thanks to everyone for the feedback and assistance on this and #394! My "journey" here began with a desire to replace about 50 lines of code of wobbly Kolmogorov-Smirnov code with something heavily tested and peer-reviewed. Ten weeks, two pull requests, 1000 LoC and several pages of documentation later I can replace those 50 lines with a few call to Boost :-).  
  
The expertise here is an invaluable resource. While I'm here, I will note a few "pain points" as a new contributor:  
  
* Automated tests on the CI platforms take too long. It would be nice to indicate a specific test group rather than CI SKIP the whole thing for minor changes. Another possibility would be skipping intermediate compiler versions (e.g instead of MSVC 9-14 just do 9, 12, and 14).  
  
* Coming from a double-only world (and a C99 world at that), getting everything to work with every possible precision and `real_concept` was more work than I would have liked. Some of this was my unfamiliarity with C++ casting and templating rules. I guess in some ways this is "the pain and the glory" of Boost::Math.  
  
* In a couple of instances, the line between "We'd like you to do this" and "We need you to do this" was hazy. It would have been nice to have a clear checklist about what is required and what is optional vis a vis new special functions and new distributions so I could prioritize my time.  
  
* Function plotting is a little bit broken. IIRC I had to go chase down a couple of unofficial Boost packages to get it working and then the plot's legend was hit or miss. I'm referring to `dist_graphs.cpp`.  
  
* Building the docs takes too long to see how a single page changed.  
  
* There's a fair amount of cruft in the non-`include` folders. Weird old scripts, obsolete PNG files, etc.  
  
And some unexpected enjoyments:  
  
* The ULP plots are really cool :-)  
  
* Quickbooks was fairly easy to write.  
  
* The long list of available constants came in handy more than once  
  
* The quadrature package is awesome for testing functions over their entire domain  
  
* Whatever dark magic is used to recover original expressions in the CHECK_CLOSE tests is neat  
  
* Very fast response times from the maintainers  
  
I think that about covers it. I'll be available for questions about the contributed code and docs, but otherwise I will be checking out for a while to focus on other tasks. I do have another 50 lines of gerry-rigged numerical code that I might try to replace with a Boost contribution – but that will have to wait until another day!

---

## Comment 46

> Username: evanmiller  
> Created_at: 2020-09-04 14:34:14 UTC  
> Url: https://github.com/boostorg/math/pull/422#issuecomment-687183694  

> @evanmiller : Nice work. I guess the next phase is extracting a p-value from the test statistic? This was something I think I got bogged down with on the Anderson-Darling test, IIRC.  
  
I'm sure @pabristow is on the case but the p-value will just be the upper tail of the distribution. I.e.  
  
```  
boost::math::cdf(boost::math::complement(dist, d));  
```

---

## Comment 47

> Username: NAThompson  
> Created_at: 2020-09-04 14:48:22 UTC  
> Url: https://github.com/boostorg/math/pull/422#issuecomment-687193982  

> the p-value will just be the upper tail of the distribution  
  
I think we're talking about two different things. The test statistic needs to computed from data correct? Your expression doesn't have an empirical cumulative distribution function.

---

## Comment 48

> Username: evanmiller  
> Created_at: 2020-09-04 14:54:01 UTC  
> Url: https://github.com/boostorg/math/pull/422#issuecomment-687197919  

> I think we're talking about two different things. The test statistic needs to computed from data correct? Your expression doesn't have an empirical cumulative distribution function.  
  
Yes - the test statistic is computed from the data (or empirical CDF) and from the theoretical distribution being compared.

---

## Comment 49

> Username: NAThompson  
> Created_at: 2020-09-04 14:59:42 UTC  
> Updated_at: 2020-09-04 15:00:52 UTC  
> Url: https://github.com/boostorg/math/pull/422#issuecomment-687202423  

> Function plotting is a little bit broken. IIRC I had to go chase down a couple of unofficial Boost packages to get it working and then the plot's legend was hit or miss. I'm referring to dist_graphs.cpp.  
  
I agree; I have branch locally that has a plotting function that has roughly the same interface as the ULPs plots. Sadly that has been sitting on my machine for quite some time.  
  
The issue is that I have no competitive advantage with these plots. With the ULPs plots I had a unique insight (that the condition number could form an envelope indicating if the function was capably implemented), but spitting out an svg with linear path info is kinda bush-league. It's annoying to me that there's no interpolating spline for svg, the Catmull-Rom curve would work wonders. The approximating Bezier splines supported by svg are ridiculous. I'd have to back out the control points from interpolating points, which is a mathematics I have no interest in, since the Catmull-Rom curve made that superfluous.

---

## Comment 50

> Username: pabristow  
> Created_at: 2020-09-07 14:25:46 UTC  
> Url: https://github.com/boostorg/math/pull/422#issuecomment-688359620  

@evanmiller Thanks for all your valuable work of the KS distribution.   
It wasn't on my TODO list, but no doubt some will find it useful (though the Anderson-Darling seems popular too).  
  
Hope you had some fun, and found some of it educational, and sorry for the un-fun bit - the broken (by me) svg_plot (I will fix it).  
(I was unbroking it when I was hit by a dodgy AMD CPU chip leading the nine months of hell until it finally broke properly (and I got a replacement).    
  
Before you depart this (Boost) life completely, I'd be interested to know your views on the two more recent papers, and compare them with the method you used.  Although n=100s is useful, there will be many wanting more.  Did you explore this region?  
  
https://arxiv.org/pdf/1802.06966.pdf Paul van Mulbregt  
Computing the Cumulative Distribution Function and Quantiles of the One-sided Kolmogorov-Smirnov Statistic  
  
also looks more promising by rewriting equations to avoid cancellation error.  
  
Python package SciPy (v0.19.1) [1] provides the scipy.stats.ksone  
  
https://core.ac.uk/download/pdf/25787785.pdf  
  
Evaluating Kolmogorov’s Distribution  
George Marsaglia   The Florida State University  
Wai Wan Tsang, Jingbo Wang    The University of Hong Kong

---

## Comment 51

> Username: evanmiller  
> Created_at: 2020-09-07 15:11:37 UTC  
> Url: https://github.com/boostorg/math/pull/422#issuecomment-688384694  

@pabristow No regrets at all – and I was glad to scrape some of the rust off of my C++ skills.  
  
Regarding the papers you linked: The first paper deals only with a one-sided statistic, which is a different distribution from the two-sided original. The paper claims it is easier to implement – but it is probably less useful in practice than a two-sided statistic.  
  
The MTW paper is included in the overview article that I linked in 421 which I think you're referring to:  
  
https://www.jstatsoft.org/article/view/v039i11  
  
Carvalho found an improvement on MTW but I suspect it can be improved more. I had a brief email correspondence with Carvalho about it, but he has moved on to other research areas.  
  
Basically: MTW is an implementation of the Durbin fomula, which requires raising a KxK matrix to the Nth power and taking the bottom-right cell of the result. MTW performs the Nth power computation with divide-and-conquer (log(N) matrix multiplications). Carvalho improves it by removing some of the multiplications that will ultimately be discarded.  
  
I suspect MTW can be improved further via eigendecomposition, the old trick where you compute the eigenvalues, put them in a diagonal matrix, and then raise the entries of the diagonal to the Nth power to get a matrix power. Durbin's matrix is nearly lower-triangular – so I further suspect it will be possible to come up with a general expression for the Kth order characteristic polynomial of the Durbin matrix via Gaussian elimination (i.e. by hand), and then compute eigenvalues for specific cases with a root finder (i.e. without LAPACK).  
  
For the yucks I tried deriving that characteristic polynomial and I made a little bit of progress but I got distracted with some other things. If anybody stumbles across this and wants to see my notes I'm happy to share.  
  
Probably a better place to start would be the FFT solution that I linked in the other thread which I'll link again here:  
  
https://openaccess.city.ac.uk/id/eprint/18541/8/Dimitrova%2C%20Kaishev%2C%20Tan%20%282017%29%20KSr1.pdf  
  
I haven't delved into that paper, so am unable to comment on the computational complexity (or numerical stability) vis a vis MTW, Carvalho, or my proposed eigendecomposition.  
  
Besides N<100 another possible direction for K-S would be a K-sample version, which for posterity I will note can be found in this paper:  
  
https://projecteuclid.org/euclid.aoms/1177706261  
  
Boost has all the building blocks for that, including Bessel zeroes, but I will not admit whether or not I have a working double-precision implementation.

---

## Review 52 [Commented]

> Username: jeremy-murphy  
> Created_at: 2020-09-07 23:06:32 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/422#pullrequestreview-483723216  

📁 include/boost/math/distributions/kolmogorov_smirnov.hpp

```diff
 137 |+     int i = 0;
 138 |+     RealType pi2 = constants::pi_sqr<RealType>();
 139 |+     RealType x2n = x*x*n;
```

> Username: jeremy-murphy  
> Created_at: 2020-09-07 23:06:32 UTC  
> Url: https://github.com/boostorg/math/pull/422#discussion_r484582576  

A very minor thing, but I would slap a `const` on these constants to make it easier for the reader and, possibly, the optimizing compiler.

> Username: jeremy-murphy  
> Created_at: 2020-09-07 23:14:59 UTC  
> Updated_at: 2020-09-07 23:15:00 UTC  
> Url: https://github.com/boostorg/math/pull/422#discussion_r484583655  

Oh, this is already merged, never mind.  :)


---

## Comment 53

> Username: jzmaddock  
> Created_at: 2020-09-26 17:47:51 UTC  
> Url: https://github.com/boostorg/math/pull/422#issuecomment-699526692  

This is causing testing failures in develop, see https://github.com/boostorg/math/pull/437.  Can someone please take a look?  Thanks!

---

## Comment 54

> Username: evanmiller  
> Created_at: 2020-09-26 19:21:48 UTC  
> Url: https://github.com/boostorg/math/pull/422#issuecomment-699537382  

@jzmaddock I think the sqrt's here:  
  
https://github.com/boostorg/math/blob/2dec5f6af5e35184d8055b7bdd1df0df12fd3d34/test/test_kolmogorov_smirnov.cpp#L59-L60  
  
Are promoting the 2nd argument to a double, so std::sqrt should fix it.

---
