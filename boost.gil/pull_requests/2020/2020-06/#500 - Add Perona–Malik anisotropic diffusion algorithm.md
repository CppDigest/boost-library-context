# #500 Add Perona–Malik anisotropic diffusion algorithm [Merged]

> Username: simmplecoder  
> Created at: 2020-06-02 19:53:50 UTC  
> Updated at: 2021-01-24 17:45:58 UTC  
> Merged at: 2021-01-22 22:55:17 UTC  
> Closed at: 2021-01-22 22:55:18 UTC  
> Url: https://github.com/boostorg/gil/pull/500  

### Description  
  
Implement anisotropic diffusion as defined by Perona-Malik equation.  
  
There are quite a few variations of the solution for the PDE, so I would like to implement the algorithm in the following way:  
  
```cpp  
template <typename NablaStrategy, typename DiffusivityStrategy, typename ReductionStrategy>  
struct diffuse_strategy {   
    NablaStrategy nabla_strategy;  
    DiffusivityStrategy diffusivity_strategy;  
    ReductionStrategy reduction_strategy;  
    diffuse_strategy(<each strategy here>);  
    bool needs_padding;  
    template <typename View>  
    typename View::value_type operator()(View view, point_t current_point) {  
        auto nabla = nabla_strategy(view, current_point);  
        auto diffusivity = diffusivity_strategy(nabla);  
        return reduction_strategy(nabla, diffusivity);  
   }  
};  
```  
  
With that, we can have defines for the known ones:  
  
```cpp  
using opencv_strategy = ...;  
using classic_strategy = ...; // the one in the paper  
```  
  
Then just have  
  
```cpp  
template <typename InputView, typename OutputView, typename ComputationStrategy = opencv_strategy>  
void anisotropic_diffusion(InputView input_view, OutputView output_view, ComputationStrategy strategy, uint64_t iteration_count)  
{  
    // pad if the strategy needs padding  
    // initialize two buffers  
    // copy input into the first buffer  
   <for iteration count>  
   {  
       <for every x y>  
            <second_buffer(x, y) = strategy(first_buffer(x, y))>  
       swap(first_buffer, second_buffer);  
   }  
   copy_pixels(first_buffer, output_view);  
}  
```  
Note that kappa and delta_t (diffusion speed) will be encoded inside the strategy, thus there no need for more arguments. I believe it will be possible to call it like the following:  
  
```cpp  
anisotropic_diffusion(input, output, {kappa, delta_t}, iter_count);  
```  
  
In the worst case I will just provide an overload.  
  
### References  
  
- Scale-Space and Edge Detection Using Anisotropic Diffusion, Pietro Perona, Jitendra Malik, 1990  
- http://www.mat.unimi.it/users/naldi/perona-malik.pdf  
- http://image.diku.dk/imagecanon/material/PeronaMalik1990.pdf  
  
### Tasklist  
  
- [X] Implement the algorithm  
- [X] Add docs  
- [X] Add example  
- [X] Add test case(s)  
- [x] Ensure all CI builds pass  
- [x] Review and approve

---

## Comment 1

> Username: mloskot  
> Created_at: 2020-06-05 10:08:09 UTC  
> Url: https://github.com/boostorg/gil/pull/500#issuecomment-639384689  

@simmplecoder Is this draft or ready for review?  
  
For review, I'd suggest you to add tests which reviewers can run. The examples are useful too, but they don't verify results as well as tests.  
  
Any particular reason you added the implementation to `image_processing/numeric.hpp`?  
I'd suggest to add new `image_processing/diffusion.hpp` file.

---

## Comment 2

> Username: simmplecoder  
> Created_at: 2020-06-05 13:14:16 UTC  
> Updated_at: 2020-06-05 13:20:49 UTC  
> Url: https://github.com/boostorg/gil/pull/500#issuecomment-639472450  

@mloskot , the algorithm itself is ready, e.g. the interface is the one I want. I will deal with the CI failing tonight.  
  
About testing: it is a bit complicated. We can do simple sanity check plus some precomputed examples. The source can be matlab scripts presented by the authors, but I'm not sure about the licensing issues. Will it be okay to use the output of it given we do not own matlab commercial license (I have student one) and our users will not have the license? I could search for other sources but the one from paper would be ideal.  
  
