# #161 Modular adaptor [Closed]

> Username: nemothenoone  
> Created at: 2019-10-15 15:18:30 UTC  
> Updated at: 2025-06-27 20:52:57 UTC  
> Closed at: 2025-06-27 20:52:57 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/161  

Initial modular adaptor implementation.  
  
Consists of a couple of reduction techniques (Montgomery and Barrett reduction techniques). Several supplementary functions. Works for all the backends available now.  
  
Still has a huge room for improvements (e.g. speed, docs etc) and this is coming.  
  
Right now usage examples can be found in ```examples/modular_examples.cpp```.  
  
Dependent on #143. Resolves #144 and https://github.com/NilFoundation/multiprecision/issues/2.

---

## Review 1 [Commented]

> Username: jzmaddock  
> Created_at: 2020-03-11 16:16:52 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/multiprecision/pull/161#pullrequestreview-372914422  

📁 include/boost/multiprecision/modular/modular_adaptor.hpp

```diff
 201 |+ 
 202 |+ template <class Backend, class T>
 203 |+ inline typename enable_if<is_arithmetic<T>, bool>
```

> Username: jzmaddock  
> Created_at: 2020-03-11 16:16:52 UTC  
> Updated_at: 2020-11-03 07:17:52 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/161#discussion_r391092410  

Formatting SNAFU? ;)

> Username: nemothenoone  
> Created_at: 2020-03-12 05:06:35 UTC  
> Updated_at: 2020-11-03 07:17:52 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/161#discussion_r391403451  

I've given up to clang-format. This is what it gave me. We can reformat this if required by disabling it with macro-attributes for these particular lines.


---

## Review 2 [Commented]

> Username: jzmaddock  
> Created_at: 2020-03-11 16:24:19 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/multiprecision/pull/161#pullrequestreview-372920929  

📁 include/boost/multiprecision/cpp_modular.hpp

```diff
  23 |+ 
  24 |+ // Fixed precision unsigned types:
  25 |+ typedef modular_params<cpp_int_backend<128, 128, unsigned_magnitude, unchecked, void> >   umod_params_params128_t;
```

> Username: jzmaddock  
> Created_at: 2020-03-11 16:24:18 UTC  
> Updated_at: 2020-11-03 07:17:52 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/161#discussion_r391097583  

Just a quick question - are fixed precision types appropriate here?  Which is to say do they still work correctly if the operation prior to the modular reduction would overflow?

> Username: nemothenoone  
> Created_at: 2020-03-12 05:18:53 UTC  
> Updated_at: 2020-11-03 07:17:52 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/161#discussion_r391406125  

That should work fine because modular params depend on internal limb storage size, which is non-mutable in case of fixed precision type. So, if the number was initialized properly with particular number, it should work fine.


---

## Review 3 [Commented]

> Username: jzmaddock  
> Created_at: 2020-03-11 16:28:22 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/multiprecision/pull/161#pullrequestreview-372924452  

📁 test/test_modular_adaptor.cpp

```diff
 198 |+ }
 199 |+ 
 200 |+ int main()
```

> Username: jzmaddock  
> Created_at: 2020-03-11 16:28:22 UTC  
> Updated_at: 2020-11-03 07:17:52 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/161#discussion_r391100375  

Small, and rather pedantic point here: not withstanding the fact that I've written many tests like this, I've come to the conclusion that it's much more convenient to put the templates in a header and then write separate - and very short - .cpp files for each type tested.  It's just that it makes it easier to run and debug individual tests when you're not using the supplied build system (whether Boost.Build or CMake).

> Username: nemothenoone  
> Created_at: 2020-03-12 05:04:42 UTC  
> Updated_at: 2020-11-03 07:17:52 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/161#discussion_r391403001  

Coming to tests.  
  
I actually think most of tests should be rewritten to modern Boost.Test usage with all these ```BOOST_AUTO_TEST_SUITE``` and ```BOOST_FIXTURE_TEST_CASE```. So this particular solution you see is a really dumb one - we made it just to put some stub and make it work for now only.  
  
That would look a little bit messy, but standardized.


---

## Comment 4

> Username: jzmaddock  
> Created_at: 2020-03-11 16:29:17 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/161#issuecomment-597734681  

This is looking rather good - let me know when and what you want me to start looking through and testing stuff.

---
