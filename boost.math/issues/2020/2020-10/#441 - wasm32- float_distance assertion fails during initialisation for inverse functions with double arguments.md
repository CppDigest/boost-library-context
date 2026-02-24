# #441 - wasm32: float_distance assertion fails during initialisation for inverse functions with double arguments [Closed]

> Username: linolinco  
> Created at: 2020-10-05 12:05:17 UTC  
> Updated at: 2020-10-12 15:56:27 UTC  
> Closed at: 2020-10-12 15:56:27 UTC  
> Url: https://github.com/boostorg/math/issues/441  

Hi, I'm compiling for WebAssembly (wasm32). Any attempt to use boost::math::erf_inv or boost::math::ibeta_inv crashes with  
  
(Assertion failed: result == floor(result), at: ../../code/boost/math/special_functions/next.hpp,545,float_distance_imp)  
  
apparently in initialisation.

---

## Comment 1

> Username: jzmaddock  
> Created at: 2020-10-05 17:40:45 UTC  
> Url: https://github.com/boostorg/math/issues/441#issuecomment-703782861  

What compiler - emscripten?  And what compiler options?  
  
I'm not familiar with webassembly so any pointers would be helpful ;)

---

## Comment 2

> Username: jzmaddock  
> Created at: 2020-10-05 19:01:02 UTC  
> Url: https://github.com/boostorg/math/issues/441#issuecomment-703826970  

Also what type are you passing to erf_inv?  I don't see that function (float_distance) called from erf_inv with double.

---

## Comment 3

> Username: linolinco  
> Created at: 2020-10-05 20:46:00 UTC  
> Url: https://github.com/boostorg/math/issues/441#issuecomment-703879014  

Yes, using emscripten 1.39.8, via Qt 5.15.1 IDE. Afraid it's also my first foray into webassembly... I'll try to boil it down to a simple test case and report Qt's compilation commands tomorrow, similar to the arm64_v8a bug I reported.

---

## Comment 4

> Username: ckormanyos  
> Created at: 2020-10-06 08:48:58 UTC  
> Url: https://github.com/boostorg/math/issues/441#issuecomment-704126260  

> apparently in initialisation  
  
This ticket seems to be another issue. We are, however, also working on an issue involving crash on floating-point exceptions during the pre-main() initialization phase in #433   
  
This would make sense if both floating-point exceptions are activated during the pre-main initialization and also if the application uses erf(), or tgamma(), or lgamma(), or beta_inv(), or any other functions needing Bernoulli numbers initialized pre-main().

---

## Comment 5

> Username: linolinco  
> Created at: 2020-10-06 09:33:01 UTC  
> Updated at: 2020-10-06 11:38:07 UTC  
> Url: https://github.com/boostorg/math/issues/441#issuecomment-704150219  

> Also what type are you passing to erf_inv? I don't see that function (float_distance) called from erf_inv with double.  
  
Well this is interesting... for a double or long double argument erf_inv fails as described, but for a float argument it runs fine, giving an acceptable workaround for now. For ibeta_inv, all three arguments need to be float for this to work.  
  