Also, the performance is faster than what I thought. I believe it is reasonable for a single threaded CPU implementation.  
  
About the location: I'm not aware of any other diffusion algorithms that are widely used. Though I believe it is reasonable to put  there, will move it tonight.

---

## Comment 3

> Username: mloskot  
> Created_at: 2020-06-05 14:34:05 UTC  
> Url: https://github.com/boostorg/gil/pull/500#issuecomment-639534223  

> About testing: it is a bit complicated.  
> We can do simple sanity check plus some precomputed examples.  
  
Right, so we are getting to the point when our tests need to be based on comparing whole or part of output image against reference image.  
  
I knew I should have implemented the PNM ASCII support done #359 long time ago, actually, for #353 during GSoC 2019. Having this, we could simply write algorithms output to [PAM](http://netpbm.sourceforge.net/doc/pam.html) file, then we could do simple `diff` against corresponding reference file also in PAM.  
I do believe test images in human-friendly text form, without using any intermediate image format libraries, would help a lot for testing. I'll see if I can work it out over the weekend.  
  
> About the location: I'm not aware of any other diffusion algorithms that are widely used.  
  
Even for single one, I think it's a good idea reflect some categories in structure of the source files.   
Second, it helps to keep files of reasonable size, instead of just few gigantic headers which are difficult to maintain.

---

## Review 4 [Changes requested]

> Username: mloskot  
> Created_at: 2020-06-05 14:49:43 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/gil/pull/500#pullrequestreview-425358038  

First nit-picking review :-)

📁 example/anisotropic_diffusion.cpp

```diff
   1 |+ #include <boost/gil/algorithm.hpp>
```

> Username: mloskot  
> Created_at: 2020-06-05 14:35:10 UTC  
> Updated_at: 2020-08-25 19:27:01 UTC  
> Url: https://github.com/boostorg/gil/pull/500#discussion_r435963824  

Please, add licence and copyright header

---

📁 example/anisotropic_diffusion.cpp

```diff
  10 |+ #include <string>
  11 |+ #include <vector>
  12 |+ #include <type_traits>
```

> Username: mloskot  
> Created_at: 2020-06-05 14:35:39 UTC  
> Updated_at: 2020-08-25 19:27:01 UTC  
> Url: https://github.com/boostorg/gil/pull/500#discussion_r435964168  

Nitpick: [includes need to be ordered](https://github.com/boostorg/gil/wiki/Include-Directives-Order)

---

📁 example/anisotropic_diffusion.cpp

```diff
  16 |+ #include <iostream>
  17 |+ 
  18 |+ void gray_version(const std::string& input_path, const std::string& output_path, unsigned int iteration_count, unsigned int kappa)
```

> Username: mloskot  
> Created_at: 2020-06-05 14:37:54 UTC  
> Updated_at: 2020-08-25 19:27:01 UTC  
> Url: https://github.com/boostorg/gil/pull/500#discussion_r435965622  

You're welcome to [Join the East Const revolution!](https://mariusbancila.ro/blog/2018/11/23/join-the-east-const-revolution/) 😆

> Username: simmplecoder  
> Created_at: 2020-06-05 21:01:40 UTC  
> Updated_at: 2020-08-25 19:27:01 UTC  
> Url: https://github.com/boostorg/gil/pull/500#discussion_r436162557  

Joined :-)

---

📁 example/anisotropic_diffusion.cpp

```diff
  97 |+         std::cerr << "unknown colorspace option passed (did you type gray with A?)\n";
  98 |+     }
  99 |+ }
```

> Username: mloskot  
> Created_at: 2020-06-05 14:38:31 UTC  
> Updated_at: 2020-08-25 19:27:01 UTC  
> Url: https://github.com/boostorg/gil/pull/500#discussion_r435966031  

Nitpick: newline missing


📁 include/boost/gil/image_processing/numeric.hpp

```diff
 297 | 
 298 |+ namespace detail {
 299 |+ enum class direction: std::size_t {
```

> Username: mloskot  
> Created_at: 2020-06-05 14:39:11 UTC  
> Updated_at: 2020-08-25 19:27:01 UTC  
> Url: https://github.com/boostorg/gil/pull/500#discussion_r435966468  

