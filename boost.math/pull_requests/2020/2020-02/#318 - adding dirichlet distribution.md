# #318 adding dirichlet distribution [Open]

> Username: mrityunjay-tripathi  
> Created at: 2020-02-27 18:53:52 UTC  
> Updated at: 2022-10-29 17:13:14 UTC  
> Url: https://github.com/boostorg/math/pull/318  

I am adding Dirichlet distribution in ```include/boost/math/distributions/```. The Dirichlet distribution is a multivariate distribution function, so vector type inputs are needed, but the tests in ```test/compile_test/test_compile_result.hpp``` are for ```int```, ```double```, ```float```, ```long double```, ```unsigned```. How can I add tests for Dirichlet distribution?  
TODO:  
- [ ] Add tests.  
- [ ] Add google benchmark.  
- [x] Decision on ```VectorType``` and it's implementation.  
- [x] Cross check ```cdf``` function. (I could not find it, so tried to derive it.)  
- [x] Implementation of ```skewness``` function  
- [x] Implementation of ```kurtosis``` function.  
  
Also, none of the distribution functions generate random samples of the corresponding distribution. That means no sampling function implemented. In PyTorch, we can do something like,  
```  
>>> import torch  
>>> import torch.distributions as dist  
>>> d = dist.dirichlet.Dirichlet(torch.tensor([0.3, 0.3, 0.3]))  
>>> d.sample()  
tensor([0.7736, 0.1908, 0.0356])  
>>> d.rsample(torch.tensor([5]))  
tensor([[2.3072e-02, 9.4317e-01, 3.3756e-02],  
        [1.8339e-01, 1.6803e-03, 8.1493e-01],  
        [7.3136e-01, 1.9131e-01, 7.7323e-02],  
        [2.1086e-01, 9.5684e-03, 7.7958e-01],  
        [9.6672e-06, 4.6919e-02, 9.5307e-01]])  
>>>   
```  
Can we add such feature or its implemented as such?

---

## Comment 1

> Username: mrityunjay-tripathi  
> Created_at: 2020-02-28 05:45:43 UTC  
> Url: https://github.com/boostorg/math/pull/318#issuecomment-592324965  

> Also, none of the distribution functions generate random samples of the corresponding distribution. That means no sampling function implemented. In PyTorch, we can do something like,  
  
Ok, got it. It is implemented in ```boostorg/random``` repository.

---

## Comment 2

> Username: pabristow  
> Created_at: 2020-02-28 10:38:09 UTC  
> Url: https://github.com/boostorg/math/pull/318#issuecomment-592454927  

Looks a really promising start.  
  
It would be really nice if the Real defaults could be vector<double> and all other parameters double.  
This would make it appear to work quite like C, Python, R etc, out of the box.  
  
For testing, \boost\libs\math\test\test_beta_dist.cpp is a slightly useful template.  However it is probably over-complicated, at least to get started on testing.  I found it easier to start with testing just double values and then refactor later with other types and higher precision.   This will at least get some sanity checks.  
  
 (Although the usefulness of high values for this distribution is unclear, our policy is to aim for tests at least the precision of 128-bit (~36 decimal digits)).  
  
There is also the important question of test values.  Python could be used to generate some test values for a start.  https://cran.r-project.org/web/packages/DirichletReg/DirichletReg.pdf might also be slightly useful but is ominously marked as 'orphaned'  - but is  quite new.  Our favorite Wolfram doesn't seem to be able to help.  
  
The beta distribution tests should be applicable when they should be identical, so this might be the first thing to try?  
  
We also like to have some example(s).  Being able to show how to combine with Boost.Random to replicate the Wikipedia/Python example(s) would be good.  
  
