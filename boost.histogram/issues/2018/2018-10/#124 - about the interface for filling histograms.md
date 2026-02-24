# #124 - about the interface for filling histograms [Closed]

> Username: fwyzard  
> Created at: 2018-10-22 14:55:01 UTC  
> Updated at: 2018-11-06 09:07:08 UTC  
> Closed at: 2018-11-06 09:07:08 UTC  
> Url: https://github.com/boostorg/histogram/issues/124  

Hi @HDembinski,  
first of all I would like to compliment and applaud your work to write a modern C++ histogram library.  
I have run into it during the review for inclusion into Boost; I've passed some comments to Mateusz, but I would like to raise one issue here.  
  
I found the interface for filling a histogram rather not intuitive.  
If I read the documentation correctly, the interface with an event loop can be summarised as  
```  
auto h = boost::histogram::make_static_histogram(...);  
for (auto const & event: data)  
    h(event.weight, event.value);  
```  
  
I am not aware of any C++ or Python libraries - apart from ROOT - that support filling histograms.  
The syntax used by ROOT can be summarised as  
```  
auto h = ...;  
for (auto const & event: data)  
    h.Fill(event.value, event.weight);  
```  
  
Given that people have by now 15+ years of experience with this syntax, I would suggest using a similar approach in `boost::histrogram`, i.e.  
```  
auto h = boost::histogram::make_static_histogram(...);  
for (auto const & event: data)  
    h.fill(event.value, event.weight);  
```  
  
It is also much more in line with the way containers work - after all, everybody is used to filling a vector with `push_back`, not `operator()`.  
  
On the other hand, do you know of any other libraries or tools that use the approach you propose ?

---

## Comment 1

> Username: HDembinski  
> Created at: 2018-10-22 19:15:09 UTC  
> Url: https://github.com/boostorg/histogram/issues/124#issuecomment-431944203  

Hi Andrea,  
  
I saw your email where you raised this issue but coudn't answer yet. Anyway, I appreciate that you opened an issue about this topic, so let's discuss it here.  
  
I am a former ROOT user, so I know where you come from, but the Boost community is larger than the ROOT community and has its own conventions. There is a reason why a vector is not using `operator()`, while it is right for histogram. You cannot see a vector in isolation, it is part of several similar containers whose interfaces have been designed to be consistent. While `std::vector` only has one unary method `push_back` to insert values, there is also `std::deque`, which has two unary methods `push_back` and `push_front` to insert values. In case of `std::deque`, it would be ambiguous if it was filled with `operator()`. Consistency with the interface of `std::deque` then dictates that `std::vector` should be filled with `push_back` as well (and to be explicit), instead of just using `operator()`.  
  
Histogram is not part of such a set of similar objects and there is only one way to fill a histogram. If there is only one way, then the equivalent of Occam's Razor for interfaces suggests that `operator()` should be used for filling. A histogram is a functor, more specifically, an aggregator. It consumes values and updates its internal state. Naturally, an aggregator uses `operator()` to do this. If that's too theoretical for you, then perhaps you find it convincing that histogram has to follow the existing example of Boost.Accumulators in that regard. Histogram is a multi-dimensional accumulator. Finally, using `operator()` makes histogram compatible with `std::for_each`. This is nice for readability and because `std::for_each` in C++17 will get the ability to parallelize code execution.

---

## Comment 2

> Username: HDembinski  
> Created at: 2018-10-22 19:20:50 UTC  
> Updated at: 2018-10-22 19:21:20 UTC  
> Url: https://github.com/boostorg/histogram/issues/124#issuecomment-431946249  

The weight comes first in histogram, because it is way easier to implement. I think it does not matter whether the weight comes first or last, as long as the code does not compile if you put the weight in the wrong place. You don't want to have the situation that you accidentally use a weight where you wanted to use a value and vice versa. Histogram forces you to mark the weight with the `boost::histogram::weight` function, so you cannot accidentally put the weight in the wrong place. The code will simply not compile if you do that.

---

## Comment 3

> Username: jpivarski  
> Created at: 2018-10-22 19:23:40 UTC  
> Url: https://github.com/boostorg/histogram/issues/124#issuecomment-431947315  

> there is only one way to fill a histogram  
  