Nitpick: `{` for types, functions, etc. apart from namespaces, should go in new line

---

📁 include/boost/gil/image_processing/numeric.hpp

```diff
 297 | 
 298 |+ namespace detail {
 299 |+ enum class direction: std::size_t {
```

> Username: mloskot  
> Created_at: 2020-06-05 14:39:47 UTC  
> Updated_at: 2020-08-25 19:27:01 UTC  
> Url: https://github.com/boostorg/gil/pull/500#discussion_r435966825  

Why `std::size_t`? Why not `unsigned int` or `std::uint32_t`?

> Username: simmplecoder  
> Created_at: 2020-06-05 20:26:15 UTC  
> Updated_at: 2020-08-25 19:27:01 UTC  
> Url: https://github.com/boostorg/gil/pull/500#discussion_r436148122  

It is an index into `std::vector`. But yes, any unsigned type will do. Though the main problem is verbosity, which I don't think I can get rid of.

> Username: mloskot  
> Created_at: 2020-06-05 21:45:44 UTC  
> Updated_at: 2020-08-25 19:27:01 UTC  
> Url: https://github.com/boostorg/gil/pull/500#discussion_r436178139  

See my https://github.com/boostorg/gil/pull/500#discussion_r436177784

---

📁 include/boost/gil/image_processing/numeric.hpp

```diff
 309 |+ 
 310 |+ template <typename OutputView, typename InputView>
 311 |+ void compute_nabla(InputView view, const std::vector<OutputView>& nabla) {
```

> Username: mloskot  
> Created_at: 2020-06-05 14:40:42 UTC  
> Updated_at: 2020-08-25 19:27:01 UTC  
> Url: https://github.com/boostorg/gil/pull/500#discussion_r435967470  

`nabla` name is cryptic, fine, but it needs a Doxygen comment on what it is.

> Username: simmplecoder  
> Created_at: 2020-06-05 20:27:21 UTC  
> Updated_at: 2020-08-25 19:27:01 UTC  
> Url: https://github.com/boostorg/gil/pull/500#discussion_r436148593  

This is not part of public namespace (it is in detail). I'll leave a normal comment saying what nabla is, and what it is in this context.

> Username: mloskot  
> Created_at: 2020-06-05 21:45:17 UTC  
> Updated_at: 2020-08-25 19:27:01 UTC  
> Url: https://github.com/boostorg/gil/pull/500#discussion_r436177981  

Okey

---

📁 include/boost/gil/image_processing/numeric.hpp

```diff
 318 |+             for (std::ptrdiff_t channel_index = 0; channel_index < input_num_channels; ++channel_index)
 319 |+             {
 320 |+                 nabla[(std::size_t)direction::north](x, y)[channel_index] = view(x, y - 1)[channel_index] - view(x, y)[channel_index];
```

> Username: mloskot  
> Created_at: 2020-06-05 14:40:57 UTC  
> Updated_at: 2020-08-25 19:27:01 UTC  
> Url: https://github.com/boostorg/gil/pull/500#discussion_r435967670  

I'd rather avoid `(std::size_t)` cast here.

> Username: simmplecoder  
> Created_at: 2020-06-05 20:33:39 UTC  
> Updated_at: 2020-08-25 19:27:01 UTC  
> Url: https://github.com/boostorg/gil/pull/500#discussion_r436151210  

It will not compile without a cast, as it is `enum class` type. I thought about omitting `class` keyword, but I'm afraid I will pay for it layer.

> Username: mloskot  
> Created_at: 2020-06-05 21:44:47 UTC  
> Updated_at: 2020-08-25 19:27:01 UTC  
> Url: https://github.com/boostorg/gil/pull/500#discussion_r436177784  

Since these are used as numbers, as indexes, and not as enumerators with e.g. `switch` statement, I'd use plain `enum`  inside structure as scope or constants within dedicated namespace:  
  
```cpp  
struct direction  
{  
    enum { north = 0, south = 1, ... }  
};  
```  
  
or  
  
```cpp  
namespace direction {  
    constexpr unsigned int north = 0;  
    constexpr unsigned int south = 1;  
}  
```

