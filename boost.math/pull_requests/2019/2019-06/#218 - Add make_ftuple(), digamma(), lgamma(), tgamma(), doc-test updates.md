# #218 Add make_ftuple(), digamma(), lgamma(), tgamma(), doc/test updates. [Merged]

> Username: pulver  
> Created at: 2019-06-16 23:46:19 UTC  
> Updated at: 2019-07-02 23:11:18 UTC  
> Merged at: 2019-06-26 00:31:48 UTC  
> Closed at: 2019-06-26 00:31:49 UTC  
> Url: https://github.com/boostorg/math/pull/218  

From https://github.com/boostorg/math/pull/176#issuecomment-485927734  
  
> It would be good to have an example of a function of 2 or more variables where only one is being differentiated. The "obvious" use of a double for the non-differentiated variable seems to work OK, but it would be nice to have this officially blessed.  
  
`black_scholes_option_price()` in the 3rd example now takes 4 template parameters, but only derivatives w.r.t. price are demonstrated.  
  
> This seems like a good example where the use of auto for returned results is a very good idea and should perhaps be encouraged in the docs?  
  
All examples now use `auto` as the type instead of the more verbose autodiff types.  
  
> It would help a lot to have an "extender manual" for functions which are better handled analytically. Something like tgamma might make a good example.  
  
The pdf manual now includes a section **Writing Functions for Autodiff Compatibility** and uses `cos()` as an example for how to define it in a way for which arbitrarily many derivatives can be accurately calculated for it using the autodiff helper functions.  
  
> It would help to know what operations are not supported - for example I tried feeding an autodiff variable into cyl_bessel_j, but that failed as one branch in the code requires a conversion from float->integer via boost::math::iround. This may have been a lost cause anyway? But I wonder if such conversions can be supported or not, as they crop up fairly commonly in special function code.  
  