As the Devil's advocate to Occam's razor, what about "fill with a scalar" vs "fill with an array"? Perhaps you have overloaded signatures for scalar and array arguments? (I don't see it when I search through the code...)

---

## Comment 4

> Username: HDembinski  
> Created at: 2018-10-22 19:33:22 UTC  
> Url: https://github.com/boostorg/histogram/issues/124#issuecomment-431950992  

In fact I do.

---

## Comment 5

> Username: HDembinski  
> Created at: 2018-10-22 19:35:59 UTC  
> Url: https://github.com/boostorg/histogram/issues/124#issuecomment-431951889  

When you have a 3D histogram `h`, you can do  
```  
h(1, 2, 3);  
h(std::vector<double>({1,2,3}));  
h(std::make_tuple(1, 2, 3));  
```

---

## Comment 6

> Username: jpivarski  
> Created at: 2018-10-22 21:21:08 UTC  
> Url: https://github.com/boostorg/histogram/issues/124#issuecomment-431991622  

Actually, this is not what I meant, and maybe that illustrates the issue. I meant an array representing a big, one-dimensional dataset (or a big n-dimensional dataset if it's an array of tuples, or `std::vector<std::tuple<double, double, double>>>`). I'm imagining an interface in which the loop is implicit and therefore can be internally parallelized.  
  
If the `operator()` overloading is used to distinguish among dimensionalities, then it can't also be used to distinguish between implicit and explicit loop via `value_type` vs `std::vector<value_type>`.  
  
I have nothing against making the interface as succinct as possible, but using `operator()` rather than a method name excludes the possibility of broadening the interface in this way... on an equal footing (you can always make scalar filling `operator()` while array filling is a method).

---

## Comment 7

> Username: HDembinski  
> Created at: 2018-10-23 09:34:39 UTC  
> Url: https://github.com/boostorg/histogram/issues/124#issuecomment-432173099  

Filling with arrays is not supported, because that is not proper C++.  
  
The C++ way to do this:  
```  
#include <functional>  
#include <algorithm>  
// ...  
auto h = ... // 3D histogram;  
auto x = std::vector<std::tuple<double, double, double>>>(...);  
std::for_each(x.begin(), x.end(), std::ref(h)); // fills h  
```

---

## Comment 8

> Username: HDembinski  
> Created at: 2018-10-23 09:41:18 UTC  
> Url: https://github.com/boostorg/histogram/issues/124#issuecomment-432175471  

This has the benefit that I can switch out `std::for_each` with a version that automatically parallelizes the loop (as is possible in C++17) without changing any code in my library. If I had provided methods inside my library that accept arrays, then I have to internalize the parallelization functionality provided by `std::for_each` in my library. That's bad.

---

## Comment 9

> Username: HDembinski  
> Created at: 2018-10-23 09:47:05 UTC  
> Url: https://github.com/boostorg/histogram/issues/124#issuecomment-432177524  

Really, attempts to sneak numpy idioms back into C++ are misguided and are usually propagated by people who don't know C++ very well. Array programming in Python (as useful as it is) is a crutch to get around a missing JIT compiler. In C++, the aspect of looping over a collection is a separate functionality from the nature of the collection, as it should be. The universal language between containers and algorithms in C++ are iterator ranges, not the containers themselves.

---

## Comment 10

> Username: jpivarski  
> Created at: 2018-10-23 12:46:12 UTC  
> Url: https://github.com/boostorg/histogram/issues/124#issuecomment-432231628  

Yeah, there was a misunderstanding because I'm not familiar with modern C++. (I learned it in the early 90s and have only kept up with new best practices by reading, not by experience.) The `for_each` functionality looks good.  
  
Numpy isn't the only paradigm that puts loops inside of function calls to hide the exact method used to iterate/parallelize (Spark and RDataFrame are other examples). The `for_each` method puts that in the library user's control, and sometimes control is a good thing, sometimes a bad thing.

---

## Comment 11

> Username: HDembinski  
> Created at: 2018-10-23 14:10:10 UTC  
> Updated at: 2018-10-23 14:10:34 UTC  
> Url: https://github.com/boostorg/histogram/issues/124#issuecomment-432262599  

> Yeah, there was a misunderstanding because I'm not familiar with modern C++. (I learned it in the early 90s and have only kept up with new best practices by reading, not by experience.) The for_each functionality looks good.  
  
I am still learning about the STL and proper C++ design myself, so this view that I am expressing so confidently now is actually pretty fresh. I had a `fill` method in one of the earlier versions of the library.  
  
> The for_each method puts that in the library user's control, and sometimes control is a good thing, sometimes a bad thing.  
  
C++ is a high-level language, but the C++ steering committee sees it as a low-level language in the tradition of C, where you want to give the user full control, zero-cost abstractions, and no fixed trade-offs. The STL is pretty good in achieving this by being build from many separable little pieces that can be combined in complex and powerful ways.

---

## Comment 12

> Username: veprbl  
> Created at: 2018-10-23 18:53:18 UTC  
> Url: https://github.com/boostorg/histogram/issues/124#issuecomment-432374505  

> Finally, using `operator()` makes histogram compatible with `std::for_each`. This is nice for readability and because `std::for_each` in C++17 will get the ability to parallelize code execution.  
  
I think you might be mistaking brief notation for a readable one. I don't think a person with just knowledge of C++ would be able to infer much about what a following line does  
```C++  
histogram(value)  
```  
To me, a much more readable construction would be  
```C++  
histogram.fill(value)  
```  
It follows natural language of *subject* (`histogram`) + *verb* (`fill`) + *object(s)* (`value`). To continue my argument, let's look at your example using `for_each`:  
```C++  
std::for_each(data.begin(), data.end(), h);  
```  
and compare it to  
```C++  
std::for_each(data.begin(), data.end(), [] (double x) { h.fill(x); });  
```  
Here, having to type weird C++ lambda syntax seems like a tedious process, but, in the end, it affects programmer mostly when writing code, not when reading it. And reading code is what people do most of the time when working on software. Our brain is quite very good at it, it can easily skip unessential parts like `[] (double x)` and look straight for the action `h.fill(x)`. I feel like, the `h` in the original example is just a too awkward of a representation for the action, and using it gains you no readability.  
  
My another argument against using `for_each(..., ..., h)` is that it's not stable against small changes to behaviour. Imagine that I need to now fill a histogram with not just the value of an `x`, but an `x^2` instead. I would be forced to rewrite my code with lambda if not to abandon `for_each` altogether (there may be other reasons to do so too). For histogram object `h` to be a functor may have made more sense if C++ was a functional programming language. Functional paradigms have been making their way to the C++ language and the standard library for a long time now, but so far the support for those is still very limited, from what I can tell. And if one really wants a functor, then, I think, they can always easily make one using `std::bind(bh::histogram::fill, h)` or using a lambda.

---

## Comment 13

> Username: HDembinski  
> Created at: 2018-10-24 08:01:54 UTC  
> Url: https://github.com/boostorg/histogram/issues/124#issuecomment-432553775  

> It follows natural language of subject (histogram) + verb (fill) + object(s) (value).  
  
In language, people tend to drop redundant parts of the sentence when an idiom is used often. You say "Sorry!" instead of "I am sorry!". This is ok whenever the meaning is unambiguous.  
  
It is the same here. You don't need the verb, because there is only one input action allowed for the histogram. So you don't need to spill it out every time. Again, Boost.Histogram is following established style here by the STL and Boost.Accumulators. Two libraries which had much more thought going into them than ROOT. I believe if you were more familiar with the STL, you would agree with me.

---

## Comment 14

> Username: jpivarski  
> Created at: 2018-10-24 11:17:42 UTC  
> Url: https://github.com/boostorg/histogram/issues/124#issuecomment-432615792  

Actually, the fact that you need to wrap `fill` in a lambda to use it in `for_each` did it for me. Some physicists don't have that C++ expertise and those that do might do it once or twice but eventually drop it because they're too busy to look up the syntax. I think you would end up seeing a lot of conventional for loops if it were a named method like `fill`. (And presumably the compiler is less able to recognize and optimize it, especially if other things are "conveniently" put in the same for loop.) The `for_each` with histogram as functor wouldn't have to be appreciated as the bitl of functional programming that it is, but would likely be learned as an idiom without understanding its etymology (e.g. "sorry" vs "I am sorrowed").

---

## Comment 15

> Username: HDembinski  
> Created at: 2018-10-24 13:12:54 UTC  
> Url: https://github.com/boostorg/histogram/issues/124#issuecomment-432650361  

I should trademark "Occam's Razor" for interfaces.

---

## Comment 16

> Username: jpivarski  
> Created at: 2018-10-24 13:29:40 UTC  
> Url: https://github.com/boostorg/histogram/issues/124#issuecomment-432656314  

It's a better application than most: "pluralitas non est ponenda sine necessitate," → "plurality should not be posited without necessity," though in this case you're creating abstractions rather than positing them.

---

## Comment 17

> Username: HDembinski  
> Created at: 2018-10-24 13:39:45 UTC  
> Updated at: 2018-10-24 13:40:15 UTC  
> Url: https://github.com/boostorg/histogram/issues/124#issuecomment-432660034  

You continue to amaze me with your knowledge of languages, ancient and current. :) You are right, Occam's Razor does not perfectly fit here, because we are not arguing about dropping a redundant interface, just about how to make this interface consistent with other parts of C++ and the expectations of users. Different users have different expectations.

---

## Comment 18

> Username: HDembinski  
> Created at: 2018-11-06 09:07:08 UTC  
> Url: https://github.com/boostorg/histogram/issues/124#issuecomment-436180267  

@fwyzard I use a new implementation now which makes it possible to pass the weight as the first or last parameter. The functionality is implemented in the develop branch, so I am closing this.