> Username: simmplecoder  
> Created_at: 2020-06-05 22:54:27 UTC  
> Updated_at: 2020-08-25 19:27:01 UTC  
> Url: https://github.com/boostorg/gil/pull/500#discussion_r436198432  

I put it inside a namespace and made it nameless enum. Feeling bad about it, but it works.

---

📁 include/boost/gil/image_processing/numeric.hpp

```diff
 337 |+     using pixel_type = typename View::value_type;
 338 |+     using channel_type = typename channel_type<View>::type;
 339 |+     BOOST_ASSERT(nablas.size() == diffusivities.size());
```

> Username: mloskot  
> Created_at: 2020-06-05 14:47:19 UTC  
> Updated_at: 2020-08-25 19:27:01 UTC  
> Url: https://github.com/boostorg/gil/pull/500#discussion_r435971715  

The Why not an exception?  
  
A while ago we had lengthy discussion on assertions vs exceptions for user-specified input, see #360, as well as on Slack [here](https://cpplang.slack.com/archives/C27KZLB0X/p1564768229242000) from Peter   
  
> you should never assert on input  
> input is not a programming error, because it's not the programmer who has supplied it

---

📁 include/boost/gil/image_processing/numeric.hpp

```diff
 338 |+     using channel_type = typename channel_type<View>::type;
 339 |+     BOOST_ASSERT(nablas.size() == diffusivities.size());
 340 |+     for (std::size_t i = 0; i < nablas.size(); ++i) {
```

> Username: mloskot  
> Created_at: 2020-06-05 14:47:30 UTC  
> Updated_at: 2020-08-25 19:27:01 UTC  
> Url: https://github.com/boostorg/gil/pull/500#discussion_r435971826  

Yup, annoying me, but please ` {` in new line

---

📁 include/boost/gil/image_processing/numeric.hpp

```diff
 369 |+     using channel_type = typename channel_type<OutputView>::type;
 370 |+ 
 371 |+     for (unsigned int i = 0; i < num_iter; ++i) {
```

> Username: mloskot  
> Created_at: 2020-06-05 14:47:54 UTC  
> Updated_at: 2020-08-25 19:27:01 UTC  
> Url: https://github.com/boostorg/gil/pull/500#discussion_r435972066  

` {` in newline

---

📁 include/boost/gil/image_processing/numeric.hpp

```diff
 376 |+                         {
 377 |+                             return gil::view(img);
 378 |+                         });
```

> Username: mloskot  
> Created_at: 2020-06-05 14:49:18 UTC  
> Updated_at: 2020-08-25 19:27:01 UTC  
> Url: https://github.com/boostorg/gil/pull/500#discussion_r435972977  

There is a bit too much blank space to the left. I'd either re-indent towards left 3 times or just do  
  
```cpp  
 std::transform(nabla_images.begin(), nabla_images.end(),  
    std::back_inserter(nabla), [](computation_image_type& img) { return gil::view(img); });  
```


---

## Comment 5

> Username: simmplecoder  
> Created_at: 2020-06-10 09:56:53 UTC  
> Updated_at: 2020-06-10 09:57:29 UTC  
> Url: https://github.com/boostorg/gil/pull/500#issuecomment-641893922  

@mloskot, after working out the equations for tests, I found that not handling the border and corner cases makes this implementation broken for sufficiently high kappa and iteration counts. I will need to handle those cases, but the code will be qutie a bit more brittle, and possibly way larger.  
  
My plan is to introduce a function that will handle the edge cases in a transparent way, but that might not be possible. I will report back once I figure it out (no more than one day should be needed).

---

## Review 6 [Changes requested]

> Username: mloskot  
> Created_at: 2020-06-16 22:43:10 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/gil/pull/500#pullrequestreview-431941555  

@simmplecoder Your last comment suggest this is still work in progress or is it ready for review?

📁 test/core/image_processing/anisotropic_diffusion.cpp

```diff
  55 |+             std::abs(after_diffusion[channel_index] - before_diffusion[channel_index]) /
  56 |+             after_diffusion[channel_index] * 100.0;
  57 |+         std::cout << percentage << ' ';
```

> Username: mloskot  
> Created_at: 2020-06-16 22:42:14 UTC  
> Updated_at: 2020-08-25 19:27:01 UTC  
> Url: https://github.com/boostorg/gil/pull/500#discussion_r441182344  