There are lots of tests of the that check for 'dodgy' input like negative, NaN or inf.  These check throwing (if the policy is default).   (and don't forget any examples should use thow'n'catch blocks so that the error messages are displayed). You can probably leave me to tidy these up later.

---

## Review 3 [Commented]

> Username: NAThompson  
> Created_at: 2020-02-28 10:41:15 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/318#pullrequestreview-366297744  

📁 include/boost/math/distributions/dirichlet.hpp

```diff
  20 |+ 
  21 |+ #ifndef BOOST_MATH_DIST_DIRICHLET_HPP
  22 |+ #define BOOST_MATH_DIST_DIRICHLET_HPP
```

> Username: NAThompson  
> Created_at: 2020-02-28 10:28:50 UTC  
> Updated_at: 2020-03-09 17:55:24 UTC  
> Url: https://github.com/boostorg/math/pull/318#discussion_r385618765  

Could you make this BOOST_MATH_DISTRIBUTIONS_DIRICHLET_HPP? I recognize that the other files have gotten out of sync with this pattern over time, but that's what we want to move towards.

> Username: mrityunjay-tripathi  
> Created_at: 2020-02-28 13:06:12 UTC  
> Updated_at: 2020-03-09 17:55:24 UTC  
> Url: https://github.com/boostorg/math/pull/318#discussion_r385684463  

That makes sense.

---

📁 include/boost/math/distributions/dirichlet.hpp

```diff
  44 |+ {
  45 |+ // Common error checking routines for dirichlet distribution function:
  46 |+ template <class VectorType, class RealType, class Policy>
```

> Username: NAThompson  
> Created_at: 2020-02-28 10:31:07 UTC  
> Updated_at: 2020-03-09 17:55:24 UTC  
> Url: https://github.com/boostorg/math/pull/318#discussion_r385619930  

Generally, for classes that accept vectors, we do:  
  
```  
template<class RandomAccessContainer>  
foo (RandomAccessContainer const & v, typename RandomAccessContainer::value_type x);  
```  
and use `using Real = typename RandomAccessContainer::value_type`, which forces the correct deduction.

> Username: mrityunjay-tripathi  
> Created_at: 2020-02-28 13:12:11 UTC  
> Updated_at: 2020-03-09 17:55:24 UTC  
> Url: https://github.com/boostorg/math/pull/318#discussion_r385687145  

Thanks, this solves my first problem in checklist.

---

📁 include/boost/math/distributions/dirichlet.hpp

```diff
  52 |+   for (size_t i = 0; i < concentration.size(); ++i)
  53 |+   {
  54 |+     if (!(boost::math::isfinite)(i) || (i <= 0))
```

> Username: NAThompson  
> Created_at: 2020-02-28 10:32:47 UTC  
> Updated_at: 2020-03-09 17:55:24 UTC  
> Url: https://github.com/boostorg/math/pull/318#discussion_r385620682  

`i <=0`? But `i` is a `size_t`. . .   
  
This will cause warning when used with `Eigen` or any other container that has signed indexes. Use `decltype(concentration.size()) i = 0; i < concentration.size()`. . .

> Username: NAThompson  
> Created_at: 2020-02-28 10:43:20 UTC  
> Updated_at: 2020-03-09 17:55:24 UTC  
> Url: https://github.com/boostorg/math/pull/318#discussion_r385625801  

BTW, in your unit tests, try to use both `std::vector` and an `Eigen` vector as the template type.

> Username: mrityunjay-tripathi  
> Created_at: 2020-02-28 13:06:19 UTC  
> Updated_at: 2020-03-09 17:55:24 UTC  
> Url: https://github.com/boostorg/math/pull/318#discussion_r385684521  

It's really silly, it should be ```concentration[i]```.

---

📁 include/boost/math/distributions/dirichlet.hpp

```diff
  71 |+   for (size_t i = 0; i < p.size(); ++i)
  72 |+   {
  73 |+     if ((i < 0) || (i > 1) || !(boost::math::isfinite)(i))
```

> Username: NAThompson  
> Created_at: 2020-02-28 10:33:30 UTC  
> Updated_at: 2020-03-09 17:55:24 UTC  
> Url: https://github.com/boostorg/math/pull/318#discussion_r385620955  

Again, weird to check for `i < 0` here. Also make `i` the same type as `p.size()`.

> Username: mrityunjay-tripathi  
> Created_at: 2020-02-28 13:07:17 UTC  
> Updated_at: 2020-03-09 17:55:24 UTC  
> Url: https://github.com/boostorg/math/pull/318#discussion_r385684941  

Here, it should be```p[i]```.

> Username: mrityunjay-tripathi  
> Created_at: 2020-02-28 13:08:23 UTC  
> Updated_at: 2020-03-09 17:55:24 UTC  
> Url: https://github.com/boostorg/math/pull/318#discussion_r385685388  

seems like I need more sleep :D

---

📁 include/boost/math/distributions/dirichlet.hpp

```diff
 173 |+ {
 174 |+ public:
 175 |+   dirichlet_distribution(VectorType concentration) : concentration(concentration)
```

> Username: NAThompson  
> Created_at: 2020-02-28 10:35:53 UTC  
> Updated_at: 2020-03-09 17:55:24 UTC  
> Url: https://github.com/boostorg/math/pull/318#discussion_r385622081  

This makes a copy; generally we don't like to copy containers. I'd make this a move constructor: `VectorType && concentration`; also, it's nice to use syntax that shows `concentration` is a member variable: `concentration_` or `m_concentration`.

---

📁 include/boost/math/distributions/dirichlet.hpp

```diff
 169 |+ } // namespace dirichlet_detail
 170 |+ 
 171 |+ template <class VectorType = std::vector<double>, class RealType = double, class Policy = policies::policy<>>
```

> Username: NAThompson  
> Created_at: 2020-02-28 10:36:46 UTC  
> Updated_at: 2020-03-09 17:55:24 UTC  
> Url: https://github.com/boostorg/math/pull/318#discussion_r385622586  

I wouldn't bother with the defaults here. Use `template<class RandomAccessContainer>`, and then use `using Real = typename RandomAccessContainer::value_type`.

---

📁 include/boost/math/distributions/dirichlet.hpp

```diff
 184 |+ 
 185 |+   // Accessor functions:
 186 |+   VectorType Concentration() const
```

> Username: NAThompson  
> Created_at: 2020-02-28 10:37:11 UTC  
> Updated_at: 2020-03-09 17:55:24 UTC  
> Url: https://github.com/boostorg/math/pull/318#discussion_r385622796  

Isn't this accessor a big memcopy?

---

📁 include/boost/math/distributions/dirichlet.hpp

```diff
 189 |+   }
 190 |+ 
 191 |+   size_t Order() const
```

> Username: NAThompson  
> Created_at: 2020-02-28 10:37:51 UTC  
> Updated_at: 2020-03-09 17:55:24 UTC  
> Url: https://github.com/boostorg/math/pull/318#discussion_r385623078  

Generally we want to stick with the snake case conventions.

---

📁 include/boost/math/distributions/dirichlet.hpp

```diff
 234 |+ 
 235 |+ 
 236 |+ template <class VectorType, class RealType, class Policy>
```

> Username: NAThompson  
> Created_at: 2020-02-28 10:38:33 UTC  
> Updated_at: 2020-03-09 17:55:24 UTC  
> Url: https://github.com/boostorg/math/pull/318#discussion_r385623439  

Again, `template<class RandomAccessContainer>` and then `using Real = typename RandomAccessContainer::value_type`.

---

📁 include/boost/math/distributions/dirichlet.hpp

```diff
 256 |+   for (size_t i = 0; i < dist.Order(); ++i)
 257 |+   {
 258 |+     m.push_back(dist.concentration[i] / dist.sum_concentration);
```

> Username: NAThompson  
> Created_at: 2020-02-28 10:39:32 UTC  
> Updated_at: 2020-03-09 17:55:24 UTC  
> Url: https://github.com/boostorg/math/pull/318#discussion_r385623924  

Repeatedly calling `push_back` is bad; get it to the correct size in line 255 and then just fill it up.

---

📁 include/boost/math/distributions/dirichlet.hpp

```diff
 269 |+   for (size_t i = 0; i < dist.Order(); ++i)
 270 |+   {
 271 |+     v.push_back(dist.concentration[i] / dist.sum_concentration * (1 - dist.concentration[i] / dist.sum_concentration) / (1 + dist.sum_concentration));
```

> Username: NAThompson  
> Created_at: 2020-02-28 10:39:51 UTC  
> Updated_at: 2020-03-09 17:55:24 UTC  
> Url: https://github.com/boostorg/math/pull/318#discussion_r385624066  

Again, get rid of the push_back for performance reasons.

---

📁 include/boost/math/distributions/dirichlet.hpp

```diff
 385 |+   for (size_t i = 0; i < dist.Order(); ++i)
 386 |+   {
 387 |+     cumm *= std::pow(x[i], dist.concentration[i]) / tgamma(dist.concentration[i]) / dist.concentration[i];
```

> Username: NAThompson  
> Created_at: 2020-02-28 10:40:55 UTC  
> Updated_at: 2020-03-09 17:55:24 UTC  
> Url: https://github.com/boostorg/math/pull/318#discussion_r385624611  

`std::pow` won't compile in `float128` precision. Use: `using std::pow` and then call `pow`. Also have a unit test showing compatibility with `#include <boost/multiprecision/float128.hpp>`.


---

## Comment 4

> Username: NAThompson  
> Created_at: 2020-02-28 10:43:47 UTC  
> Url: https://github.com/boostorg/math/pull/318#issuecomment-592457164  

Looks like a great start. I'll go through your other items on your checklist later.

---

## Comment 5

> Username: mrityunjay-tripathi  
> Created_at: 2020-02-29 08:48:49 UTC  
> Url: https://github.com/boostorg/math/pull/318#issuecomment-592921236  

Hi @pabristow @NAThompson Can we use something like ```validate_args``` to see if the user wants to check for argument validity. Currently, we are validating the arguments when an instance of the object is created which is inefficient.

---

## Comment 6

> Username: pabristow  
> Created_at: 2020-02-29 10:17:14 UTC  
> Url: https://github.com/boostorg/math/pull/318#issuecomment-592929445  

I have a vague recollection that we had a complaint similar to this many years ago.  In the end we decided to keep the apparently redundant check because there were circumstances when it would allow a 'bad' value to sneak in.  Checking seems quite cheap, as is construction generally?

---

## Comment 7

> Username: mrityunjay-tripathi  
> Created_at: 2020-02-29 11:24:46 UTC  
> Url: https://github.com/boostorg/math/pull/318#issuecomment-592935292  

Right. If some 'invalid' value comes in for reasons unknown to the user, it would be a disaster.

---

## Comment 8

> Username: NAThompson  
> Created_at: 2020-02-29 12:59:35 UTC  
> Url: https://github.com/boostorg/math/pull/318#issuecomment-592943051  

@mrityunjay-tripathi Could you write a google benchmark and show how fast it is with and without the argument validation? This is a really hard question to answer without hard data. An example is [here](https://github.com/boostorg/math/blob/dc52a787c8eacd9d45d9d674a0d479e6f577ba1f/example/daubechies_wavelets/bench.cpp).

---

## Review 9 [Commented]

> Username: NAThompson  
> Created_at: 2020-02-29 13:08:12 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/318#pullrequestreview-366797071  

📁 include/boost/math/distributions/dirichlet.hpp

```diff
  23 | 
  24 | #include <boost/math/distributions/fwd.hpp>
  25 |+ #include <boost/multiprecision/float128.hpp>
```

> Username: NAThompson  
> Created_at: 2020-02-29 13:01:27 UTC  
> Updated_at: 2020-03-09 17:55:24 UTC  
> Url: https://github.com/boostorg/math/pull/318#discussion_r386026906  

Don't include `boost/multiprecision/float128.hpp` in this file. Anyone who wants to use the `float128` type will #include it in their code. For instance, it should be in your unit tests, but not here, because it'll slow compilation.

---

📁 include/boost/math/distributions/dirichlet.hpp

```diff
 194 |-   }
 230 |+   // Get the order of concentration parameters.
 231 |+   decltype(m_alpha.size()) &order() const { return m_alpha.size(); }
```

> Username: NAThompson  
> Created_at: 2020-02-29 13:05:47 UTC  
> Updated_at: 2020-03-09 17:55:24 UTC  
> Url: https://github.com/boostorg/math/pull/318#discussion_r386027148  

This shouldn't be a reference.

> Username: pabristow  
> Created_at: 2020-02-29 16:24:13 UTC  
> Updated_at: 2020-03-09 17:55:24 UTC  
> Url: https://github.com/boostorg/math/pull/318#discussion_r386038641  

We don't test routinely float128 (because it is slow and GCC only).  When the whole package is working OK for the Big Three MSVC, GCC and Clang, we might run a one-off check.  Boost.Multipreciison cpp_bin_float_quad is entirely portable (if slower) but too slow for routine CI testing.  Lets make sure that we can walk first ;-)

---

📁 include/boost/math/distributions/dirichlet.hpp

```diff
 336 |+ {
 337 |+   using RealType = typename RandomAccessContainer::value_type;
 338 |+   RealType ent = std::log(dirichlet_detail::mvar_beta(dist.m_alpha, 0)) + (dirichlet_detail::alpha0(dist.m_alpha) - dist.order()) * digamma(dirichlet_detail::alpha0(dist.m_alpha));
```

> Username: NAThompson  
> Created_at: 2020-02-29 13:06:56 UTC  
> Updated_at: 2020-03-09 17:55:24 UTC  
> Url: https://github.com/boostorg/math/pull/318#discussion_r386027222  

`using std::log; log(dirichet_detail::...`


---

## Comment 10

> Username: NAThompson  
> Created_at: 2020-02-29 13:09:36 UTC  
> Url: https://github.com/boostorg/math/pull/318#issuecomment-592943917  

@jzmaddock , @pabristow : For scalar distribution, using free functions to compute kurtosis, range, so on, is just fine. But for vector types, following this pattern is getting a bit awkward. Should each of these functions go from free functions acting on the class  to member functions?

---

## Comment 11

> Username: NAThompson  
> Created_at: 2020-02-29 13:35:21 UTC  
> Url: https://github.com/boostorg/math/pull/318#issuecomment-592946334  

@mrityunjay-tripathi : We have a file "boost/libs/math/test/math_unit_test.hpp", which you can use to write unit tests. An example is "test/whittaker_shannon_test.cpp".

---

## Comment 12

> Username: mrityunjay-tripathi  
> Created_at: 2020-02-29 16:23:20 UTC  
> Url: https://github.com/boostorg/math/pull/318#issuecomment-592962162  

>@mrityunjay-tripathi : We have a file "boost/libs/math/test/math_unit_test.hpp", which you can use to write unit tests. An example is "test/whittaker_shannon_test.cpp".  
  
This is clear now. I will soon add the tests.

---

## Comment 13

> Username: mrityunjay-tripathi  
> Created_at: 2020-02-29 16:31:18 UTC  
> Url: https://github.com/boostorg/math/pull/318#issuecomment-592963087  

> @mrityunjay-tripathi Could you write a google benchmark and show how fast it is with and without the argument validation? This is a really hard question to answer without hard data. An example is [here](https://github.com/boostorg/math/blob/dc52a787c8eacd9d45d9d674a0d479e6f577ba1f/example/daubechies_wavelets/bench.cpp).  
  
I am not familiar with 'google benchmark', but ready to delve into it. As soon as I add the tests, I will try to write the benchmark as well.

---

## Comment 14

> Username: pabristow  
> Created_at: 2020-02-29 17:25:26 UTC  
> Url: https://github.com/boostorg/math/pull/318#issuecomment-592970618  

> @jzmaddock , @pabristow : For scalar distribution, using free functions to compute kurtosis, range, so on, is just fine. But for vector types, following this pattern is getting a bit awkward. Should each of these functions go from free functions acting on the class to member functions?  
  
@jzmaddock devised this scheme - with good reasons - so he is best qualified to comment on this change to multi-thingys.

---

## Comment 15

> Username: pabristow  
> Created_at: 2020-02-29 17:58:47 UTC  
> Url: https://github.com/boostorg/math/pull/318#issuecomment-592974451  

From: Mrityunjay Tripathi <notifications@github.com>  
Sent: 29 February 2020 16:23  
To: boostorg/math <math@noreply.github.com>  
Cc: Paul A. Bristow <paul@pbristow.uk>; Mention <mention@noreply.github.com>  
Subject: Re: [boostorg/math] adding dirichlet distribution (#318)  
  
  
@mrityunjay-tripathi<https://github.com/mrityunjay-tripathi> : We have a file "boost/libs/math/test/math_unit_test.hpp", which you can use to write unit tests. An example is "test/whittaker_shannon_test.cpp".  
  
This is clear now. I will soon add the tests.  
  
For distributions a distribution test is a much better starting point to see how the Boost.Test is used..  
  
For example :\boost\libs\math\test\test_normal.cpp  
  
I am looking for some ‘known-good’ values from SciPy, for example, but hopefully you have some already..  
  
  
  
  
  
  
  
  
  
  
  
—  
You are receiving this because you were mentioned.  
Reply to this email directly, view it on GitHub<https://github.com/boostorg/math/pull/318?email_source=notifications&email_token=AAIG4AMMMBSY55NF56XX2WDRFE27RA5CNFSM4K5A75T2YY3PNVWWK3TUL52HS4DFVREXG43VMVBW63LNMVXHJKTDN5WW2ZLOORPWSZGOENL6E4Q#issuecomment-592962162>, or unsubscribe<https://github.com/notifications/unsubscribe-auth/AAIG4AKXZZJNYHXHRGK4KALRFE27RANCNFSM4K5A75TQ>.

---

## Comment 16

> Username: jzmaddock  
> Created_at: 2020-02-29 18:07:31 UTC  
> Url: https://github.com/boostorg/math/pull/318#issuecomment-592975246  

> For scalar distribution, using free functions to compute kurtosis, range, so on, is just fine. But for vector types, following this pattern is getting a bit awkward. Should each of these functions go from free functions acting on the class to member functions?  
  
For better or worse I'd prefer to keep to the current pattern if possible.  Implementation and use wise, I think it makes very little practical difference.  
  
I confess to a certain amount of hesitation in adding something that's not entirely mainstream as our first multivariate distribution.  I had always assume that the multivariate normal would come first, but no matter I guess.  
  
With regard to the CDF, I note that multivariate normal has 2 different possible definitions, and I assume that is the case here also.  I think we should be careful about treading on shaky ground here.  Likewise, I assume there is no real notion of a quantile, not least because there is unlikely to be a unique vector of x-values corresponding to any given probability.

---

## Review 17 [Commented]

> Username: jzmaddock  
> Created_at: 2020-02-29 18:08:39 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/318#pullrequestreview-366811423  

📁 include/boost/math/distributions/dirichlet.hpp

```diff
 224 |+ 
 225 |+   // Get the concentration parameters.
 226 |+   RandomAccessContainer &alpha() const { return m_alpha; }
```

> Username: jzmaddock  
> Created_at: 2020-02-29 18:08:39 UTC  
> Updated_at: 2020-03-09 17:55:24 UTC  
> Url: https://github.com/boostorg/math/pull/318#discussion_r386045064  

Should return a const-reference from a const member function here.


---

## Review 18 [Commented]

> Username: jzmaddock  
> Created_at: 2020-02-29 18:09:06 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/318#pullrequestreview-366811451  

📁 include/boost/math/distributions/dirichlet.hpp

```diff
 229 |+ 
 230 |+   // Get the order of concentration parameters.
 231 |+   decltype(m_alpha.size()) &order() const { return m_alpha.size(); }
```

> Username: jzmaddock  
> Created_at: 2020-02-29 18:09:06 UTC  
> Updated_at: 2020-03-09 17:55:24 UTC  
> Url: https://github.com/boostorg/math/pull/318#discussion_r386045097  

This returns a reference to a temporary - should return a value.


---

## Comment 19

> Username: NAThompson  
> Created_at: 2020-02-29 18:46:30 UTC  
> Url: https://github.com/boostorg/math/pull/318#issuecomment-592980968  

> I am not familiar with 'google benchmark', but ready to delve into it. As soon as I add the tests, I will try to write the benchmark as well.  
  
You will definitely enjoy adding google benchmark to your toolkit. It's amazing.

---

## Comment 20

> Username: NAThompson  
> Created_at: 2020-02-29 18:48:02 UTC  
> Updated_at: 2020-02-29 20:19:06 UTC  
> Url: https://github.com/boostorg/math/pull/318#issuecomment-592981125  

> I confess to a certain amount of hesitation in adding something that's not entirely mainstream as our first multivariate distribution. I had always assume that the multivariate normal would come first, but no matter I guess.  
  
I think the problem is that the multivariate normal factors into products so that there is little demand for such a thing. Writing down a useful definition of the cdf that translates into an ergonomic API is going to be a problem though . .

---

## Comment 21

> Username: mrityunjay-tripathi  
> Created_at: 2020-03-01 05:57:41 UTC  
> Url: https://github.com/boostorg/math/pull/318#issuecomment-593059807  

> Likewise, I assume there is no real notion of a quantile, not least because there is unlikely to be a unique vector of x-values corresponding to any given probability.  
  
@jzmaddock I thought of using some random values so that they satisfy the condition. But it seems there is no practical use for this. I was putting it for the sense of completeness of the implementation.

---

## Comment 22

> Username: mrityunjay-tripathi  
> Created_at: 2020-03-01 08:13:13 UTC  
> Url: https://github.com/boostorg/math/pull/318#issuecomment-593069131  

>Writing down a useful definition of the cdf that translates into an ergonomic API is going to be a problem though . .  
  
@NAThompson: I've used this deduction to calculate the CDF.  
  
![cdf](https://user-images.githubusercontent.com/35535378/75621061-ea84c780-5bb5-11ea-92c6-ae8ce9bac9e0.jpg)  
![cdf1](https://user-images.githubusercontent.com/35535378/75621140-22d8d580-5bb7-11ea-82a0-3367d9994fbb.jpg)  
  
Talking about efficiency, this distribution obviously is computationally expensive.

---

## Comment 23

> Username: NAThompson  
> Created_at: 2020-03-01 17:34:12 UTC  
> Updated_at: 2020-03-01 19:54:00 UTC  
> Url: https://github.com/boostorg/math/pull/318#issuecomment-593122775  

```  
./include/boost/math/distributions/dirichlet.hpp:53:20: error: need 'typename' before 'RandomAccessContainer::value_type' because 'RandomAccessContainer' is a dependent scope  
   53 |   using RealType = RandomAccessContainer::value_type;  
      |                    ^~~~~~~~~~~~~~~~~~~~~  
      |                    typename  
```  
  
Also:  
  
```  
./include/boost/math/distributions/dirichlet.hpp:231:12: error: 'm_alpha' was not declared in this scope; did you mean 'alpha'?  
  231 |   decltype(m_alpha.size()) &order() const { return m_alpha.size(); }  
      |            ^~~~~~~  
      |            alpha  
```

---

## Comment 24

> Username: mrityunjay-tripathi  
> Created_at: 2020-03-02 10:59:49 UTC  
> Updated_at: 2020-03-02 11:00:57 UTC  
> Url: https://github.com/boostorg/math/pull/318#issuecomment-593346058  

I was trying this [sample program from scipy](https://docs.scipy.org/doc/scipy/reference/generated/scipy.stats.dirichlet.html) but the values I am getting is not in sync with that of scipy.  
Not even the 'mean' is correct! What might be the reason?  
```  
#include <bits/stdc++.h>  
#include <boost/math/distributions/dirichlet.hpp>  
using namespace std;  
  
void print(std::vector<long double> v)  
{  
  for (size_t i = 0; i < v.size(); ++i)  
  {  
    cout<<v[i]<<" ";  
  }  
  cout<<endl;  
}  
int main()  
{  
  std::vector<long double> alpha = {0.4, 5.0, 15.0};  
  std::vector<long double> x = {0.2, 0.2, 0.6};  
    
  using VectorType = typename std::vector<long double>;  
    
  boost::math::dirichlet_distribution<VectorType> diri(std::move(alpha));  
  long double p = pdf(diri, x);  
  long double c = cdf(diri, x);  
  std::vector<long double> m = mean(diri);  
  std::vector<long double> v = variance(diri);  
  std::vector<long double> std = standard_deviation(diri);  
  long double en = entropy(diri);  
  cout<<"pdf: "<<p<<endl;  
  cout<<"cdf: "<<c<<endl;  
  cout<<"entropy: "<<en<<endl;  
    
  cout<<"mean: ";  
  print(m);  
  cout<<"variance: ";  
  print(v);  
  cout<<"statndard deviation: ";  
  print(std);  
}  
```  
The output:  
```  
pdf: 0.0203131  
cdf: 6.90545e-05  
entropy: -nan  
mean: 0.02 0.25 0.75   
variance: 0.000933333 0.00892857 0.00892857   
statndard deviation: 0.0305505 0.0944911 0.0944911   
```

---

## Review 25 [Commented]

> Username: NAThompson  
> Created_at: 2020-03-02 11:56:53 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/318#pullrequestreview-367142694  

📁 include/boost/math/distributions/dirichlet.hpp

```diff
 203 |   // Set the concentration parameters.
 228 |-   RandomAccessContainer &alpha() { return m_alpha; }
 204 |+   RandomAccessContainer &set_alpha() { return m_alpha; }
```

> Username: NAThompson  
> Created_at: 2020-03-02 11:54:22 UTC  
> Updated_at: 2020-03-09 17:55:24 UTC  
> Url: https://github.com/boostorg/math/pull/318#discussion_r386349866  

What is the use case for `set_alpha()`? Isn't `alpha` set in the constructor?

> Username: mrityunjay-tripathi  
> Created_at: 2020-03-02 12:13:30 UTC  
> Updated_at: 2020-03-09 17:55:24 UTC  
> Url: https://github.com/boostorg/math/pull/318#discussion_r386357600  

It's not that important but provides a little flexibility. This helps in case ```m_alpha``` (concentration parameter) needs to be changed (or updated) on the go.

> Username: NAThompson  
> Created_at: 2020-03-02 12:15:58 UTC  
> Updated_at: 2020-03-09 17:55:24 UTC  
> Url: https://github.com/boostorg/math/pull/318#discussion_r386358575  

If you don't have an applications that needs this, I would definitely remove it. In fact, `set_alpha` is more expensive than generating a new object since instantiation is done by move.

> Username: mrityunjay-tripathi  
> Created_at: 2020-03-02 13:44:54 UTC  
> Updated_at: 2020-03-09 17:55:24 UTC  
> Url: https://github.com/boostorg/math/pull/318#discussion_r386399866  

No worries. I will remove it.

---

📁 include/boost/math/distributions/dirichlet.hpp

```diff
 206 |   // Get the order of concentration parameters.
 231 |-   decltype(m_alpha.size()) &order() const { return m_alpha.size(); }
 207 |+   size_t order() const { return m_alpha.size(); }
```

> Username: NAThompson  
> Created_at: 2020-03-02 11:55:08 UTC  
> Updated_at: 2020-03-09 17:55:24 UTC  
> Url: https://github.com/boostorg/math/pull/318#discussion_r386350157  

I would still use `decltype(m_alpha.size())` just to get rid of annoying sign compare warnings for RandomAccessContainers with signed indexes.

> Username: mrityunjay-tripathi  
> Created_at: 2020-03-03 07:09:53 UTC  
> Updated_at: 2020-03-09 17:55:24 UTC  
> Url: https://github.com/boostorg/math/pull/318#discussion_r386832265  

that was throwing error (not in scope).

> Username: pabristow  
> Created_at: 2020-03-03 09:21:14 UTC  
> Updated_at: 2020-03-09 17:55:24 UTC  
> Url: https://github.com/boostorg/math/pull/318#discussion_r386888723  

I trust that your demo/test program has try'n'catch blocks so that throw error messages are visible, rather than unhelpfully bombing with abort

> Username: NAThompson  
> Created_at: 2020-03-03 12:16:25 UTC  
> Updated_at: 2020-03-09 17:55:24 UTC  
> Url: https://github.com/boostorg/math/pull/318#discussion_r386980813  

@pabristow : That only happens on Windows command line; in other shells the programs don't abort and do give helpful error messages.

> Username: NAThompson  
> Created_at: 2020-03-03 12:17:14 UTC  
> Updated_at: 2020-03-09 17:55:24 UTC  
> Url: https://github.com/boostorg/math/pull/318#discussion_r386981195  

@mrityunjay-tripathi: Sounds good.

> Username: pabristow  
> Created_at: 2020-03-03 14:24:24 UTC  
> Updated_at: 2020-03-09 17:55:24 UTC  
> Url: https://github.com/boostorg/math/pull/318#discussion_r387052171  

We have had many complaints about not seeing the error messages, so not knowing what platform was being used, I thought it was worth repeating.  I'm sure Microsoft would say it was a feature, not a bug, which I suppose it is...

> Username: NAThompson  
> Created_at: 2020-03-03 14:27:35 UTC  
> Updated_at: 2020-03-09 17:55:24 UTC  
> Url: https://github.com/boostorg/math/pull/318#discussion_r387054415  

How about?  
  
```cpp  
auto order() const { return m_alpha.size(); }  
```

> Username: mrityunjay-tripathi  
> Created_at: 2020-03-03 14:28:53 UTC  
> Updated_at: 2020-03-09 17:55:24 UTC  
> Url: https://github.com/boostorg/math/pull/318#discussion_r387055384  

That's nice.

> Username: pabristow  
> Created_at: 2020-03-03 15:50:32 UTC  
> Updated_at: 2020-03-09 17:55:24 UTC  
> Url: https://github.com/boostorg/math/pull/318#discussion_r387112800  

But there are users  (Poor Peasants?) still in the Dark Ages of C++03.  Do we need to cut them off gratuitously just for an auto?  (or perhaps they are they cut off already?)

---

📁 include/boost/math/distributions/dirichlet.hpp

```diff
 240 |+   RealType sum_alpha() const
 241 |+   {
 242 |+     return accumulate(m_alpha.begin(), m_alpha.end(), 0);
```

> Username: NAThompson  
> Created_at: 2020-03-02 11:56:18 UTC  
> Updated_at: 2020-03-09 17:55:24 UTC  
> Url: https://github.com/boostorg/math/pull/318#discussion_r386350637  

Should this be `std::accumulate`? `std::accumulate` works on any type with `+` defined; it doesn't need to use ADL for multiprecision types.


---

## Comment 26

> Username: NAThompson  
> Created_at: 2020-03-02 12:00:57 UTC  
> Updated_at: 2020-03-02 13:17:48 UTC  
> Url: https://github.com/boostorg/math/pull/318#issuecomment-593368806  

Get your unit tests in, make them as simple and as meaningful as possible, and these errors will become obvious to you. Though I think you might be using "moved-from" data; some of your functions take data by move that should be taken by reference.  
  
This is really coming together nicely; good work.

---

## Comment 27

> Username: mrityunjay-tripathi  
> Created_at: 2020-03-02 13:36:53 UTC  
> Url: https://github.com/boostorg/math/pull/318#issuecomment-593405575  

Ohh. Got it!  
>  RealType sum_alpha() const  
  {  
    return accumulate(m_alpha.begin(), m_alpha.end(), 0);  
  
The initializing value must be ```RealType```. Here I made the mistake of keeping it ```int```.

---

## Comment 28

> Username: pabristow  
> Created_at: 2020-03-02 17:28:41 UTC  
> Url: https://github.com/boostorg/math/pull/318#issuecomment-593519342  

Something has gone wrong ;-)  (It often does...)   
  
Special cases have always proved good, especially if the result should be 'exact'.  
  
https://docs.scipy.org/doc/scipy/reference/generated/scipy.stats.dirichlet.html  
  
looks recent (Last updated on Dec 19, 2019). so likely to be correct?  
  
After a little light googling, I also found a more recent bit of code in R and some tests that might be helpful?  
  
https://github.com/bertcarnell/dirichlet/blob/7954c574e2bb1359883e8d4b810d266acc6c5a35/tests/testthat/test-dirichlet.r  
  
  it("A two parameter Dirichlet is equivalent to a beta", {  
    expect_equal(ddirichlet(c(0.3, 0.7), c(2, 3)), dbeta(0.3, 2, 3))  
  
http://ugrad.stat.ubc.ca/R/library/MCMCpack/html/dirichlet.html  might be useful too?  Old but might be correct nonetheless?  
  
HTH

---

## Comment 29

> Username: mrityunjay-tripathi  
> Created_at: 2020-03-02 17:34:31 UTC  
> Url: https://github.com/boostorg/math/pull/318#issuecomment-593522087  

>After a little light googling, I also found a more recent bit of code in R and some tests that might be helpful?  
https://github.com/bertcarnell/dirichlet/blob/7954c574e2bb1359883e8d4b810d266acc6c5a35/tests/testthat/test-dirichlet.r  
  
@pabristow: This seems helpful. Thank you.

---

## Comment 30

> Username: NAThompson  
> Created_at: 2020-03-03 12:18:46 UTC  
> Updated_at: 2020-03-03 12:19:23 UTC  
> Url: https://github.com/boostorg/math/pull/318#issuecomment-593922367  

BTW, your code is a little verbose:  
  
```cpp  
  std::vector<long double> alpha = {0.4, 5.0, 15.0};  
  std::vector<long double> x = {0.2, 0.2, 0.6};  
    
  using VectorType = typename std::vector<long double>;  
    
  boost::math::dirichlet_distribution<VectorType> diri(std::move(alpha));  
```  
  
Instead:  
  
```cpp  
std::vector<double> alpha = {0.4, 5.0, 15.0};  
std::vector<double> x = {0.2, 0.2, 0.6};  
auto diri = boost::math::dirichlet_distribution(std::move(alpha));  
```  
  
Note how long double doesn't help since your floating point literals are parsed as doubles, and that the template argument can be deduced by the compiler.

---

## Comment 31

> Username: mrityunjay-tripathi  
> Created_at: 2020-03-03 14:32:05 UTC  
> Url: https://github.com/boostorg/math/pull/318#issuecomment-593980423  

>Note how long double doesn't help since your floating point literals are parsed as doubles, and that the template argument can be deduced by the compiler.  
  
How about using 'L' as suffix for those floating point literals?

---

## Comment 32

> Username: NAThompson  
> Created_at: 2020-03-03 14:33:46 UTC  
> Url: https://github.com/boostorg/math/pull/318#issuecomment-593981215  

> How about using 'L' as suffix for those floating point literals?  
  
Yes, that would fix the issue.

---

## Comment 33

> Username: mrityunjay-tripathi  
> Created_at: 2020-03-03 14:41:49 UTC  
> Updated_at: 2020-03-03 14:49:05 UTC  
> Url: https://github.com/boostorg/math/pull/318#issuecomment-593985182  

```  
#include <bits/stdc++.h>  
#include <boost/math/distributions/dirichlet.hpp>  
using namespace std;  
  
template <typename T>  
void print(std::vector<T> v)  
{  
  for (size_t i = 0; i < v.size(); ++i)  
  {  
    cout<<fixed<<setprecision(20)<<v[i]<<", ";  
  }  
  cout<<endl;  
}  
int main()  
{  
  using RealType = long double;  
  using VectorType = typename std::vector<RealType>;  
    
  VectorType alpha = {3.462347646231L, 5.2136450991334L, 15.341L};  
  VectorType x = {0.03536252L, 0.28787621346L, 0.676761265L};  
    
  boost::math::dirichlet_distribution<VectorType> diri(std::move(alpha));  
  RealType p = pdf(diri, x);  
  RealType c = cdf(diri, x);  
  VectorType m = mean(diri);  
  VectorType v = variance(diri);  
  VectorType std = standard_deviation(diri);  
  RealType en = entropy(diri);  
    
  cout<<"alpha: ";  
  print(diri.get_alpha());  
  cout<<"x: ";  
  print(x);  
    
  cout<<"pdf: "<<p<<endl;  
  cout<<"cdf: "<<c<<endl;  
  cout<<"entropy: "<<en<<endl;  
    
  cout<<"mean: ";  
  print(m);  
  cout<<"variance: ";  
  print(v);  
  cout<<"standard deviation: ";  
  print(std);  
  cout<<"Normalizing Constant, B: "<<diri.normalizing_constant()<<endl;  
  cout<<"Sum of Concentration Parameters, a0: "<<diri.sum_alpha()<<endl;  
  cout<<"Sum of quantiles: "<<std::accumulate(x.begin(), x.end(), 0.0)<<endl;  
  
}    
```  
  
The Output:  
```  
alpha: 3.46234764623100000009, 5.21364509913340000002, 15.34100000000000000016,   
x: 0.03536252000000000000, 0.28787621346000000000, 0.67676126499999999999,   
pdf: 6.12940449892651902141  
cdf: 0.00015248845250954929  
entropy: 93.39123000056522383439  
mean: 0.14416241379342879160, 0.21708151201151955451, 0.63875607419505165389,   
variance: 0.00493183227490614376, 0.00679366744373401038, 0.00922360070303550949,   
standard deviation: 0.07022700531067905191, 0.08242370680656148689, 0.09603957883620434480,   
Normalizing Constant, B: 0.00000000084752204199  
Sum of Concentration Parameters, a0: 24.01699274536439999983  
Sum of quantiles: 0.99999999845999998360  
  
```  
I also cross-checked it with scipy. Its giving correct results now.  
I am almost done with the tests. I will make PR for it probably tomorrow.

---

## Comment 34

> Username: NAThompson  
> Created_at: 2020-03-03 14:43:18 UTC  
> Url: https://github.com/boostorg/math/pull/318#issuecomment-593985876  

What is the header `#include <bits/stdc++.h>` doing?

---

## Comment 35

> Username: mrityunjay-tripathi  
> Created_at: 2020-03-03 14:46:11 UTC  
> Updated_at: 2020-03-03 14:50:50 UTC  
> Url: https://github.com/boostorg/math/pull/318#issuecomment-593987355  

>What is the header #include <bits/stdc++.h> doing?  
  
Nothing xD. I have just the habit of putting it whenever writing a sketch code. (maybe bad practice)  
In place of it, we can use   
```  
#include <iostream>  
#include <vector>  
#include <iomanip>  
```

---

## Comment 36

> Username: NAThompson  
> Created_at: 2020-03-03 14:51:36 UTC  
> Url: https://github.com/boostorg/math/pull/318#issuecomment-593990138  

Cool. I would get rid of the header and start cleaning up the code you wrote there for an example (add it to (say) `examples/dirichlet_distribution.cpp`).  
  
I'd also pass the vector by `const &`, and get rid of the `using namespace std;`.

---

## Comment 37

> Username: pabristow  
> Created_at: 2020-03-03 15:17:10 UTC  
> Url: https://github.com/boostorg/math/pull/318#issuecomment-594003289  

I'm just checking this out using  
  
Paul@hetpD MINGW64 /i/boost/libs/math (mrityunjay-tripathi/dirichlet)  
$ git log  
commit a73ff6b70ba66911ad09d2305a9e7818d55a348c (HEAD -> mrityunjay-tripathi/dirichlet)  
Author: Mrityunjay Tripathi <mrityunjay2668@gmail.com>  
Date:   Mon Mar 2 16:06:31 2020 +0530  
    slight errors removed  
  
I needed to remove  
  
`using namespace std; `  
  
 as this is not Boost good-practice on grounds of the risk of name clash.  
  
(It might be OK in .cpp examples, but it is much better to avoid completely.  It always leads to trouble eventually, and can be very puzzling to trace).  
  
and I needed to add  
  
#include <iostream>  
#include <numeric>  
  
and use std::cout and std::endl;   
  
but it fails to compile  
  
\boost\boost\math\distributions\dirichlet.hpp(235): error C4700: uninitialized local variable 'mb' used  
  
I tried setting mb to zero (assuming it might be zeroed by default), but that still doesn't give anything like the results you show.  (MSVC doesn't do 80-but long doubles, only 64-bit but it should be nearly the same?).  
  
 alpha: 3.46235 5.21365 15.341  
  x: 0.0353625 0.287876 0.676761  
  pdf: inf  
  cdf: 6.28359e-06  
  entropy: -inf  
  mean: 0.150537 0.22668 0.667  
  variance: 0.00532815 0.00730401 0.00925463  
  standard deviation: 0.0729942 0.0854635 0.096201  
  Normalizing Constant, B: 0  
  Sum of Concentration Parameters, a0: 23  
  Sum of quantiles: 1  
  
What am I doing wrong?  (Old version of dirichlet.hpp).

---

## Comment 38

> Username: mrityunjay-tripathi  
> Created_at: 2020-03-03 15:20:03 UTC  
> Updated_at: 2020-03-03 15:21:22 UTC  
> Url: https://github.com/boostorg/math/pull/318#issuecomment-594005052  

@pabristow: Yes, that one is old version of dirichlet.hpp  
I removed errors locally and yet to push it.  
  
mb is initialized to 1, and one other thing was there to correct (i forgot).

---

## Comment 39

> Username: NAThompson  
> Created_at: 2020-03-03 15:33:01 UTC  
> Url: https://github.com/boostorg/math/pull/318#issuecomment-594011794  

@mrityunjay-tripathi : If you haven't added any unit tests, tag your PR commit messages with `[CI SKIP]`.

---

## Review 40 [Commented]

> Username: mrityunjay-tripathi  
> Created_at: 2020-03-03 16:11:09 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/318#pullrequestreview-368114500  

📁 include/boost/math/distributions/dirichlet.hpp

```diff
 212 |-       RandomAccessContainer &&variance) // Expected value of variance.
 209 |+       RandomAccessContainer &mean,     // Expected value of mean.
 210 |+       RandomAccessContainer &variance) const// Expected value of variance.
```

> Username: mrityunjay-tripathi  
> Created_at: 2020-03-03 16:11:09 UTC  
> Updated_at: 2020-03-09 17:55:24 UTC  
> Url: https://github.com/boostorg/math/pull/318#discussion_r387127292  

```const``` here needs to be removed. don't know what i was experimenting.

> Username: NAThompson  
> Created_at: 2020-03-04 11:39:18 UTC  
> Updated_at: 2020-03-09 17:55:24 UTC  
> Url: https://github.com/boostorg/math/pull/318#discussion_r387611934  

Why does `const` need to be removed here?

> Username: mrityunjay-tripathi  
> Created_at: 2020-03-04 13:45:02 UTC  
> Updated_at: 2020-03-09 17:55:24 UTC  
> Url: https://github.com/boostorg/math/pull/318#discussion_r387674275  

Because m_alpha is being assigned in this function but ```const``` forces it to be read-only.


---

## Comment 41

> Username: NAThompson  
> Created_at: 2020-03-03 20:42:46 UTC  
> Url: https://github.com/boostorg/math/pull/318#issuecomment-594158480  

I have slightly reworked your example:  
  
```cpp  
#include <iostream>  
#include <numeric>  
#include <boost/math/distributions/dirichlet.hpp>  
  
template <typename RandomAccessContainer>  
void print(RandomAccessContainer const & v)  
{  
   using Real = typename RandomAccessContainer::value_type;  
   std::cout << std::fixed << std::setprecision(std::numeric_limits<Real>::digits10 + 2);  
   std::cout << "{";  
   for (size_t i = 0; i < v.size() - 1; ++i)  
   {  
      std::cout << v[i] << ", ";  
   }  
   std::cout << v.back() << "}\n";  
}  
  
int main()  
{  
   using Real = long double;  
     
   std::vector<Real> alpha = {3.462347646231L, 5.2136450991334L, 15.341L};  
   std::vector<Real> x = {0.03536252L, 0.28787621346L, 0.676761265L};  
     
   auto diri = boost::math::dirichlet_distribution(std::move(alpha));  
   Real c = cdf(diri, x);  
   auto m = mean(diri);  
   auto v = variance(diri);  
   auto std = standard_deviation(diri);  
   Real en = entropy(diri);  
     
   std::cout << "alpha = ";  
   print(diri.get_alpha());  
  
   std::cout<< "x = ";  
   print(x);  
     
   std::cout << "pdf = " << pdf(diri, x) << "\n";  
  
   std::cout << "cdf = " << c << "\n";  
   std::cout << "entropy: " << en << "\n";  
     
   std::cout << "mean: ";  
   print(m);  
   std::cout << "variance: ";  
   print(v);  
   std::cout << "standard deviation: ";  
   print(std);  
   std::cout << "Normalizing Constant, B: " << diri.normalizing_constant() << "\n";  
   std::cout << "Sum of Concentration Parameters, a0: "<<diri.sum_alpha() << "\n";  
   std::cout << "Sum of quantiles: " << std::accumulate(x.begin(), x.end(), 0.0L) << "\n";  
}    
```  
  
I have run this with `-fsanitize=address -fsanitize=undefined`, which is a very strong test, so things are looking good.

---

## Comment 42

> Username: mrityunjay-tripathi  
> Created_at: 2020-03-04 02:20:57 UTC  
> Url: https://github.com/boostorg/math/pull/318#issuecomment-594282019  

>auto diri = boost::math::dirichlet_distribution(std::move(alpha));  
  
I think this is what you intended?  
```auto diri = boost::math::dirichlet_distribution<std::vector<Real>>(std::move(alpha));```  
  
This is required since the default type was ```std::vector<double>``` and here we are using ```Real``` which is ```long double```.

---

## Comment 43

> Username: pabristow  
> Created_at: 2020-03-04 11:21:45 UTC  
> Url: https://github.com/boostorg/math/pull/318#issuecomment-594465220  

`std::setprecision(std::numeric_limits<Real>::max_digits10);`  
  
is more correct and clearer than  
  
`std::setprecision(std::numeric_limits<Real>::digits10 + 2);`  
  
And I like to use it thus  
  
```  
//! Show value of z to the full possibly-significant max_digits10 precision of type T.  
//! Restores io state on exit.  
template<typename T>  
void show_value(T z)  
{  
  std::streamsize precision = std::cout.precision(std::numeric_limits<T>::max_digits10);  // Save.  
  std::cout.precision(std::numeric_limits<T>::max_digits10); // Show all posssibly significant digits.  
  std::ios::fmtflags flags(std::cout.flags());  
  std::cout.setf(std::ios_base::showpoint); // Include any trailing zeros.  
  std::cout << z;  
  // Restore:  
  std::cout.precision(precision);  
  std::cout.flags(flags);  
} // template<typename T> void show_value(T z)  
  
```  
to show the implied precision with no zero-information trailing junk, only those that are noisy but possibly-significant, but including trailing zeros.  
  
Pedants will rule the world, eventually!

---

## Comment 44

> Username: NAThompson  
> Created_at: 2020-03-04 11:38:05 UTC  
> Url: https://github.com/boostorg/math/pull/318#issuecomment-594471460  

> I think this is what you intended?  
auto diri = boost::math::dirichlet_distribution<std::vector<Real>>(std::move(alpha));  
  
This is required since the default type was std::vector<double> and here we are using Real which is long double.  
  
It should compile (in C++17 mode at least, I think in C++14) without the explicit template argument, so removing the default type `std::vector<double>` will make the API more ergonomic.  
  
@pabristow: Good catch on `max_digits10`.

---

## Comment 45

> Username: pabristow  
> Created_at: 2020-03-04 11:44:22 UTC  
> Url: https://github.com/boostorg/math/pull/318#issuecomment-594473889  

Using MSVC latest c++14, I've now got   
  
(after adding #include <numeric> to dirichlet.hpp to find std::accumulate)  
```  
  
  dirichlet_basic_examples.cpp  
  Generating code  
  Previous IPDB not found, fall back to full compilation.  
  All 421 functions were compiled because no usable IPDB/IOBJ from previous compilation was found.  
  Finished generating code  
  dirichlet_examples.vcxproj -> I:\Cpp\math\x64\Release\dirichlet_examples.exe  
  Autorun "I:\Cpp\math\x64\Release\dirichlet_examples.exe"  
  alpha: 3.4623476462310001 5.2136450991333998 15.340999999999999   
  x: 0.035362520000000001 0.28787621345999997 0.67676126500000000   
  pdf: 6.1294  
  cdf: 0.000152488  
  entropy: 93.3912  
  mean: 0.14416241379342881 0.21708151201151957 0.63875607419505165   
  variance: 0.0049318322749061453 0.0067936674437340111 0.0092236007030355102   
  standard deviation: 0.070227005310679061 0.082423706806561498 0.096039578836204342   
  Normalizing Constant, B: 8.47522e-10  
  Sum of Concentration Parameters, a0: 24.017  
  Sum of quantiles: 1  
```  
  
Looks promising?   
  
(Will work on some Boost.test using these values - but we need more tests of course!)

---

## Comment 46

> Username: mrityunjay-tripathi  
> Created_at: 2020-03-04 12:03:35 UTC  
> Url: https://github.com/boostorg/math/pull/318#issuecomment-594481958  

>It should compile (in C++17 mode at least, I think in C++14) without the explicit template argument, so removing the default type std::vector<double> will make the API more ergonomic.  
  
@NAThompson: Maybe in C++17. I am using C++14 and it throws this error.   
```  
dirichlet_test.cpp:29:51: error: missing template arguments before ‘(’ token  
    auto diri = boost::math::dirichlet_distribution(std::move(alpha));  
```

---

## Comment 47

> Username: mrityunjay-tripathi  
> Created_at: 2020-03-06 09:07:11 UTC  
> Url: https://github.com/boostorg/math/pull/318#issuecomment-595672441  

Hi @pabristow @NAThompson: I've added tests (still more sample tests to be added, I was busy with my assignments). But I wanted to check if [this error](https://docs.google.com/document/d/1fDOplqMiwvP5XsMksxVuhGOAvQc4VPLS5uNMUwqzvzw/edit?usp=sharing) also comes during CI. I am worried if this turns out to be something stupid :(

---

## Comment 48

> Username: pabristow  
> Created_at: 2020-03-06 09:36:43 UTC  
> Url: https://github.com/boostorg/math/pull/318#issuecomment-595684123  

I've also been working up a basic example and checking that it works with Boost.Multiprecision.  
  
with these patches  
  
```  
 #include <utility>  
+#include <numeric>   <<<<<<<<<<<<<<< Needed to find std::accumulate  
```  
(The example.cpp happens to use accumulate, but this isn't always so.  Boost has uncovered countless examples of missing includes.  Always best to be explicit and not rely on includes in other files).  
  
```  
@@ -100,7 +101,7 @@ inline bool check_x(const char *function,  
       return false;  
     }  
   }  
-  return std::accumulate(x.begin(), x.end(), 0.0) <= 1.0;  
+  return std::accumulate(x.begin(), x.end(), static_cast<RealType>(0.0)) <= 1.0;  
 } // bool check_x  
  
 template <class RandomAccessContainer, class Policy>  
@@ -222,10 +223,10 @@ public:  
     }  
   } // void find_alpha  
  
-  RealType normalizing_constant(RealType b = 0.0) const  
+  RealType normalizing_constant(RealType b = static_cast<RealType>(0.0)) const  
   {  
     // B(a1,a2,...ak) = (tgamma(a1)*tgamma(a2)...*tgamma(ak)/tgamma(a1+a2+...+ak)  
-    RealType mb = 1.0;  
+    RealType mb = static_cast<RealType>(1);  
     RealType alpha_sum = accumulate(m_alpha.begin(), m_alpha.end(), b * m_alpha.size());  
     for (decltype(m_alpha.size()) i = 0; i < m_alpha.size(); ++i)  
```  
  
These static_casts cause a lot of clutter, but are essential for UDTs.  
  
I can compute and pretty-print your SciPy sample values for the fundamental types, float, double ... and as well, float128 (on GCC only) cpp_bin_float_quad and cpp_dec_float_50.  
  
Although I can't yet see a use for extravagant precisions like these, it is useful to confirm that the library can be use with UserDefinedTypes for which there are a typical example.  
  
We don't routinely test (too slow) but a macro BOOST_MATH_TEST_MULTIPRECISION can be used to control, and/or they can be placed in a different in the B2 jamfile.  
  
I'll look at your error messages separately.

---

## Comment 49

> Username: pabristow  
> Created_at: 2020-03-06 10:16:22 UTC  
> Url: https://github.com/boostorg/math/pull/318#issuecomment-595700224  

I’ve fired up your test using MSVC and I see immediately that you do not have A check around   
  
#include <boost/multiprecision/float128.hpp>  
  
FLOAT128 is only possible on GCC, but not Clang not MSVC.  
  
A way of dealing with this is to check   
  
#ifdef BOOST_HAS_FLOAT128  
  
I’ve put  
  
```  
#ifdef BOOST_MATH_TEST_FLOAT128  
#ifdef BOOST_HAS_FLOAT128  
// Including this header below without float128 triggers:  
// fatal error C1189: #error:  "Sorry compiler is neither GCC, not Intel, don't know how to configure this header."  
#include <boost/multiprecision/float128.hpp>  
using boost::multiprecision::float128;  
#endif // ifdef BOOST_HAS_FLOAT128  
#endif // #ifdef #ifdef BOOST_MATH_TEST_FLOAT128  
```  
A test of BOOST_MATH_TEST_FLOAT128   is also useful because of the time that float128 tests may take, it’s not worth testing routinely.    
  
2  As far as I remember, the macro defines for Boost.Test must come *before* the #include in order to have the desired effect.  
  
// Defines must come before includes.  
#define BOOST_TEST_MAIN  
#define BOOST_MATH_CHECK_THROW BOOST_CHECK_THROW  
#define BOOST_LIB_DIAGNOSTIC "on" // Report library file details.  
  
#include <boost/test/unit_test.hpp>                       // for test_main  
  
(aside Although it is not enforced, I believe that Boost best practice is to place the std:: includes after all the Boost ones, so that Boost takes precedence.)  
  
#define BOOST_MATH_CHECK_THROW BOOST_CHECK_THROW  
  
Is needed to get the real check command to work.  (Some platforms don’t have throws).  
  
3  There are zillions of warnings from constants – very tiresomely, they all need casting to RealType to keep the compiler quiet.  Sigh!  
  
4    in mode, the compiler didn’t like the return result for reasons I have yet to understand.  But it should not normally be executed, so I’ve commented it out to get going for the rest of the tests.  
  
5  Below is using VS , I'll try using the b2 jamfile next...  
  
`  if (dist.get_alpha()[i] <= 1)  
    {  
      result = policies::raise_domain_error<RealType>(  
          function,  
          "mode undefined for alpha = %1%, must be > 1!", dist.get_alpha()[i], Policy());  
     // return result;  
    }`  
  
  
```  
  test_dirichlet_dist.vcxproj -> I:\Cpp\math\x64\Release\test_dirichlet_dist.exe  
  Autorun "I:\Cpp\math\x64\Release\test_dirichlet_dist.exe"  
  Running 1 test case...  
  Platform: Win32  
  Compiler: Microsoft Visual C++ version 14.2  
  STL     : Dinkumware standard library version 650  
  Boost   : 1.73.0  
I:/boost/libs/math/test/test_dirchlet_dist.cpp(126): error : in "test_main": exception std::domain_error expected but not raised  
I:/boost/libs/math/test/test_dirchlet_dist.cpp(172): error : in "test_main": exception std::domain_error expected but not raised  
I:/boost/libs/math/test/test_dirchlet_dist.cpp(75): error : in "test_main": difference{3.03688e-08} between boost::math::pdf(diri, x){0.0586615382185217} and pdf{0.058661539999999998} exceeds 2.2204460492503131e-09  
I:/boost/libs/math/test/test_dirchlet_dist.cpp(76): error : in "test_main": difference{6.21687e-06} between boost::math::cdf(diri, x){0.00071693445706326182} and cdf{0.00071692999999999995} exceeds 2.2204460492503131e-09  
I:/boost/libs/math/test/test_dirchlet_dist.cpp(77): error : in "test_main": difference{1.68492e-06} between boost::math::entropy(diri){17.674729780402942} and entropy{17.674700000000001} exceeds 2.2204460492503131e-09  
I:/boost/libs/math/test/test_dirchlet_dist.cpp(81): error : in "test_main": difference{5.74049e-09} between calc_mean[i]{0.69312878397890099} and mean[i]{0.69312878} exceeds 2.2204460492503131e-09  
I:/boost/libs/math/test/test_dirchlet_dist.cpp(82): error : in "test_main": difference{1.90732e-07} between calc_variance[i]{0.022781795654775592} and var[i]{0.022781800000000001} exceeds 2.2204460492503131e-09  
I:/boost/libs/math/test/test_dirchlet_dist.cpp(84): error : in "test_main": difference{9.79326e-09} between calc_skewness[i]{-0.4951551348491805} and skewness[i]{-0.49515513} exceeds 2.2204460492503131e-09  
I:/boost/libs/math/test/test_dirchlet_dist.cpp(81): error : in "test_main": difference{1.2966e-08} between calc_mean[i]{0.3068712160210989} and mean[i]{0.30687122} exceeds 2.2204460492503131e-09  
I:/boost/libs/math/test/test_dirchlet_dist.cpp(82): error : in "test_main": difference{1.90732e-07} between calc_variance[i]{0.022781795654775585} and var[i]{0.022781800000000001} exceeds 2.2204460492503131e-09  
I:/boost/libs/math/test/test_dirchlet_dist.cpp(84): error : in "test_main": difference{9.79322e-09} between calc_skewness[i]{0.49515513484916279} and skewness[i]{0.49515513} exceeds 2.2204460492503131e-09  
I:/boost/libs/math/test/test_dirchlet_dist.cpp(126): error : in "test_main": exception std::domain_error expected but not raised  
    
  *** 24 failures are detected in the test module "Master Test Suite"  
I:/boost/libs/math/test/test_dirchlet_dist.cpp(172): error : in "test_main": exception std::domain_error expected but not raised  
...  
```  
Don’t panic – most of these are due to too tight a tolerance.  
  
Looking very promising despite these reports.  
  
PS I don’t understand your compile errors, but try to sort out the above first?

---

## Review 50 [Commented]

> Username: NAThompson  
> Created_at: 2020-03-06 10:32:17 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/318#pullrequestreview-370225328  

📁 include/boost/math/distributions/dirichlet.hpp

```diff
  55 |+   {
  56 |+     throw invalid_argument("Size of 'concentration parameters' must be greater than 0.");
  57 |+     return false;
```

> Username: NAThompson  
> Created_at: 2020-03-06 10:32:17 UTC  
> Updated_at: 2020-03-09 17:55:24 UTC  
> Url: https://github.com/boostorg/math/pull/318#discussion_r388828166  

Do we need both a throw and a `return false`? I'd just do `throw std::invalid_argument`.

> Username: mrityunjay-tripathi  
> Created_at: 2020-03-06 11:31:55 UTC  
> Updated_at: 2020-03-09 17:55:24 UTC  
> Url: https://github.com/boostorg/math/pull/318#discussion_r388853760  

Oh yes.


---

## Review 51 [Commented]

> Username: NAThompson  
> Created_at: 2020-03-06 10:33:21 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/318#pullrequestreview-370225960  

📁 include/boost/math/distributions/dirichlet.hpp

```diff
  94 |   }
 103 |-   return std::accumulate(x.begin(), x.end(), 0.0) <= 1.0;
  95 |+   return accumulate(x.begin(), x.end(), 0.0) <= 1.0;
```

> Username: NAThompson  
> Created_at: 2020-03-06 10:33:21 UTC  
> Updated_at: 2020-03-09 17:55:24 UTC  
> Url: https://github.com/boostorg/math/pull/318#discussion_r388828662  

`0.0` parses as a double precision value, no matter what your `RealType` is. Use `RealType(0)`. Also, use `std::accumulate`, rather than `accumulate`, since you don't need argument-dependent lookup here.

> Username: mrityunjay-tripathi  
> Created_at: 2020-03-06 10:55:04 UTC  
> Updated_at: 2020-03-09 17:55:24 UTC  
> Url: https://github.com/boostorg/math/pull/318#discussion_r388838317  

hmmm...


---

## Review 52 [Commented]

> Username: NAThompson  
> Created_at: 2020-03-06 10:35:27 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/318#pullrequestreview-370227203  

📁 test/test_dirichlet_dist.cpp

```diff
 179 |+   // Checking precalculated values on scipy.
 180 |+   // https://docs.scipy.org/doc/scipy/reference/generated/scipy.stats.dirichlet.html
 181 |+   alpha[0] = static_cast<RealType>(5.778238829L); alpha[1] = static_cast<RealType>(2.55821892973L);
```

> Username: NAThompson  
> Created_at: 2020-03-06 10:35:27 UTC  
> Updated_at: 2020-03-09 17:55:24 UTC  
> Url: https://github.com/boostorg/math/pull/318#discussion_r388829635  

Have these values been computed up to long double precision? If so it doesn't look like there's enough digits there. In fact, I don't think there are enough digits even for double precision.

> Username: mrityunjay-tripathi  
> Created_at: 2020-03-06 11:31:26 UTC  
> Updated_at: 2020-03-09 17:55:24 UTC  
> Url: https://github.com/boostorg/math/pull/318#discussion_r388853553  

Scipy's gamma function doesn't support some (high precision) data types. Yes, these are not long double or double.

> Username: NAThompson  
> Created_at: 2020-03-06 13:06:14 UTC  
> Updated_at: 2020-03-09 17:55:24 UTC  
> Url: https://github.com/boostorg/math/pull/318#discussion_r388891277  

Ok, I'd try to get some more digits here, maybe via Mathematica?

> Username: NAThompson  
> Created_at: 2020-03-06 13:07:39 UTC  
> Updated_at: 2020-03-09 17:55:24 UTC  
> Url: https://github.com/boostorg/math/pull/318#discussion_r388891900  

I'd also try to find some exact values.


---

## Comment 53

> Username: pabristow  
> Created_at: 2020-03-06 11:42:32 UTC  
> Url: https://github.com/boostorg/math/pull/318#issuecomment-595731619  

I agree that the differences 1e-8ish look like using float precision?  
  
We definitely need some long double precision (output to numeric_limits<long double>max_digits10 = 18 decimal digits)   
  
But before you change them all, I suspect we should also be using this  
  
#include <boost/math/tools/test_value.hpp>  // for create_test_value and macro BOOST_MATH_TEST_VALUE.  
  
to create the right type for testing with UDT like multiprecision.  
  
For example  
  
  BOOST_CHECK_CLOSE_FRACTION(  // Check -exp(-1) ~= -0.367879450 == -1  
    lambert_w0(BOOST_MATH_TEST_VALUE(RealType, -0.36787944117144232159552377016146086744581113103176783450783680169746149574489980335714727434591964374662732527)),  
    BOOST_MATH_TEST_VALUE(RealType, -1.),  
    tolerance);  
  
It's unfortunate that we can't use Wolfram to create spot values at really high precision, but they haven't this distribution implemented yet :-(  
  
@jzmaddock Should we use BOOST_MATH_TEST_VALUE ?

---

## Comment 54

> Username: pabristow  
> Created_at: 2020-03-06 12:01:55 UTC  
> Url: https://github.com/boostorg/math/pull/318#issuecomment-595738219  

If I increase the tolerance to float, the tests all pass, but there are two tests that don't throw when expected.  
  
  alpha[0] = 1.26;  
  alpha[1] = 2.99;  
  x[0] = 0.5;  
  x[1] = 0.75; // sum(x) > 1.0  
  BOOST_MATH_CHECK_THROW(boost::math::pdf(dirichlet_distribution<V>(std::move(alpha)), x), std::domain_error);  
  
and   
  
  alpha[0] = 1.56;  
  alpha[1] = 4.00;  
  x[0] = 0.31;  
  x[1] = 0.71; // sum(x) > 1.  
  BOOST_MATH_CHECK_THROW(boost::math::cdf(dirichlet_distribution<V>(std::move(alpha)), x), std::domain_error);  
  
So almost all looking right, but imprecise tests.  Are there some parameter values that are expected to give exact results?  Beta_distribution?

---

## Comment 55

> Username: pabristow  
> Created_at: 2020-03-06 12:17:14 UTC  
> Url: https://github.com/boostorg/math/pull/318#issuecomment-595743252  

I also wonder if we can dispense with passing a zero of the right type (required in the Dark Days of C++98) `test_spots(std::vector<float>(0.0F));`  and instead pass nothing, and be explicit   
by calling ` test_spots<std::vector<float> >();` ...   (This works OK)  
  
(Since you have already upped the C++ standard version to 14, if not 17!)  
  
Incidentally, is using c++14/17 really helpful - I'd much prefer to limit it to c++11 if possible?  
  
@jzmaddock Boost.Math policy decision?  Your important views?

---

## Comment 56

> Username: jzmaddock  
> Created_at: 2020-03-06 12:58:27 UTC  
> Url: https://github.com/boostorg/math/pull/318#issuecomment-595756536  

>Incidentally, is using c++14/17 really helpful - I'd much prefer to limit it to c++11 if possible?  
  
It depends.... I think we need to mark C++03 as deprecated in the next release - that way we can start removing it from old code next year sometime (!).  
  
For new code, I'm reasonably relaxed about the compiler requirements, although it would be nice to not require stuff gratuitously.  I guess recent GCC's are C++14 by default, as is MSVC so that seems like a reasonable target unless there's something in C++17 that essential?

---

## Comment 57

> Username: jzmaddock  
> Created_at: 2020-03-06 12:59:17 UTC  
> Url: https://github.com/boostorg/math/pull/318#issuecomment-595756841  

>Should we use BOOST_MATH_TEST_VALUE ?  
  
It's probably overkill unless you have extended precision values which you're certain are correct.

---

## Comment 58

> Username: pabristow  
> Created_at: 2020-03-06 13:54:15 UTC  
> Url: https://github.com/boostorg/math/pull/318#issuecomment-595777522  

Boost has to cope with systems without ‘throwability’ (eg embedded systems, real-time, IOT…).  
  
Perhaps a vector containing a NaN?  But still thinking about this.  
  
From: Nick <notifications@github.com>  
Sent: 6 March 2020 10:32  
To: boostorg/math <math@noreply.github.com>  
Cc: Paul A. Bristow <paul@pbristow.uk>; Mention <mention@noreply.github.com>  
Subject: Re: [boostorg/math] adding dirichlet distribution (#318)  
  
  
@NAThompson commented on this pull request.  
  
________________________________  
  
In include/boost/math/distributions/dirichlet.hpp<https://github.com/boostorg/math/pull/318#discussion_r388828166>:  
  
> @@ -50,6 +50,12 @@ inline bool check_alpha(const char *function,  
  
                         const Policy &pol)  
  
 {  
  
   using RealType = typename RandomAccessContainer::value_type;  
  
+  using std::invalid_argument;  
  
+  if (alpha.size() < 1)  
  
+  {  
  
+    throw invalid_argument("Size of 'concentration parameters' must be greater than 0.");  
  
+    return false;  
  
Do we need both a throw and a return false? I'd just do throw std::invalid_argument.  
  
—  
You are receiving this because you were mentioned.  
Reply to this email directly, view it on GitHub<https://github.com/boostorg/math/pull/318?email_source=notifications&email_token=AAIG4ALL6B4RANNUS2FYCOTRGDGLDA5CNFSM4K5A75T2YY3PNVWWK3TUL52HS4DFWFIHK3DMKJSXC5LFON2FEZLWNFSXPKTDN5WW2ZLOORPWSZGOCYITBMA#pullrequestreview-370225328>, or unsubscribe<https://github.com/notifications/unsubscribe-auth/AAIG4AOCKIZ66FUWZALNSDTRGDGLDANCNFSM4K5A75TQ>.

---

## Comment 59

> Username: pabristow  
> Created_at: 2020-03-06 14:01:08 UTC  
> Url: https://github.com/boostorg/math/pull/318#issuecomment-595780315  

Sadly Dirichlet distribution in Wolfram says "Contribute this entry."  :-(

---

## Comment 60

> Username: pabristow  
> Created_at: 2020-03-06 14:52:06 UTC  
> Url: https://github.com/boostorg/math/pull/318#issuecomment-595802465  

Correction of my bad - max_digits10 should be 21 for 80-bit long double, not 17 as I said before (that's only MSVC who can't be bothered to implement 80-bit long double).  But I have yet to discover who to make SciPy output all those digits. Still digits10 = 18 would be better than only 6 decimal digit output!

---

## Comment 61

> Username: mrityunjay-tripathi  
> Created_at: 2020-03-06 17:43:29 UTC  
> Updated_at: 2020-03-06 17:44:33 UTC  
> Url: https://github.com/boostorg/math/pull/318#issuecomment-595881797  

>but there are two tests that don't throw when expected.  
alpha[0] = 1.26;  
alpha[1] = 2.99;  
x[0] = 0.5;  
x[1] = 0.75; // sum(x) > 1.0  
BOOST_MATH_CHECK_THROW(boost::math::pdf(dirichlet_distribution(std::move(alpha)), x), std::domain_error);  
and  
alpha[0] = 1.56;  
alpha[1] = 4.00;  
x[0] = 0.31;  
x[1] = 0.71; // sum(x) > 1.  
BOOST_MATH_CHECK_THROW(boost::math::cdf(dirichlet_distribution(std::move(alpha)), x), std::domain_error);  
  
@pabristow: I got it why this is the case. Actually the check for sum of x just returns false if the condition is not satisfied but doesn't throw the error.  
```return accumulate(x.begin(), x.end(), 0.0) <= 1.0;``` (line 95).  
  
>Boost has to cope with systems without ‘throwability’ (eg embedded systems, real-time, IOT…).  
  
Never thought about that!  
and Thank You for your constant support :)

---

## Comment 62

> Username: mrityunjay-tripathi  
> Created_at: 2020-03-07 10:05:44 UTC  
> Url: https://github.com/boostorg/math/pull/318#issuecomment-596070169  

Why do these tests pass even if there are several errors? Am I missing something?

---

## Comment 63

> Username: pabristow  
> Created_at: 2020-03-07 10:17:28 UTC  
> Url: https://github.com/boostorg/math/pull/318#issuecomment-596071234  

I don't think that the jamfile.v2 includes dirichlet tests or examples yet .  
  
I will do this and check it out locally using the b2 build system with a variety of gcc clang and msvc versions and c++std variants too.  Job for next week.  
  
(I think that it is premature to be using the CI suite yet.  I'm only testing locally.  When pushing, include the string [CI SKIP] in the commit message will avoid triggering the long-suffering and very tired and overworked CI systems.)

---

## Comment 64

> Username: pabristow  
> Created_at: 2020-03-07 10:28:13 UTC  
> Url: https://github.com/boostorg/math/pull/318#issuecomment-596072338  

I'm also trying to use beta distribution (which works with Boost.Multiprecision) for some really high precision tests.  
  
https://github.com/bertcarnell/dirichlet/blob/7954c574e2bb1359883e8d4b810d266acc6c5a35/tests/testthat/test-dirichlet.r  
  
says  
  it("A two parameter Dirichlet is equivalent to a beta", {  
    expect_equal(ddirichlet(c(0.3, 0.7), c(2, 3)), dbeta(0.3, 2, 3))  
  
and this seems to work for PDF, but so far I get a different value for CDF.  
  
I've been playing with  
  
    //   dirichlet outputs             cdf: 0.061739999999999982  ??????????????  
    // https://www.wolframalpha.com/input/?i=betaregularized%280.3%2C+0.3%2C+0.7%29  
    // I0.3(0.3,0.7) = 0.6122328397117941119545947029213687501316859461148673  
    // alpha 0.3, beta 0.7  
    // (beta(0.3, alpha, beta) gamma(alpha + beta))/(gamma(alpha) gamma(beta))  
    // betadistribution(0.3,0.7)   
    // mean  0.3, mode = 0.7, sd = 0.324037, variance = 0.105, skew = 0.822951   
  
    // N[PDF[betadistribution(0.3,0.7), 0.3], 55] =  0.6657228773303705085295919960559028800560346898849140226  
    // N[PDF[betadistribution(2, 3), 0.3], 55] 1.764000000000000000000000000000000000000000000000000000  
    // N[CDF[betadistribution(2, 3), 0.3], 55] 0.3483000000000000000000000000000000000000000000000000000  
  
    // N[mode[betadistribution(2, 3), 0.3], 55] shows   
    // mean 0.4, mode = 0.3333, sd = 1/5 = 0.2  
    // variance = 1/25 = 0.04, skewness = 2/7 = 0.285714  
    // PDF is as expected, but not CDF whose plot shows about 0.35  
  
I'm not sure if this is feasible and I suspect my ignorance and inexpertise.  Views from mathy experts?  
  
I also would hope that one can do the equivalent of this for didchlet  
  
    // Check beta distribution round-tripping:  
    double cq = quantile(b, cdf(b, 0.3)); //   
    show_value(cq, "quantile(b, cdf(b, 0.3) = ", "\n"); // quantile(b, cdf(b, 0.3) = 0.29999999999999999  
    BOOST_ASSERT_MSG(0.3000000000000000 == cq, "round_trip failed!");  
  
  
    double qc = cdf(b, quantile(b, 0.3)); //   
    show_value(qc, "cdf(b, quantile(b, 0.3)) = ", "\n"); // cdf(b, quantile(b, 0.3)) = 0.30000000000000004  
    BOOST_ASSERT_MSG(0.3000000000000000 == qc, "round_trip failed!");  
  
How to do this for a multi distribution (in hand-waving mode ;-) )?

---

## Comment 65

> Username: mrityunjay-tripathi  
> Created_at: 2020-03-07 11:41:33 UTC  
> Updated_at: 2020-03-07 11:43:30 UTC  
> Url: https://github.com/boostorg/math/pull/318#issuecomment-596079489  

>and this seems to work for PDF, but so far I get a different value for CDF.  
  
The deduction for CDF is definitely wrong and now it really seems complicated to deduce CDF of Dirichlet distribution though I will try to work on it. Scipy also doesn't have CDF implementation, now I see why.

---

## Comment 66

> Username: mrityunjay-tripathi  
> Created_at: 2020-03-07 16:22:26 UTC  
> Url: https://github.com/boostorg/math/pull/318#issuecomment-596104862  

I got [this answer on researchgate](https://www.researchgate.net/post/What_is_the_CDF_of_Dirichlet_Distribution) about CDF of Dirichlet distribution. Since the application for which I am using Dirichlet distribution is concerned about generating random distribution mostly. Can we omit CDF for now and wait if something (approximation or efficient deductions) comes up later.

---

## Comment 67

> Username: NAThompson  
> Created_at: 2020-03-07 16:25:33 UTC  
> Url: https://github.com/boostorg/math/pull/318#issuecomment-596105135  

@mrityunjay-tripathi : Why don't you try Cross-Validated and see if you get any better answers?  
  
https://stats.stackexchange.com/

---

## Comment 68

> Username: pabristow  
> Created_at: 2020-03-07 16:39:54 UTC  
> Url: https://github.com/boostorg/math/pull/318#issuecomment-596106447  

For the record here is the helpful if negative reply.  
  
"3rd Mar, 2020  
Joachim Domsta  
The State University of Applied Sciences in Elbląg,  
Dear Mrityunjay Tripathi  
do you mean the multivariate continuous Dirichlet distribution (extended beta)?  
If yes, then do not be wondering, since then for the cpdf the formula is a very complicated combination of gamma multidimensional integrals. Moreover this is a pd concentrated on the simplex formed by points with positive coordinates summing up to 1In the case of 3D, this is concentrated on the triangle  
x+y+z=1, where x,y,z >0.  
What would you do with such function F(a,b,c) which is zero for all triples a,b,c, which sum up to value less than 1?  
"  
  
Sounds tricky, as my rusty maths quickly concluded.

---

## Review 69 [Commented]

> Username: mrityunjay-tripathi  
> Created_at: 2020-03-09 18:05:35 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/318#pullrequestreview-371411481  

📁 test/test_dirichlet_dist.cpp

```diff
  48 |-     typename RandomAccessContainer::value_type cdf,     // cdf
  46 |     typename RandomAccessContainer::value_type tol)     // Test tolerance.
  47 | {
```

> Username: mrityunjay-tripathi  
> Created_at: 2020-03-09 18:05:34 UTC  
> Updated_at: 2020-03-09 18:05:35 UTC  
> Url: https://github.com/boostorg/math/pull/318#discussion_r389868002  

I removed ```mode```, ```skewness```, ```kurtosis```, ```cdf``` because I don't have predefined values for them. I thought of adding tests first for those that we have predefined values.


---

## Review 70 [Commented]

> Username: mrityunjay-tripathi  
> Created_at: 2020-03-09 18:11:21 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/318#pullrequestreview-371415449  

📁 test/test_dirichlet_dist.cpp

```diff
 198 |+   var[1] = static_cast<RealType>(0.016749888798155716);
 199 |+   pdf = static_cast<RealType>(2.870121181949622);
 200 |+   entropy = static_cast<RealType>(-0.6347509574442718);
```

> Username: mrityunjay-tripathi  
> Created_at: 2020-03-09 18:11:21 UTC  
> Updated_at: 2020-03-09 18:18:25 UTC  
> Url: https://github.com/boostorg/math/pull/318#discussion_r389871122  

I couldn't get higher precision than this (```numpy.float64```), because gamma function in SciPy doesn't support ```numpy.float128```/```numpy.longdouble```.


---

## Review 71 [Commented]

> Username: NAThompson  
> Created_at: 2020-03-09 20:03:24 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/318#pullrequestreview-371489160  

Can’t you test kurtosis/variance with a scaling relation?  
  
Var(kX) = k^2Var(X)?

---

## Comment 72

> Username: pabristow  
> Created_at: 2020-03-10 15:39:18 UTC  
> Url: https://github.com/boostorg/math/pull/318#issuecomment-597155198  

I could generate some cpp_bin_float_50 values for this, but that is somewhat circular in that it assumes that your code is correct ;-)    
These values would also least be a check on regressions from ill-advised edits in future.  
  
Cunning-er tests like Nick's are a Good Idea.  (Past experience is that it is all to easy to code these equations wrong :-( so cross checks are Really Useful)  
We should also be able to compare the beta_distribution results ('Know Good' from comparison with Wolfram) with Dirichlet when k= 2?  
  
I've asked again about the Dirichlet CDF  
[https://stats.stackexchange.com/questions/57262/implementation-of-dirichlet-cdf/453526#453526](url)

---

## Comment 73

> Username: pabristow  
> Created_at: 2020-03-10 15:42:14 UTC  
> Url: https://github.com/boostorg/math/pull/318#issuecomment-597156853  

We also like to have some worked examples of using functions and distributions, (often lifted from somewhere else so we can compare results).  
  
Have you any examples yet?  Perhaps using with Boost.Random?  
  
I am starting on documentation.

---

## Comment 74

> Username: mrityunjay-tripathi  
> Created_at: 2020-03-10 16:42:16 UTC  
> Url: https://github.com/boostorg/math/pull/318#issuecomment-597189262  

Sorry, I couldn't get done much today because of all-day celebration of 'Holi'.   
>I could generate some cpp_bin_float_50 values for this, but that is somewhat circular in that it assumes that your code is correct ;-)  
  
@pabristow: The ```pdf```, ```mean```, ```variance```, ```mode```, ```entropy``` are producing correct results now. I have to work on ```cdf``` and recheck ```skewness``` and ```kurtosis```. :)  
>I've asked again about the Dirichlet CDF  
  
I got the [equation for CDF](https://www.researchgate.net/publication/220461721_On_numerical_calculation_of_probabilities_according_to_Dirichlet_distribution) but it's toooo big. I am trying to implement it as soon as possible.  
>We also like to have some worked examples of using functions and distributions, (often lifted from somewhere else so we can compare results).  
Have you any examples yet? Perhaps using with Boost.Random?  
  
Yes, I have but I am yet to get familiar with Boost.Random  
  
>I am starting on documentation.  
  
That's nice.

---

## Comment 75

> Username: mrityunjay-tripathi  
> Created_at: 2020-03-10 16:58:25 UTC  
> Updated_at: 2020-03-10 16:59:50 UTC  
> Url: https://github.com/boostorg/math/pull/318#issuecomment-597197884  

>We should also be able to compare the beta_distribution results ('Know Good' from comparison with Wolfram) with Dirichlet when k= 2?  
  
It seems unlikely that we will get the same results because the equation for cdf of dirichlet is much complex and has to be dealt using approximation (as far as I could get it). May be we could hard-code it for k <= 2 and use the other equation for k > 2?

---

## Comment 76

> Username: pabristow  
> Created_at: 2020-03-10 17:02:09 UTC  
> Url: https://github.com/boostorg/math/pull/318#issuecomment-597199964  

https://www.researchgate.net/publication/220461721_On_numerical_calculation_of_probabilities_according_to_Dirichlet_distribution  
  
looks intimidating to be an exercise for the student ;-)

---

## Comment 77

> Username: mrityunjay-tripathi  
> Created_at: 2020-03-18 06:07:08 UTC  
> Url: https://github.com/boostorg/math/pull/318#issuecomment-600441979  

Sorry for the delay in the implementation of the CDF function, I've almost completed it but need to change the API accordingly. I got busy with the project proposal for GSoC. The approximation for Lauricella function mentioned in [this paper](https://www.researchgate.net/publication/220461721_On_numerical_calculation_of_probabilities_according_to_Dirichlet_distribution) is elaborated [here](https://link.springer.com/article/10.1007/s10444-014-9397-5).   
Sorry that I might take another few days  :(

---

## Comment 78

> Username: pabristow  
> Created_at: 2020-03-18 09:10:34 UTC  
> Url: https://github.com/boostorg/math/pull/318#issuecomment-600506028  

Ah - that sounds extremely interesting and promising.  Approximations like these are really useful.  
I have been trying to get my head around the Gouda and Szantai paper, but I have yet to get access to the Butler and Wood paper.  Comparing with beta distribution will give some good sanity checks on your code.

---

## Comment 79

> Username: mrityunjay-tripathi  
> Created_at: 2020-03-20 12:30:34 UTC  
> Url: https://github.com/boostorg/math/pull/318#issuecomment-601675960  

I've removed std::invalid_argument almost everywhere, I will see if it is still used somewhere.  
  
Also, my Institute is closed and I am on my way to home due to this whole Corona thing. I too can't access them right now, though I downloaded some of the papers (if it's that what you want) and can send you if you say.

---

## Comment 80

> Username: pabristow  
> Created_at: 2020-03-20 15:04:19 UTC  
> Url: https://github.com/boostorg/math/pull/318#issuecomment-601746712  

I've searched better and found the article, but not even partially digested it.  
  
I'm still struggling to re-understand how the error_handling works - don't worry about it for now.  Getting the cdf to work would be excellent.  
  
I've download lauricella.zip from http://faculty.smu.edu/rbutler  
Ronald Butler  C.F. Frensley Professor of Mathematical Sciences Department of Statistical Science  
Southern Methodist University Dallas  
  
contains a pdf for Laplace Approximation of Lauricella Functions  FA, FD, and Φ2  
  
lauricella_NM2.pdf contains a section on 3.1.1 explaining how the there is an error in the Exton paper (equation 10)  
  
We may be able to get help from Andrew Wood at Nottingham, UK.  This is above my 'pay grade' ;-)  
  
http://www.smu.edu/statistics/faculty/butler.html is is 404, but the download above has some .m programs that might be useful?  
  
HTH  
  
Good luck avoiding the virus - at least you are not in the Death Age Zone!

---

## Comment 81

> Username: evanmiller  
> Created_at: 2020-06-19 01:58:33 UTC  
> Url: https://github.com/boostorg/math/pull/318#issuecomment-646392298  

For test values, [Selected Tables in Mathematical Statistics, Volume IV (1977)](https://books.google.com/books?id=SIoaNt7WoGQC&pg=PP1&lpg=PP1&dq=selected+tables+in+mathematical+statistics+volume+4&source=bl&ots=iptkABzEes&sig=ACfU3U3lv6kHZ2L-cr31YrCmxr_0lEJscg&hl=en&sa=X&ved=2ahUKEwiGgpXa4YzqAhWDTN8KHZqtCJ4Q6AEwBXoECAcQAQ#v=onepage&q&f=false) has 200 pages of numerical tables dedicated to the Dirichlet distribution's CDF, inverse, mean, and variance. The book also includes a lengthy exposition on how to compute them.

---

## Comment 82

> Username: pabristow  
> Created_at: 2020-06-19 07:44:37 UTC  
> Url: https://github.com/boostorg/math/pull/318#issuecomment-646491574  

Thanks for this.  Covid-19 and other things have pushed the Dirichlet onto the back burner, but this looks a very useful reference.  Library loans are not functionally at present, but I hope to return to this in due course.

---
