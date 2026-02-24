# #696 - Any plans for a ttmath backend? [Open]

> Username: LegalizeAdulthood  
> Created at: 2025-06-17 22:34:08 UTC  
> Updated at: 2025-06-28 18:14:02 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/696  

I only stumbled across [ttmath](https://www.ttmath.org/) today.  It was mentioned in the [docs for Boost.Geometry](https://www.boost.org/doc/libs/latest/libs/geometry/doc/html/geometry/introduction.html).  
  
Maybe if there aren't any plans to implement this directly as a backend for Boost.Multiprecision it could be used as a documentation example of how to add your own backend?

---

## Comment 1

> Username: ckormanyos  
> Created at: 2025-06-27 21:16:10 UTC  
> Updated at: 2025-06-27 21:17:13 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/696#issuecomment-3014370716  

Hi Richard (@LegalizeAdulthood) this is a good idea. I've wrapped a couple of my own backends for Multiprecision.  
  
Generally, I distinguish between wrapping one for local or project-specific work as opposed to actually getting it formally approved and tested for Boost.  
  
The former is rather straghtforward. Whereby the latter can be tougher. We just finished getting `cpp_double_fp_backend` into MP and it was quite an effort. But just wrapping a backend is much, much more streamlined.  
  
You are right, we do need a real world nuts-and-bolts tutorial for this. Let's talk about this as we evolve this project.  
  
Cc: @jzmaddock and @mborland

---

## Comment 2

> Username: LegalizeAdulthood  
> Created at: 2025-06-27 21:34:20 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/696#issuecomment-3014397456  

> You are right, we do need a real world nuts-and-bolts tutorial for this. Let's talk about this as we evolve this project.  
  
That would be great; I'm giving a [presentation on Boost.Multiprecision](https://www.meetup.com/utah-cpp-programmers/events/308632008) in a couple weeks, I'll give it a go in [my example](https://github.com/LegalizeAdulthood/boost-multiprecision-example).

---

## Comment 3

> Username: ckormanyos  
> Created at: 2025-06-28 06:25:44 UTC  
> Updated at: 2025-06-28 07:36:50 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/696#issuecomment-3015018740  

Very cool. Thanks for getting into Boost and Multiprecision.  
  
So I was just looking at the new work on `cpp_double_fp_backend`. If you look at about the first $150$ lines of [`cpp_double_fp.hpp`](https://github.com/boostorg/multiprecision/blob/develop/include/boost/multiprecision/cpp_double_fp.hpp), you will see a series of functions called `eval_`whatever.  
  
Most of these are mandatory for a floating-point backend, with exceptions like `eval_exp`, `eval_log` and `eval_pow` (I did these for enhanced performance).  
  
Basically if you make your `eval_`whatever functions and get the right headers, you plug into Multiprecison and Math.  
  
You must also make two or three tuples and clarify your number category and also make a kind of _precision_ structure. The precision is near the bottom of the file. And the tuples for built-in types are near the top of the class (`signed_types` and `unsigned_types` and `float_types`). The types are needed for the internals of the backend.

---

## Comment 4

> Username: ckormanyos  
> Created at: 2025-06-28 07:30:39 UTC  
> Updated at: 2025-06-28 07:37:20 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/696#issuecomment-3015063951  

There is another completely unrelated yet interesting example in [ckormanyos/mp_cpp](https://github.com/ckormanyos/mp_cpp/tree/main). This project is real legacy and looks a little bit like `cpp_dec_float`. I worked on this as long ago as in the 90s. It's an old toy. It would not pass all of the Multiprecision testing without significant work. There is even a bug somewhere in millions of digits.  
  
Again, it is more of a toy. Over there (and also in the `wide-decimal` project), I am trying my hand at Karatsuba and Toom-Cook and FFT multiplication.  
  
I wrapped the legacy `mp_cpp` class as a Multiprecision backend [here](https://github.com/ckormanyos/mp_cpp/tree/main/boost/multiprecision).

---

## Comment 5

> Username: jzmaddock  
> Created at: 2025-06-28 15:45:56 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/696#issuecomment-3015557177  

There are (admittedly rather minimal) docs on adding a new backend here: https://www.boost.org/doc/libs/latest/libs/multiprecision/doc/html/boost_multiprecision/tut/new_backend.html  
  
More importantly is https://www.boost.org/doc/libs/latest/libs/multiprecision/test/skeleton_backend.hpp which basically does a lot of the drudgery for you and lets you "fill in the blanks".  The list of *required* functions is surprisingly short.  
  
I might be inclined to use the Intel Decimal Library as a case study as a new backend if we were going to put the work in for one.

---

## Comment 6

> Username: ckormanyos  
> Created at: 2025-06-28 17:41:30 UTC  
> Updated at: 2025-06-28 17:43:13 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/696#issuecomment-3015895542  

> More importantly is  ... [skeleton].  
  
Yes thanks John. I have used _skeleton_ on several occasions to get started. It is the right starting point.  
  
> inclined to use the Intel Decimal Library  
  
We have a couple of pending client requests for backend support. If you go through the open issus in Multiprecision, you can find them.  
  
Some of our clients want _quad_-_double_, and I think I will start on that in a few months. Then there is a package with the terse name `arb`. I think there was interest in that one too. I read an article once about a world-record-breaking calculation of the non-trivial zeros of the complex-valued Riemann-zeta function. And they used a package for interval-math there. I forgot its name.

---

## Comment 7

> Username: ckormanyos  
> Created at: 2025-06-28 17:46:01 UTC  
> Updated at: 2025-06-28 17:46:30 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/696#issuecomment-3015908858  

And don't forget to specify `std::numeric_limits` for your type. This is an interesting case, since we extend the `std`-namespace. I think it _is_ allowed, but it might be a topic of debate in some circles.

---

## Comment 8

> Username: ckormanyos  
> Created at: 2025-06-28 18:14:02 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/696#issuecomment-3015967150  

The issue for `arb` is [here](https://github.com/boostorg/multiprecision/issues/135).  
  
In addition, I think that `arb` was actually used in "The Riemann hypothesis is true up to $10^{12}$ ...". I forgot the correct citation but it was intruiging.  
  
It's interval math, so maybe not what you're looking for. But there was interest in that wrapper.