Would it be possible to wrap this `cout` and all other calls to `<iostream>`-s with `#ifdef BOOST_GIL_TEST_DEBUG` ?


---

## Review 7 [Changes requested]

> Username: mloskot  
> Created_at: 2020-06-16 22:55:10 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/gil/pull/500#pullrequestreview-431946051  

📁 test/core/image_processing/anisotropic_diffusion.cpp

```diff
  23 |+ {
  24 |+     std::mt19937 twister(seed);
  25 |+     std::uniform_int_distribution<gil::uint8_t> dist;
```

> Username: mloskot  
> Created_at: 2020-06-16 22:53:16 UTC  
> Updated_at: 2020-08-25 19:27:01 UTC  
> Url: https://github.com/boostorg/gil/pull/500#discussion_r441186072  

If you add  
```cpp  
#include "core/test_fixture.hpp"  
```  
then you should be able to use our facade on those two:  
```cpp  
gil::test::fixture::random_value<std::uint8_t> random;  
```  
If you also add this extra constructor to the `random_value`  
```cpp  
 random_value(std::uint32_t seed) : rng_(seed), uid_(range_min, range_max) {}  
```  
then, obviously, you can use  
```cpp  
gil::test::fixture::random_value<std::uint8_t> random(seed);  
```

> Username: simmplecoder  
> Created_at: 2020-08-25 19:01:45 UTC  
> Updated_at: 2020-08-25 19:27:01 UTC  
> Url: https://github.com/boostorg/gil/pull/500#discussion_r476672076  

I believe we could also change default constructor to not use `std::random_device`, as not being able to reproduce the results is not useful. It should not break any test if the stuff they are testing is correct. What do you think?

> Username: simmplecoder  
> Created_at: 2020-08-25 19:04:35 UTC  
> Updated_at: 2020-08-25 19:27:01 UTC  
> Url: https://github.com/boostorg/gil/pull/500#discussion_r476673692  

auto value = uid_(rng_);  
    BOOST_ASSERT(range_min <= value && value <= range_max);  
This assert looks useless too, as `value` comes from a distribution of `[range_min, range_max]`.

> Username: simmplecoder  
> Created_at: 2020-08-25 19:27:18 UTC  
> Url: https://github.com/boostorg/gil/pull/500#discussion_r476686021  

I have added a few changes to `random_value`, please have a look

> Username: mloskot  
> Created_at: 2020-08-25 19:45:26 UTC  
> Url: https://github.com/boostorg/gil/pull/500#discussion_r476695497  

@simmplecoder Your changes there look good to me. Thanks


📁 include/boost/gil/image_processing/diffusion.hpp

```diff
  44 |+     const auto height = view.height();
  45 |+     using namespace direction;
  46 |+     const auto zero_pixel = []() {
```

> Username: mloskot  
> Created_at: 2020-06-16 22:54:32 UTC  
> Updated_at: 2020-08-25 19:27:01 UTC  
> Url: https://github.com/boostorg/gil/pull/500#discussion_r441186443  

This lambda needs to capture `input_num_channels`

---

📁 include/boost/gil/image_processing/diffusion.hpp

```diff
  54 |+     }();
  55 |+     auto minus = [](pixel_type lhs, pixel_type rhs) {
  56 |+         for (std::ptrdiff_t i = 0; i < input_num_channels; ++i)
```

> Username: mloskot  
> Created_at: 2020-06-16 22:54:47 UTC  
> Updated_at: 2020-08-25 19:27:01 UTC  
> Url: https://github.com/boostorg/gil/pull/500#discussion_r441186528  

This lambda needs to capture `input_num_channels` too

> Username: simmplecoder  
> Created_at: 2020-06-20 20:19:11 UTC  
> Updated_at: 2020-08-25 19:27:01 UTC  
> Url: https://github.com/boostorg/gil/pull/500#discussion_r443157130  

I'm resolving the conversation because the new version uses different approach.


---

## Comment 8

> Username: simmplecoder  
> Created_at: 2020-06-18 20:25:01 UTC  
> Url: https://github.com/boostorg/gil/pull/500#issuecomment-646287526  