Also, adding an erf_inv(0.0) call to the very end of the program (after the event loop, so it's never actually reached) is sufficient for the assertion to fail, confirming that it happens during initialisation.

---

## Comment 6

> Username: linolinco  
> Created at: 2020-10-06 10:21:39 UTC  
> Updated at: 2020-10-06 10:22:00 UTC  
> Url: https://github.com/boostorg/math/issues/441#issuecomment-704175424  

Okay, I now have a minimal example that does not require Qt:  
[test.cpp.txt](https://github.com/boostorg/math/files/5333029/test.cpp.txt) (remove .txt extension)  
This is actually the same as for bug #442; feel free to change ibeta_inv to erf_inv. Compile with:  
  
em++ -I. -std=gnu++11 -s WASM=1 test.cpp -o test.js  
  
This should produce a test.wasm as well as test.js file. You also need an HTML loader:  
[test.html.txt](https://github.com/boostorg/math/files/5333038/test.html.txt) (remove .txt extension)  
  
Now put test.wasm, test.js and test.html on a web server (local or remote) and open test.html in your browser. The code doesn't actually draw anything, but you'll find the error log in the Javascript console.

---

## Comment 7

> Username: linolinco  
> Created at: 2020-10-06 11:18:55 UTC  
> Url: https://github.com/boostorg/math/issues/441#issuecomment-704201458  

Running the test example with -DBOOST_MATH_INSTRUMENT produces:  
  
Second order root iteration, guess = 0.225312, min = 0, max = inf, digits = 75, max_iter = 200 test.js:2117:16  
Second order root iteration, limit = 5.29396e-23 test.js:2117:16  
./boost/math/special_functions/erf.hpp:1168 result_type = e test.js:2117:16  
./boost/math/special_functions/erf.hpp:1169 value_type = e test.js:2117:16  
./boost/math/special_functions/erf.hpp:1170 precision_type = N5boost4math8policies7digits2ILi113EEE test.js:2117:16  
./boost/math/special_functions/erf.hpp:1179 tag_type = N5boost17integral_constantIiLi113EEE test.js:2117:16  
./boost/math/special_functions/erf.hpp:638 113-bit precision erf_imp called test.js:2117:16  
./boost/math/tools/roots.hpp:563 f0 = 1.6955949946085303256914321859947458e-21 test.js:2117:16  
./boost/math/tools/roots.hpp:564 f1 = inf test.js:2117:16  
./boost/math/tools/roots.hpp:565 f2 = -inf test.js:2117:16  
./boost/math/tools/roots.hpp:384 denom = 3.3911899892170606513828643719894915e-21 test.js:2117:16  
./boost/math/tools/roots.hpp:385 num = inf test.js:2117:16  
Second order root iteration, delta = 0 test.js:2117:16  
./boost/math/tools/roots.hpp:619 result = 0.22531205501217810294178889307659119 test.js:2117:16  
Second order root finder, final iteration count = 1 test.js:2117:16  
Second order root iteration, guess = -0.33751663059354436446923841685929801, min = 0, max = inf, digits = 75, max_iter = 200 test.js:2117:16  
Second order root iteration, limit = 5.2939559203393771191770156292477623e-23 test.js:2117:16  
./boost/math/special_functions/erf.hpp:1203 result_type = e test.js:2117:16  
./boost/math/special_functions/erf.hpp:1204 value_type = e test.js:2117:16  
./boost/math/special_functions/erf.hpp:1205 precision_type = N5boost4math8policies7digits2ILi113EEE test.js:2117:16  
./boost/math/special_functions/erf.hpp:1214 tag_type = N5boost17integral_constantIiLi113EEE test.js:2117:16  
./boost/math/special_functions/erf.hpp:638 113-bit precision erf_imp called 2 test.js:2117:16  
./boost/math/tools/roots.hpp:563 f0 = 0.91686615335866461506952873605769128 test.js:2117:16  
./boost/math/tools/roots.hpp:564 f1 = -inf test.js:2117:16  
./boost/math/tools/roots.hpp:565 f2 = -inf test.js:2117:16  
./boost/math/tools/roots.hpp:384 denom = 1.8337323067173292301390574721153826 test.js:2117:16  
./boost/math/tools/roots.hpp:385 num = -inf test.js:2117:16  
Second order root iteration, delta = -0 test.js:2117:16  
./boost/math/tools/roots.hpp:619 result = -0.33751663059354436446923841685929801 test.js:2117:16  
Assertion failed: result == floor(result), at: ./boost/math/special_functions/next.hpp,545,float_distance_imp

---

## Comment 8

> Username: jzmaddock  
> Created at: 2020-10-12 15:54:27 UTC  
> Url: https://github.com/boostorg/math/issues/441#issuecomment-707203316  

OK, so I got emscripten installed, still can't execute anything with `emrun` for some reason, but they do run OK under `node` so that's OK I guess :-/  
  
Running libs/config/test/math_info.cpp reveals a few surprises:  
  
* long double is a 128-bit type (software emulation I guess).  
* There is no iostream support for long double except by conversion to double.  
* The long double std lib math functions are mostly implemented in terms of the double ones, so for example:  
  
```  
   assert(std::numeric_limits<long double>::min() != 0);  //OK  
   assert(std::isfinite(std::numeric_limits<long double>::max())); // OK  
   assert(std::isfinite(std::sqrt(std::numeric_limits<long double>::max()/2))); // fails - sqrt of finite number is infinite!  
```  
  
In the past we've defined BOOST_MATH_NO_LONG_DOUBLE_MATH_FUNCTIONS in this situation since all our internal reasoning fails otherwise, I'll commit this fix shortly, but with this in place I've confirmed the special function tests pass.  There may need to be Boost.Config adjustments as well BTW, particularly around exception handling, I'll look into that shortly.