Still WIP but there is this [discussion](https://github.com/pulver/autodiff/issues/17#issuecomment-477271379) which you have already responded to.

---

## Comment 1

> Username: NAThompson  
> Created_at: 2019-06-19 10:56:41 UTC  
> Url: https://github.com/boostorg/math/pull/218#issuecomment-503511698  

I just merged the fix for the travis ci that you already discovered; you might wanna do a merge commit while it's fresh in our minds what the purpose of that commit was.

---

## Comment 2

> Username: pulver  
> Created_at: 2019-06-19 13:13:34 UTC  
> Url: https://github.com/boostorg/math/pull/218#issuecomment-503555023  

> I just merged the fix for the travis ci that you already discovered; you might wanna do a merge commit while it's fresh in our minds what the purpose of that commit was.  
  
Thanks, I was actually keeping an eye on your progress and have already copied your fix to `.travis.yml`.  
  
The remaining error I'm getting in both travis and appveyor is:  
```  
../../../bin.v2/libs/math/test/test_autodiff_8.test/gcc-8/debug/link-static/threading-multi/visibility-hidden/test_autodiff_8.o: In function `boost::enable_if_c<boost::is_same<__float128, __float128>::value, boost::multiprecision::backends::cpp_bin_float<50u, (boost::multiprecision::backends::digit_base_type)10, void, int, 0, 0>&>::type boost::multiprecision::backends::cpp_bin_float<50u, (boost::multiprecision::backends::digit_base_type)10, void, int, 0, 0>::assign_float<__float128>(__float128)':  
/home/travis/build/boostorg/boost-root/libs/math/test/../../../boost/multiprecision/cpp_bin_float.hpp:238: undefined reference to `signbitq'  
/home/travis/build/boostorg/boost-root/libs/math/test/../../../boost/multiprecision/cpp_bin_float.hpp:242: undefined reference to `isnanq'  
/home/travis/build/boostorg/boost-root/libs/math/test/../../../boost/multiprecision/cpp_bin_float.hpp:249: undefined reference to `isinfq'  
/home/travis/build/boostorg/boost-root/libs/math/test/../../../boost/multiprecision/cpp_bin_float.hpp:270: undefined reference to `frexpq'  
/home/travis/build/boostorg/boost-root/libs/math/test/../../../boost/multiprecision/cpp_bin_float.hpp:273: undefined reference to `ldexpq'  
/home/travis/build/boostorg/boost-root/libs/math/test/../../../boost/multiprecision/cpp_bin_float.hpp:275: undefined reference to `truncq'  
collect2: error: ld returned 1 exit status  
```  
https://travis-ci.org/boostorg/math/jobs/547481084  
  
Do you have any advice for this?

---

## Comment 3

> Username: NAThompson  
> Created_at: 2019-06-19 13:36:26 UTC  
> Url: https://github.com/boostorg/math/pull/218#issuecomment-503563792  

I think it needs the linker flag `-lquadmath`. Here's the `Jamfile.v2` line for Gaussian quadrature which needs the same thing:  
  
```  
 [ run gauss_quadrature_test.cpp : : : <define>TEST1 [ check-target-builds ../config//has_float128 "GCC libquadmath and __float128 support" : <linkflags>-lquadmath ]  
```

---

## Comment 4

> Username: pabristow  
> Created_at: 2019-06-19 13:50:02 UTC  
> Url: https://github.com/boostorg/math/pull/218#issuecomment-503569209  

I can confirm that is needs it needs the linker flag -lquadmath, but you have probably confirmed this already yourself.

---

## Comment 5

> Username: pulver  
> Created_at: 2019-06-20 11:03:40 UTC  
> Url: https://github.com/boostorg/math/pull/218#issuecomment-503982492  

Thanks, `-lquadmath` did indeed seem to fix the problem.  
  
Once the appveyor test passes, is it ok to merge to `develop`?

---

## Comment 6

> Username: NAThompson  
> Created_at: 2019-06-20 12:40:56 UTC  
> Url: https://github.com/boostorg/math/pull/218#issuecomment-504010105  

I'm building a little app on top of this which will constitute a review; just a couple more days.

---

## Comment 7

> Username: pabristow  
> Created_at: 2019-06-20 13:35:36 UTC  
> Url: https://github.com/boostorg/math/pull/218#issuecomment-504028260  

From: pulver [mailto:notifications@github.com]   
Sent: 20 June 2019 12:04  
To: boostorg/math  
Cc: Paul A. Bristow; Comment  
Subject: Re: [boostorg/math] Add make_ftuple(), digamma(), lgamma(), tgamma(), doc/test updates. (#218)  
  
Thanks, -lquadmath did indeed seem to fix the problem.  
  
Once the appveyor test passes, is it ok to merge to develop?  
  
Looking good to me.  
  
But I’m not the boss ;-)  
  
—  
You are receiving this because you commented.  
Reply to this email directly, view it on GitHub <https://github.com/boostorg/math/pull/218?email_source=notifications&email_token=AAIG4AILMQTSSGPNHVIVQULP3NPY3A5CNFSM4HYSILDKYY3PNVWWK3TUL52HS4DFVREXG43VMVBW63LNMVXHJKTDN5WW2ZLOORPWSZGODYFCTHA#issuecomment-503982492> , or mute the thread <https://github.com/notifications/unsubscribe-auth/AAIG4AIFHLS5LL4TK4VC6VLP3NPY3ANCNFSM4HYSILDA> .https://github.com/notifications/beacon/AAIG4AN5OAVBADBT6QFR3OTP3NPY3A5CNFSM4HYSILDKYY3PNVWWK3TUL52HS4DFVREXG43VMVBW63LNMVXHJKTDN5WW2ZLOORPWSZGODYFCTHA.gif

---

## Comment 8

> Username: NAThompson  
> Created_at: 2019-06-21 11:11:25 UTC  
> Updated_at: 2019-06-21 12:40:04 UTC  
> Url: https://github.com/boostorg/math/pull/218#issuecomment-504388590  

Quick comments:  
  
- In your docs you state:  
  
> Accuracy of derivatives over finite difference methods. Finite difference methods always include a Δx free variable that must be carefully chosen for each application. If Δx is too small, then numerical errors become large. If Δx is too large, then mathematical errors become large. With autodiff, there are no free variables to set and the accuracy of the answer is generally superior to finite difference methods even with the best choice of Δx.  
  
This is incorrect; there is no free parameter. The optimal value of ∆x is found by balancing the truncation error and the error of representing the function values in floating point. The reason why automatic differentiation is better is that finite differencing recovers n/(n+1) of the available binary digits for n function evaluations, whereas automatic differentiation tends to recover all the available binary digits in a single evaluation.  
  
- In the generated `autodiff.html`, your synopsis section includes code from the `detail` namespace. The synopsis should only show the user what they are interested in, and the `detail` namespace is precisely the opposite of this.  
  
- I hope I'm not rehashing old ground, but is there any chance that I can get a `make_active` alias to `make_fvar`? That would then match Griewank's language.  
  
- Could we have an example of how to use `make_ftuple`? I only see a statement about how it should be used rather than code.  
  
- I don't see any `make_ftuple` unit tests:  
  
```bash  
➜  math git:(autodiff) ✗ grep -nr 'make_ftuple' test  
```  
  
- I don't know why, but I felt like this should've worked:  
  
```cpp  
std::array<double, 2> initial_conditions;  
auto ics = boost::math::differentiation::make_ftuple(initial_conditions);  
```  
  
Would it be sensible to add an overload for this? Also, an initializer list?  
  
```cpp  
auto ics = boost::math::differentiation::make_ftuple({a, b, c});  
```  
  
  
Just so you know what I'm trying to accomplish while running into this stuff: I'm trying to implement algorithm 13.2 of Corless's A Graduate Introduction to Numerical Methods on top of your autodiff package. The idea is that you want to solve x' = f by writing x(t+h) = x(t) + hx'(t) + h^2/2 x''(t), but you know x'(t) = f, and x''(t) = f'. So if you can autodiff f you can get the Taylor series out to really high order cheaply and then you have a fast ODE stepper and you get an interpolator for free. And it's great for forward-mode autodiff since f:R^{n+1}->R^{n}.

---

## Comment 9

> Username: pulver  
> Created_at: 2019-06-21 14:03:06 UTC  
> Updated_at: 2019-06-21 14:06:20 UTC  
> Url: https://github.com/boostorg/math/pull/218#issuecomment-504437507  

Thank you for the thoughtful feedback.  
  
> > Accuracy of derivatives over finite difference methods. Finite difference methods always include a Δx free variable that must be carefully chosen for each application. If Δx is too small, then numerical errors become large. If Δx is too large, then mathematical errors become large. With autodiff, there are no free variables to set and the accuracy of the answer is generally superior to finite difference methods even with the best choice of Δx.  
>   
> This is incorrect; there is no free parameter. The optimal value of ∆x is found by balancing the truncation error and the error of representing the function values in floating point. The reason why automatic differentiation is better is that finite differencing recovers n/(n+1) of the available binary digits for n function evaluations, whereas automatic differentiation tends to recover all the available binary digits in a single evaluation.  
  
What I am calling a free variable, you are describing one algorithm for finding. If the evaluation of *f(x)* is expensive then people may settle upon a hard-coded guesstimated value for *Δx* (I've worked in a financial firm that did this regularly for calculating option greeks quickly - which is what motived me to develop an AD library.)  
  
To your point, I'll update the documentation to call it "Single-iteration finite difference method" to distinguish it from a more methodical finite difference method you are describing. Better descriptions are welcome.  
  
> In the generated `autodiff.html`, your synopsis section includes code from the `detail` namespace. The synopsis should only show the user what they are interested in, and the `detail` namespace is precisely the opposite of this.  
  
How do you recommend displaying the overloaded arithmetic operators for the `detail::fvar<>` class template? Or are you saying that is not necessary?  
  
>   
> I hope I'm not rehashing old ground, but is there any chance that I can get a `make_active` alias to `make_fvar`? That would then match Griewank's language.  
  
I don't find that very appealing, for a few reasons, in order of most important to least:  
  
* `make_fvar` was chosen in anticipation of a future reverse AD library in which a corresponding `make_rvar` will be used.  
* `make_active` suggests that a `make_passive` function may exist. Instead, passive variables are created with the single-parameter `fvar<>` constructors. This fits in nicely when the return type of a function is of type `fvar<>` but includes branches that return "constants", e.g. `return 1.0;`  
* I don't consider Griewank & Walther or their book to be an authoritative reference on AD. Instead, we already have terms directly from mathematics to describe these concepts. Namely, variables of differentiation, or constants, and I haven't encountered the need increase the lexicon to accommodate them.  
  
> Could we have an example of how to use `make_ftuple`? I only see a statement about how it should be used rather than code.  
  
The documentation should reference the 2nd example for usage:  
```  
  auto const variables = make_ftuple<float50, Nw, Nx, Ny, Nz>(11, 12, 13, 14);  
```  
  
> I don't see any `make_ftuple` unit tests:  
>   
> ```shell  
> ➜  math git:(autodiff) ✗ grep -nr 'make_ftuple' test  
> ```  
  
Do you mind if I add that later on after this PR is merged? In the meantime, it is auto-compiled in three of the examples:  
* `example/autodiff_black_scholes.cpp`  
* `example/autodiff_mixed_partials.cpp`  
* `example/autodiff_multiprecision.cpp`  
  
> I don't know why, but I felt like this should've worked:  
>   
> ```c++  
> std::array<double, 2> initial_conditions;  
> auto ics = boost::math::differentiation::make_ftuple(initial_conditions);  
> ```  
  
Try  
```c++  
auto ics = make_ftuple<double,N0,N1>(ic0, ic1);  
```  
where `N0` and `N1` are the highest derivative orders to be calculated for `ic0` and `ic1` respectively.  
  
> Would it be sensible to add an overload for this? Also, an initializer list?  
>   
> ```c++  
> auto ics = boost::math::differentiation::make_ftuple({a, b, c});  
> ```  
  
If that were done, then the same should also be done for `fvar::derivative()`. I'd like to see an instance where that would significantly simplify the code before adding this onto the API. If you are presently working on one such instance, then I'd be happy to look at it when it's done.

---

## Comment 10

> Username: NAThompson  
> Created_at: 2019-06-21 21:51:51 UTC  
> Url: https://github.com/boostorg/math/pull/218#issuecomment-504584284  

Quick question: Do you have optimal complexity vis a vis the cheap gradients theorem discussed in Griewank's [history of AD](https://www.math.uni-bielefeld.de/documenta/vol-ismp/52_griewank-andreas-b.pdf)?

---

## Comment 11

> Username: pulver  
> Created_at: 2019-06-22 13:18:58 UTC  
> Url: https://github.com/boostorg/math/pull/218#issuecomment-504665256  

> Do you have optimal complexity vis a vis the cheap gradients theorem discussed in Griewank's history of AD?  
  
Unlikely. If there are n independent variables for which one is calculating up to their first derivatives, the nth variable is represented in autodiff by a 2^n-element tensor.

---

## Comment 12

> Username: NAThompson  
> Created_at: 2019-06-22 15:46:28 UTC  
> Updated_at: 2019-06-22 15:48:13 UTC  
> Url: https://github.com/boostorg/math/pull/218#issuecomment-504676352  

The comment about single-iteration finite differencing is still not correct. The optimal stepsize can be chosen independently of the function. See [here](https://web.archive.org/web/20150420195907/http://www.uio.no/studier/emner/matnat/math/MAT-INF1100/h08/kompendiet/diffint.pdf).

---

## Comment 13

> Username: NAThompson  
> Created_at: 2019-06-22 15:59:44 UTC  
> Updated_at: 2019-06-22 16:03:19 UTC  
> Url: https://github.com/boostorg/math/pull/218#issuecomment-504677392  

Ok, I just created a [repo](https://github.com/NAThompson/taylor_ode) trying to implement Taylor series methods to solve ODEs using `autodiff`.  
  
Here's the problems I encountered:  
  
- I wanted to support arbitrary dimensions, but since I can't call the `make_ftuple` with a `std::array`, I had to restrict to two dimensions:  
  
```  
auto ics = boost::math::differentiation::make_ftuple<Real, N, N>(initial_conditions[0], initial_conditions[1]);  
```  
  
So how can I initialize an `autodiff::ftuple` with an arbitrarily long list of arguments?  
  
Also, can I say "differentiate all arguments the same number of times"?

---

## Comment 14

> Username: pulver  
> Created_at: 2019-06-22 17:07:08 UTC  
> Url: https://github.com/boostorg/math/pull/218#issuecomment-504682488  

> The comment about single-iteration finite differencing is still not correct. The optimal stepsize can be chosen independently of the function.  
  
To help illustrate your point, what the optimal step size for *f(x)=x^n* independently of *x* and *n*?

---

## Comment 15

> Username: NAThompson  
> Created_at: 2019-06-22 17:15:51 UTC  
> Updated_at: 2019-06-22 17:18:07 UTC  
> Url: https://github.com/boostorg/math/pull/218#issuecomment-504683164  

>  To help illustrate your point, what the optimal step size for f(x)=x^n independently of x and n?  
  
The stepsize is `2*sqrt(eps/2)`, for a second order accurate differencing. This is shown in figure 11.1 of [the link](https://web.archive.org/web/20150420195907/http://www.uio.no/studier/emner/matnat/math/MAT-INF1100/h08/kompendiet/diffint.pdf).  
  
For higher order finite differences, you can repeat the same procedure to get slightly different steps, but the idea is similar.

---

## Comment 16

> Username: pulver  
> Created_at: 2019-06-22 17:21:35 UTC  
> Url: https://github.com/boostorg/math/pull/218#issuecomment-504683609  

> Ok, I just created a [repo](https://github.com/NAThompson/taylor_ode) trying to implement Taylor series methods to solve ODEs using `autodiff`.  
>   
> Here's the problems I encountered:  
>   
>     * I wanted to support arbitrary dimensions, but since I can't call the `make_ftuple` with a `std::array`, I had to restrict to two dimensions:  
>   
>   
> ```  
> auto ics = boost::math::differentiation::make_ftuple<Real, N, N>(initial_conditions[0], initial_conditions[1]);  
> ```  
>   
> So how can I initialize an `autodiff::ftuple` with an arbitrarily long list of arguments?  
  
I don't see a way either, if the length of the list is to be determined at run-time. It is a deliberate design decision for some aspects of autodiff to be chosen at compile-time. A benefit of this is stack-only memory usage.  
  
Having said that, if you can determine an upper limit to the number of independent variables and their derivative orders at compile-time, then you can select subsets of them which can be determined at run-time.  
  
Note that this restriction is not all that different from `std::make_tuple` which also does not accept arrays.  
  
I'm happy to work on a feature request in this regard. I don't think there is enough time to get in this new feature before Wednesday's deadline.

---

## Comment 17

> Username: pulver  
> Created_at: 2019-06-22 17:22:36 UTC  
> Updated_at: 2019-06-23 15:48:04 UTC  
> Url: https://github.com/boostorg/math/pull/218#issuecomment-504683679  

> > To help illustrate your point, what the optimal step size for f(x)=x^n independently of x and n?  
>   
> The stepsize is `2*sqrt(eps/2)`, for a second order accurate differencing. This is shown in figure 11.1 of [the link](https://web.archive.org/web/20150420195907/http://www.uio.no/studier/emner/matnat/math/MAT-INF1100/h08/kompendiet/diffint.pdf).  
>   
> For higher order finite differences, you can repeat the same procedure to get slightly different steps.  
  
Thank you for spelling that out; I see your point. ~I'll rephrase accordingly.~  
  
Skeptical about whether this is objectively the best choice. See my response below with the plot of points.

---

## Comment 18

> Username: NAThompson  
> Created_at: 2019-06-22 17:34:57 UTC  
> Url: https://github.com/boostorg/math/pull/218#issuecomment-504684615  

> I'm happy to work on a feature request in this regard. I don't think there is enough time to get in this new feature before Wednesday's deadline.  
  
Wednesday's deadline is for new libraries, not for new features. I think you'll be fine for 1.71.  
  
> I don't see a way either, if the length of the list is to be determined at run-time. It is a deliberate design decision for some aspects of autodiff to be chosen at compile-time. A benefit of this is stack-only memory usage.  
  
BTW, I'm using a `std::array` which is indeed all on the stack, and has compile-time known length.  
  
> Note that this restriction is not all that different from std::make_tuple which also does not accept arrays. I'm happy to work on a feature request in this regard.  
  
Yeah, I don't mean to spring this on you, but I do think this that Taylor methods for ODEs are a really common use case for forward-mode automatic differentiation; it really shines in this application, and `std::array`s are commonly used for this. Of course, so are `Eigen::Vector`s and `arms::vec`s, so it's really a question of how do you support all these containers generically. (Maybe take a `.begin()` and `.end()` with a templated number of arguments? Forgive me for not having a good solution here.)  
  
In any case, I think you have a legitimate claim that your code has an idiomatic api, so is there a way you can see to do what I'm attempting to do with the Taylor methods for ODEs?

---

## Comment 19

> Username: pulver  
> Created_at: 2019-06-22 17:57:22 UTC  
> Url: https://github.com/boostorg/math/pull/218#issuecomment-504686160  

> > Note that this restriction is not all that different from std::make_tuple which also does not accept arrays. I'm happy to work on a feature request in this regard.  
>   
> Yeah, I don't mean to spring this on you, but I do think this that Taylor methods for ODEs are a really common use case for forward-mode automatic differentiation; it really shines in this application, and `std::array`s are commonly used for this. Of course, so are `Eigen::Vector`s and `arms::vec`s, so it's really a question of how do you support all these containers generically. (Maybe take a `.begin()` and `.end()` with a templated number of arguments? Forgive me for not having a good solution here.)  
>   
> In any case, I think you have a legitimate claim that your code has an idiomatic api, so is there a way you can see to do what I'm attempting to do with the Taylor methods for ODEs?  
  
It's not clear to me the problem you are trying to solve (I re-read your explanation of the problem but it's not clear as to what is given and what is not. Maybe an explicit example might help.)  
  
Here's some discussion points that can be used to seed a new feature request:  
  
* What should the instantiation look like? E.g.  
`auto init = std::array<double,N>{{10,11,12,13}};`  
`auto vars = make_ftuple_from_array<M>(init);`  
where `M` is the compile-time maximum order derivative to be calculated for all `N` variables. Or do they each need to be independently specified?  
* What should retrieval of derivatives look like? E.g.  
`auto orders = std::array<unsigned,N>(1,2,0,1); // each value must not exceed M`  
`double d = z.derivative(orders);`  
  
Given that this will need to be made backward-compatible with C++11 and tests need to be written, and my general lack of time, I cannot make any promises for a delivery date.

---

## Comment 20

> Username: NAThompson  
> Created_at: 2019-06-22 18:03:56 UTC  
> Updated_at: 2019-06-22 18:09:06 UTC  
> Url: https://github.com/boostorg/math/pull/218#issuecomment-504686612  

> It's not clear to me the problem you are trying to solve (I re-read your explanation of the problem but it's not clear as to what is given and what is not. Maybe an explicit example might help.)  
  
Do you have a library which can get you electronic access to Corless's [A Graduate Introduction to Numerical Methods](https://www.amazon.com/Graduate-Introduction-Numerical-Methods-Viewpoint-ebook/dp/B00H5IOGR8/)? I ask because the section on Taylor methods for ODEs is incredibly well-written, and I cannot really explain it better. In terms of how I'm implementing it, I'm making it look as much as possible like Boost.ODEInt.  
  
> What should the instantiation look like? E.g.  
auto init = std::array<double,N>{{10,11,12,13}};  
auto vars = make_ftuple_from_array<M>(init);  
where M is the compile-time maximum order derivative to be calculated for all N variables. Or do they each need to be independently specified?  
  
In general people think of C^k functions, not C^{k_1} in argument 1, C^{k_2} in argument 2, so on. So I think it's safe to have M be the compile-time maximum order derivative.

---

## Comment 21

> Username: NAThompson  
> Created_at: 2019-06-22 18:24:42 UTC  
> Url: https://github.com/boostorg/math/pull/218#issuecomment-504688134  

> Given that this will need to be made backward-compatible with C++11 and tests need to be written, and my general lack of time, I cannot make any promises for a delivery date.  
  
I wouldn't worry about delivery dates, I would worry about no one caring. If the API is ergonomic, people will care, and otherwise, they won't. (I mean, periodically PRs do languish, in which case you have to light a fire under someone to get things going, but it's certainly not the case with this one: I want this feature! And I'm gonna make sure it crosses the finish line.)

---

## Comment 22

> Username: pulver  
> Created_at: 2019-06-22 18:38:05 UTC  
> Url: https://github.com/boostorg/math/pull/218#issuecomment-504689058  

> > Given that this will need to be made backward-compatible with C++11 and tests need to be written, and my general lack of time, I cannot make any promises for a delivery date.  
>   
> I wouldn't worry about delivery dates, I would worry about no one caring. If the API is ergonomic, people will care, and otherwise, they won't. (I mean, periodically PRs do languish, in which case you have to light a fire under someone to get things going, but it's certainly not the case with this one: I want this feature! And I'm gonna make sure it crosses the finish line.)  
  
Hopefully we can do things in this order:  
  
1. Merge present features from `autodiff` branch to `develop` -> `master`.  
2. Add the new feature.  
  
Or are you insisting this new feature is added as part of this PR?

---

## Comment 23

> Username: NAThompson  
> Created_at: 2019-06-22 18:41:43 UTC  
> Url: https://github.com/boostorg/math/pull/218#issuecomment-504689325  

> Or are you insisting this new feature is added as part of this PR?  
  
No, not at all. I think this is a nice improvement to the feature set, so I'll go ahead and merge it once we get out of this build hell we're in. (You've probably noticed that Boost.Math is ddos'ing appveyor.)  
  
But I do think getting the higher-dimensional stuff more ergonomic would be really cool; but do it at your leisure.

---

## Comment 24

> Username: pulver  
> Created_at: 2019-06-23 15:39:41 UTC  
> Url: https://github.com/boostorg/math/pull/218#issuecomment-504762946  

> > To help illustrate your point, what the optimal step size for f(x)=x^n independently of x and n?  
>   
> The stepsize is `2*sqrt(eps/2)`, for a second order accurate differencing. This is shown in figure 11.1 of [the link](https://web.archive.org/web/20150420195907/http://www.uio.no/studier/emner/matnat/math/MAT-INF1100/h08/kompendiet/diffint.pdf).  
>   
> For higher order finite differences, you can repeat the same procedure to get slightly different steps, but the idea is similar.  
  
Using a stepsize of `2*sqrt(eps/2)` is may be an appropriate choice when nothing else is known about a function or value at which a derivative is to be approximated, but it is not the best choice in all cases. Assuming `using Real=double`, here is an example for *f(x)=x^4* at *x=1000* in which a better step size is `5e-6` than the prescribed `2.1e-8=2*sqrt(eps/2)`.  
  
The following C++ program generates a list of `dx,error` pairs, where  
* `dx` = stepsize used in the evaluation of `slope = ( f(x+dx) - f(x) ) / dx`  
* `error` = `slope - derivative` where `derivative = 4*1000*1000*1000` (known derivative at x=1000).  
  
```c++  
#include <cmath>  
#include <iostream>  
#include <limits>  
  
template <typename T>  
T f(T const& x) {  
  T retval = x * x;  
  retval *= retval;  
  return retval;  
}  
  
int main() {  
  using Real = double;  
  using std::sqrt;  
  Real const delta_best = 2*sqrt(std::numeric_limits<Real>::epsilon()/2);  
  constexpr Real x0 = 1000;  
  constexpr Real derivative = 4*x0*x0*x0;  
  Real const upper_bound = 5e2*delta_best;  
  Real const ddx = upper_bound/1001;  
  for (Real dx=ddx ; dx<=upper_bound ; dx+=ddx) {  
    Real slope = ( f(x0+dx) - f(x0) ) / dx;  
    Real error = slope - derivative;  
    std::cout << dx << ',' << error << '\n';  
  }  
  std::cerr << "Best delta = " << delta_best << '\n';  
  return 0;  
}  
```  
The generated 1000 data points are plotted here:  
  
![plot](https://user-images.githubusercontent.com/39707/59978113-e58f6300-95a6-11e9-97ea-43fed2cdf84c.png)  
  
The stepsize of `2*sqrt(eps/2)=2.1e-8` is at about the left-most 1/500th on the plot. In contrast, one can estimate that the real "best" choice is near the middle, at about `5e-6`. How do these compare in practice? Here is a program that compares these two values:  
  
```c++  
#include <cmath>  
#include <iomanip>  
#include <iostream>  
#include <limits>  
  
template <typename T>  
T f(T const& x) {  
  T retval = x * x;  
  retval *= retval;  
  return retval;  
}  
  
// Avg derivative over 10 points witihin 0.01% of delta  
template <typename T>  
T avg_derivative(T const& x, T const& delta) {  
  constexpr int npoints = 10;  
  constexpr T error_range = 0.00001;  
  T const lower_bound = delta;  
  T const upper_bound = (1+error_range) * delta;  
  T const dx = error_range * delta / npoints;  
  T delta_var = lower_bound;  
  T sum = 0;  
  for (int i=0 ; i<npoints ; ++i, delta_var+=dx) {  
    T const derivative = ( f(x+delta_var) - f(x) ) / delta_var;  
    sum += derivative;  
  }  
  return sum / npoints;  
}  
  
int main() {  
  using Real = double;  
  using std::sqrt;  
  Real const x = 1000.0;  
  Real const delta0 = 2*sqrt(std::numeric_limits<Real>::epsilon()/2);  
  Real const delta1 = 5e-6;  
  std::cout << "delta0:\t avg_derivative("<<x<<','<<delta0<<") = "  
    << std::setprecision(20) << avg_derivative(x,delta0)<<'\n';  
  std::cout.precision(6);  
  std::cout << "delta1:\t avg_derivative("<<x<<','<<delta1<<") = "  
    << std::setprecision(20) << avg_derivative(x,delta1)<<'\n';  
  return 0;  
}  
```  
Output:  
```  
delta0:	 avg_derivative(1000,2.10734e-08) = 3999998416.2864022255  
delta1:	 avg_derivative(1000,5e-06) = 4000000027.3436441422  
```  
Using the stepsize of `2*sqrt(eps/2)=2.1e-8` gives a finite difference approximation that is off by `1583.7`, vs using a stepsize of 5e-6 which is off by `27.3`, based on an average of 10 measurements.  
  
There is a bit more to the story, involving floating point error and (avg,stddev) of results, etc. but the main point is that there no objective "best" formula for selecting a stepsize that works in all cases, or at least one has not yet been presented. In lieu of this I think the present phrasing in which the stepsize is effectively a free variable that must be carefully chosen is still appropriate. Just because one has chosen a particular formula for it doesn't change the fact that a choice was made - and not necessarily the best choice either.

---

## Comment 25

> Username: NAThompson  
> Created_at: 2019-06-23 16:11:55 UTC  
> Updated_at: 2019-06-23 16:14:53 UTC  
> Url: https://github.com/boostorg/math/pull/218#issuecomment-504765744  

You are arguing against a claim that is not being made. The claim is that there is an optimal stepsize that recovers a given *order of accuracy*, not that this stepsize is the same as would be found by exhaustive search with an available oracle. Your graph is consistent with this claim.  
  
In any case, you've helped your cause by using second order accurate differencing. Try the default version in `numerical_differentiation.hpp` (which is sixth order accurate) and see if you still feel like this claim is still "morally incorrect", as the mathematicians say.

---

## Comment 26

> Username: pulver  
> Created_at: 2019-06-23 17:16:21 UTC  
> Updated_at: 2019-06-23 17:21:11 UTC  
> Url: https://github.com/boostorg/math/pull/218#issuecomment-504770541  

> You are arguing against a claim that is not being made.  
  
I'm arguing quite specifically against:  
  
> The comment about single-iteration finite differencing is still not correct.  
  
The comment you are saying is incorrect is:  
  
> Single-iteration finite difference methods always include a Δx free variable that must be carefully chosen for each application.  
  
Your response that "Try the default version in `numerical_differentiation.hpp` (which is sixth order accurate) and see if you still feel like this claim is still "morally incorrect", as the mathematicians say" proves my point. You are suggesting better ways to choose Δx. This is one problem with finite difference methods that AD solves - you don't need to consider all the various ways to find Δx, weighing things like speed against accuracy, for instance.  
  
> The claim is that there is an optimal stepsize that recovers a given _order of accuracy_, not that this stepsize is the same as would be found by exhaustive search with an available oracle. Your graph is consistent with this claim.  
  
Ok - no disagreement there.  
  
Sounds to me like we are feeling [different parts of the same elephant](https://en.wikipedia.org/wiki/Blind_men_and_an_elephant), but telling me that my description of the elephant is incorrect.  
  
It seems we agree that there are different ways to calculate/choose Δx for a given application. It seems you have various tools/methods for calculating Δx, and each method has their own costs/benefits. Is it always clear which method/value is best to use for any given application? No, I don't think it is. It is to be carefully chosen.  
  
The point I am making is that this choice need not be made with AD, and the results of AD are generally more accurate than the best choice of Δx via finite difference methods.

---

## Comment 27

> Username: pulver  
> Created_at: 2019-06-24 13:07:38 UTC  
> Updated_at: 2019-06-24 13:29:42 UTC  
> Url: https://github.com/boostorg/math/pull/218#issuecomment-505002008  

Now that both CI tests are passing, I'll go ahead and merge (and squash) to `develop` branch at Wednesday 00:00:00 GMT, provided there are no objections to the current state of the code.  
  
Any doc issues can be merged separately with `[SKIP CI]`.

---

## Comment 28

> Username: pabristow  
> Created_at: 2019-06-24 14:23:16 UTC  
> Url: https://github.com/boostorg/math/pull/218#issuecomment-505032389  

Fine by me, asap.

---

## Review 29 [Approved]

> Username: NAThompson  
> Created_at: 2019-06-25 11:57:38 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/math/pull/218#pullrequestreview-253951680  

📁 include/boost/math/differentiation/autodiff.hpp

```diff
   9 |- #include <boost/config.hpp>
   9 |+ #include <boost/cstdfloat.hpp>
  10 |+ #include <boost/lexical_cast.hpp>
```

> Username: NAThompson  
> Created_at: 2019-06-25 11:55:27 UTC  
> Updated_at: 2019-06-25 11:57:38 UTC  
> Url: https://github.com/boostorg/math/pull/218#discussion_r297147899  

Is lexical_cast.hpp absolutely necessary here? I ask because if you run `#include <boost/lexical_cast.hpp>` you get an additional 410 header dependencies! (But I guess you already checked that `static_casts` won't work?)

> Username: pulver  
> Created_at: 2019-06-25 12:34:47 UTC  
> Updated_at: 2019-06-25 12:36:04 UTC  
> Url: https://github.com/boostorg/math/pull/218#discussion_r297162902  

There are a number of instances, but to take one example, `boost::math::tgamma1pm1(x)` at some point calls `boost::lexical_cast<Real>(c)` where `Real=autodiff_fvar<>`. Adding an `explicit fvar(char const* ca)` constructor which calls `boost::lexical_cast(ca)` seemed to fix it, but perhaps there is a better way?

> Username: NAThompson  
> Created_at: 2019-06-25 12:44:04 UTC  
> Updated_at: 2019-06-25 12:44:05 UTC  
> Url: https://github.com/boostorg/math/pull/218#discussion_r297166823  

Ok, if it's already being pulled in then there's no additional compilation cost.

> Username: kedarbhat  
> Created_at: 2019-06-26 00:04:39 UTC  
> Updated_at: 2019-06-26 00:04:40 UTC  
> Url: https://github.com/boostorg/math/pull/218#discussion_r297440402  

Sorry, I meant to get to this earlier today, but Matthew's answered the question.  
  
For a little more color, I looked back at the PR which first included this constructor (https://github.com/pulver/autodiff/pull/11); Matthew asked me for the reason for the new constructor, and below is the explanation.   
  
"During static initialization, `unchecked_factorial` (actually `unchecked_factorial_imp`) calls `boost::math::tools::convert_from_string<T>(const char*)`, which returns either `const char*` or type `T` (depending on whether `T` is constructible from `const char*`). Without the constructor, we'll need a way to have `boost::lexical_cast` (called by `boost::math::tools::convert_from_string`) operate on the root type rather than on `autodiff_fvar` directly. The extra code in the constructor is due to `float` causing `boost::lexical_cast` to throw an error when the factorials are too large too fit within the `float` type.  
  
I agree, though: it's not an ideal situation."

> Username: pulver  
> Created_at: 2019-07-02 22:07:49 UTC  
> Url: https://github.com/boostorg/math/pull/218#discussion_r299704944  

Btw, what's the recommended way to define types `T` in order to call functions like `boost::math::tgamma1pm1(x)` which require the type `T` to work with `boost::lexical_cast<T>`?  
  
We solved it by adding the `fvar(char const*)` constructor but am looking to remove this from the API.  
  
CC: @pabristow @NAThompson @kedarbhat

> Username: NAThompson  
> Created_at: 2019-07-02 23:11:18 UTC  
> Url: https://github.com/boostorg/math/pull/218#discussion_r299720256  

I just took a look at that and it looks like `std::to_string` will work instead of lexical cast in this case. I believe that that would be a backwards incompatible change from C++03, so @jzmaddock will have to give me the thumbs up to make this change:  
  
```cpp  
➜  special_functions git:(develop) grep -nr 'lexical_cast' .  
./detail/unchecked_factorial.hpp:18:#include <boost/lexical_cast.hpp>  
./detail/polygamma.hpp:147:       return policies::raise_evaluation_error<T>(function, ("Exceeded maximum series evaluations evaluating at n = " + boost::lexical_cast<std::string>(n) + " and x = %1%").c_str(), x, pol);  
./lambert_w.hpp:187:{ // try a lexical_cast and hope for the best:  
./lambert_w.hpp:188:   return boost::lexical_cast<double>(z);  
```


---