@mloskot , I've been on a search for a bug, and I believe I found it. The issue is that I'm using completely wrong formula there. I thought that the version that I wrote is actually an improved version of the original, but I was wrong. First of all the summation step should be sum between differences of opposing directions. Second, the flux (which is the product in the summation) step is not diffusivity by difference, but rather diffusivity by double difference. I will check this out and report back.

---

## Comment 9

> Username: simmplecoder  
> Created_at: 2020-06-20 19:28:01 UTC  
> Url: https://github.com/boostorg/gil/pull/500#issuecomment-647036462  

@mloskot , I'm a bit lost on what people call anisotropic diffusion. I implemented the one that is clearly mentioned in the book, but it still leaks luminosity heavily. I will check out opencv and if it behaves the same way, I will just drop the second test. If my implementation is wrong, I will probably put this on a backburner until the later parts of GSoC.  
  
Book I'm using: [link](http://www.sci.utah.edu/~gerig/CS7960-S2010/materials/Perona-Malik/anisotropic_diffusion-Book.pdf). The formula is on page 81 (page 9 of the pdf). I will deal with the CI failures after figure out if my formula is correct.

---

## Comment 10

> Username: mloskot  
> Created_at: 2020-06-20 20:53:38 UTC  
> Updated_at: 2020-06-20 20:54:02 UTC  
> Url: https://github.com/boostorg/gil/pull/500#issuecomment-647044264  

@simmplecoder I think OpenCV implementation is based this original paper, `http://image.diku.dk/imagecanon/material/PeronaMalik1990.pdf`, which probably is the same as the one you followed (`https://github.com/opencv/opencv/issues/8361`). Different implementations seem to solve the PDE using also different flux functions, e.g. OpenCV allows two alternatives.  
  
I will try to dig this a bit further and look at your code.  
Meanwhile, I think it is a good idea to move to other topic for a break, then get back to it with fresh view.

---

## Comment 11

> Username: simmplecoder  
> Created_at: 2020-06-25 18:56:21 UTC  
> Url: https://github.com/boostorg/gil/pull/500#issuecomment-649759013  

@mloskot , I believe the current version is the correct one according to the paper. I have updated the description though, please have a look.

---

## Comment 12

> Username: mloskot  
> Created_at: 2020-07-05 09:34:35 UTC  
> Url: https://github.com/boostorg/gil/pull/500#issuecomment-653864876  

@simmplecoder The PR is getting close to be merged, would it be possible to ensure the CI builds pass?

---

## Comment 13

> Username: simmplecoder  
> Created_at: 2020-07-06 21:30:13 UTC  
> Url: https://github.com/boostorg/gil/pull/500#issuecomment-654475542  

@mloskot , I almost finished everything. The only thing left is to cover laplace functions with tests, and adapt demo to include command line argument to choose between default, classic and matlab versions of anisotropic diffusion. Code is well tested. Even the diffusivity functions that are not part of any configuration are tested so that in case somebody uses them they are not broken.   
  
Overall this is quite a bit of code, please let me know if some parts don't make sense. I guess I'll need to write up good docs pages for this, though we will need to start from the beginning (topic - finite differences and PDEs). I guess we could do docs at the end of GSoC.

---

## Comment 14

> Username: mloskot  
> Created_at: 2020-07-11 17:42:04 UTC  
> Url: https://github.com/boostorg/gil/pull/500#issuecomment-657101614  

@simmplecoder  First, I'm terribly sorry, for the long silence again.  
I am catching up with the reviews now, but first I need to work out the merge of develop to master for Boost 1.74 release before the master closes today.

---

## Review 15 [Commented]

> Username: mloskot  
> Created_at: 2020-07-26 12:26:09 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/gil/pull/500#pullrequestreview-455359723  

📁 include/boost/gil/image_processing/diffusion.hpp

```diff
  30 |+         // C++11 doesn't seem to capture members
  31 |+         auto local_kappa = kappa;
  32 |+         static_transform(input, input, [local_kappa](channel_type value) {
```

> Username: mloskot  
> Created_at: 2020-07-26 12:26:09 UTC  
> Updated_at: 2020-08-25 19:27:01 UTC  
> Url: https://github.com/boostorg/gil/pull/500#discussion_r460521215  

If you capture `this`, then lambda will automatically capture all the members:  
  
```cpp  
 static_transform(input, input, [&this](channel_type value) {  
            value /= kappa;  
```  
  
I think it's just a syntax detail here.


---

## Review 16 [Approved]

> Username: mloskot  
> Created_at: 2020-07-26 13:14:02 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/gil/pull/500#pullrequestreview-455361688  

@simmplecoder Excellent work!  
  
This is a complex topic and, I think, you've managed to very well, despite quite a number of hurdles encountered on your way. The interface for the anisotropic diffusion is as simple as possible, yet extensible via pluggable strategies.   
  
I'm happy to accept this PR.  
  
The only aspects that could be improved is adding some code comments that could help a reader trace the calculation process and find references between the implementation and the Perona-Malik paper.  
A piece of documentation would be awesome too.  
  
------  
  
  
I think, we can assume there are no objections to merging it.  
Please, let me know if/when this is ready to merge.

📁 include/boost/gil/image_processing/diffusion.hpp

```diff
  39 |+ };
  40 |+ 
  41 |+ struct gaussian_diffusivity
```

> Username: mloskot  
> Created_at: 2020-07-26 12:51:10 UTC  
> Updated_at: 2020-08-25 19:27:01 UTC  
> Url: https://github.com/boostorg/gil/pull/500#discussion_r460523966  

Is there any particular reason why you preferred 'diffusivity' over 'conductivity'?  
  
AFAIU, those are not synonyms and my understanding is this:  
- The 'conductivity' is a property of material (an area of image).  
- The 'diffusivity' is a property of dynamic process, a rate of diffusion that depends on 'conductivity'.  
  
To me, for Perona-Malik, 'conductivity' makes better fit.

---

📁 include/boost/gil/image_processing/diffusion.hpp

```diff
 104 |+     return {point_t{-1, -1}, point_t{0, -1}, point_t{+1, -1}, point_t{+1, 0},
 105 |+             point_t{+1, +1}, point_t{0, +1}, point_t{-1, +1}, point_t{-1, 0}};
 106 |+ }
```

> Username: mloskot  
> Created_at: 2020-07-26 12:52:54 UTC  
> Updated_at: 2020-08-25 19:27:01 UTC  
> Url: https://github.com/boostorg/gil/pull/500#discussion_r460524162  

This could be constexpr, I think,

> Username: simmplecoder  
> Created_at: 2020-08-06 14:25:06 UTC  
> Updated_at: 2020-08-25 19:27:01 UTC  
> Url: https://github.com/boostorg/gil/pull/500#discussion_r466451336  

It seems like either `point_t` preventing `constexpr` initialization or aggregate initialization not being `constexpr` in C++11. Marking this as resolved.

> Username: mloskot  
> Created_at: 2020-08-07 08:04:01 UTC  
> Updated_at: 2020-08-25 19:27:01 UTC  
> Url: https://github.com/boostorg/gil/pull/500#discussion_r466887043  

@simmplecoder Yes, the `gil::point` is not constexpr-friendly. It's constructor should be `constexpr` to allow that  
  
```cpp  
constexpr point(T px, T py) : x(px), y(py) {}  
```  
  
Let's leave it as it is.

---

📁 include/boost/gil/image_processing/diffusion.hpp

```diff
 109 |+ using stencil_type = std::array<PixelType, 8>;
 110 |+ 
 111 |+ struct stencil_5points
```

> Username: mloskot  
> Created_at: 2020-07-26 12:57:03 UTC  
> Updated_at: 2020-08-25 19:27:01 UTC  
> Url: https://github.com/boostorg/gil/pull/500#discussion_r460524653  

AFAIU, these are the approx. of Lapplacian by a 2D kernels, right?  
Can this could be explained in a comment?

> Username: simmplecoder  
> Created_at: 2020-08-06 15:32:51 UTC  
> Updated_at: 2020-08-25 19:27:01 UTC  
> Url: https://github.com/boostorg/gil/pull/500#discussion_r466499390  

Sort of, yes. The difference is computed first, then conductivity, then the computed values are laid out in 8 directions and convolution is performed by a kernel, though it is not exactly Laplacian kernel. Laplacian kernel has -4 in 5 points and -6 (normalized) in 9 points in the middle, whereas in the current version the middle is just left out, as in all other implementations. I added some inline docs, please let me know if I should improve them.

> Username: simmplecoder  
> Created_at: 2020-08-06 17:40:55 UTC  
> Updated_at: 2020-08-25 19:27:01 UTC  
> Url: https://github.com/boostorg/gil/pull/500#discussion_r466579581  

To be honest, no matter how I approach it, I cannot work out _direct_ relationship between Laplacian kernel and the operation the code is doing. The only thing that is certain is that conductivity calculation is not linear kernel, thus it makes convolution complicated.

> Username: mloskot  
> Created_at: 2020-08-07 08:15:53 UTC  
> Updated_at: 2020-08-25 19:27:01 UTC  
> Url: https://github.com/boostorg/gil/pull/500#discussion_r466892588  

AFAIU, it is non-linear.

> Username: mloskot  
> Created_at: 2020-08-07 09:56:34 UTC  
> Updated_at: 2020-08-25 19:27:01 UTC  
> Url: https://github.com/boostorg/gil/pull/500#discussion_r466942813  

@simmplecoder On reflection, AFAIK, a non-linear filter cannot be used with convolution. A non-linear filter can be applied as sliding window (or ellipsoidal region) pixel by pixel.

> Username: mloskot  
> Created_at: 2020-08-25 15:57:56 UTC  
> Updated_at: 2020-08-25 19:27:01 UTC  
> Url: https://github.com/boostorg/gil/pull/500#discussion_r476558929  

@simmplecoder  Can we mark this conversation as resolved?

> Username: simmplecoder  
> Created_at: 2020-08-25 18:38:17 UTC  
> Updated_at: 2020-08-25 19:27:01 UTC  
> Url: https://github.com/boostorg/gil/pull/500#discussion_r476658542  

Let me open an issue about the relation between Laplacian and this phase of the algorithm and I will mark this as resolved.


📁 test/core/image_processing/anisotropic_diffusion.cpp

```diff
 181 |+ 
 182 |+     auto _4way = gil::laplace_function::stencil_5points{};
 183 |+     auto _8way = gil::laplace_function::stencil_9points_standard{};
```

> Username: mloskot  
> Created_at: 2020-07-26 12:58:46 UTC  
> Updated_at: 2020-08-25 19:27:01 UTC  
> Url: https://github.com/boostorg/gil/pull/500#discussion_r460524831  

Nitpick: I'd avoid leading underscores, in general. `s4way` as stencil four-way :-)


---

## Comment 17

> Username: simmplecoder  
> Created_at: 2020-08-06 15:37:32 UTC  
> Url: https://github.com/boostorg/gil/pull/500#issuecomment-670005262  

I had paranoia about current version not being correct. Texts mention that conductivity in south direction is negative of north. What they don't mention is that they take different cells, the ones shifted by one in south direction.  
  
Lets say `diff` is the vertical difference between elements, upper cell minus lower one. If we will shift cells by one and take negative, it will be `I[x][y + 1] - I[x][y]` which is exactly what current code does. I'm just documenting it here in case somebody comes by with questions and I forget I had a look at this already.

---

## Comment 18

> Username: mloskot  
> Created_at: 2020-08-07 08:22:45 UTC  
> Url: https://github.com/boostorg/gil/pull/500#issuecomment-670396427  

@simmplecoder Good finding. I think I've seen different approaches to orientation/origin of kernel in some papers.  
The freedom of choice seems similarly to the Hough transform where the angles and radius are anchored differently for the parameter space.

---

## Review 19 [Approved]

> Username: mloskot  
> Created_at: 2021-01-22 22:52:02 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/gil/pull/500#pullrequestreview-574679621  

@simmplecoder Finally, I'm happy to approve this PR  
  
I'm still hoping we will be able to add some piece of documentation for this feature,  
but that has to happen after the merge.

---

## Comment 20

> Username: simmplecoder  
> Created_at: 2021-01-24 17:45:58 UTC  
> Url: https://github.com/boostorg/gil/pull/500#issuecomment-766401483  

@mloskot Thank you! I will write down the documentation after we settle #517 . I'll ask somebody who knows and document it. That seems to be the most critical piece.

---
