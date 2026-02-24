# #442 - arm64_v8a: ibeta_inv exceeds 200 iterations for some arguments [Open]

> Username: linolinco  
> Created at: 2020-10-05 13:03:32 UTC  
> Updated at: 2020-10-12 17:33:13 UTC  
> Url: https://github.com/boostorg/math/issues/442  

Hi, when compiling for 64-bit ARM (arm64_v8a), ibeta_inv(alpha, beta, x) produces  
  
boost::exception_detail::clone_impl<boost::exception_detail::error_info_injector<boost::math::evaluation_error> >: Error in function boost::math::ibeta<long double>(long double, long double, long double): Root finding evaluation exceeded 200 iterations, giving up now.  
  
when either alpha or beta are smaller than about 3e-3. For other target processors I've tried (arm_v7a, x86, x86_64) this works fine.  
  
I've now hacked up my own custom root finder for these cases which converges in just a few iterations. My code's certainly not up to boost standards, but I'm happy to share it as a starting point if someone wants to use it to improve the ibeta_inv root finder in this regard.  
  
Though I suspect that something else is going on here - the bug may be indicative of some deeper numerical issues on arm64_v8a?

---

## Comment 1

> Username: jzmaddock  
> Created at: 2020-10-05 17:34:11 UTC  
> Url: https://github.com/boostorg/math/issues/442#issuecomment-703779250  

Works for me here, but may possibly be fixed by https://github.com/boostorg/math/commit/fa8f763df9e6635cf255d60766af65950eec58e8.  
  
Can you:  
  
1) check to see if the above commit fixes your issue, otherwise,  
2) Can you please provide a test case (the exact arguments that trigger the issue), and  
3) Compile your test case with `-DBOOST_MATH_INSTRUMENT` and then run your test case and report the program output - it'll allow me to see where the root finder is going wrong.  
  
Many thanks for the report!

---

## Comment 2

> Username: linolinco  
> Created at: 2020-10-05 20:24:55 UTC  
> Updated at: 2020-10-05 20:32:59 UTC  
> Url: https://github.com/boostorg/math/issues/442#issuecomment-703868776  

Hi, fa8f763 unfortunately didn't help. Here is the test case I've boiled it down to:  
[test.cpp.txt](https://github.com/boostorg/math/files/5329872/test.cpp.txt)  
[test.pro.txt](https://github.com/boostorg/math/files/5329875/test.pro.txt)  
(remove .txt extensions)  
Essentially it's just a call to boost::math::ibeta_inv(1e-3, 20.0, 0.2).  
  
Though there's no code dependency on Qt, I'm using Qt 5.15.1 for its cross-compilation toolchain. Sorry but I don't know how to do this otherwise. FWIW, Qt's compilation commands for this are:  
  
/Developer/Applications/AndroidSDK/ndk/21.1.6352462/toolchains/llvm/prebuilt/darwin-x86_64/bin/clang++ -c -target aarch64-linux-android21 -fno-limit-debug-info -fPIC -fstack-protector-strong -DANDROID -g -Wall -W -D_REENTRANT -fPIC -DBOOST_MATH_INSTRUMENT -DQT_QML_DEBUG -I. -I/Developer/Qt/5.15.1/android/mkspecs/android-clang -o arm64-v8a/test.o ../tools/test.cpp  
  
/Developer/Applications/AndroidSDK/ndk/21.1.6352462/toolchains/llvm/prebuilt/darwin-x86_64/bin/clang++ -target aarch64-linux-android21 -fno-limit-debug-info -Wl,-soname,libtest_arm64-v8a.so -Wl,--build-id=sha1 -Wl,--no-undefined -Wl,-z,noexecstack -shared -o libtest_arm64-v8a.so arm64-v8a/test.o -llog -lz -lm -ldl -lc  
  
in case you can glean anything from that. BOOST_MATH_INSTRUMENT unfortunately doesn't help  as I can't see cout/cerr under Qt.  
  
Can anyone help us run this one-liner naked (without Qt) on an arm64_v8a device?

---

## Comment 3

> Username: jzmaddock  
> Created at: 2020-10-09 17:59:00 UTC  
> Url: https://github.com/boostorg/math/issues/442#issuecomment-706324047  

Well.... I've just spent 2 rather fruitless days getting Android Studio up and running (how does anyone actually get any work done with this thing?) only to resolutely fail to be able to run any of the arm virtual machines :(  The Intel ones are fine (albeit slow as hell), just can't get an arm emulation one to start up.  
  
So.... I'm going to have to get you to do some debugging at your end.  
  
What's happening in this domain is that the initial guess is zero (actually gets set to `boost::math::tools::min_value<T>()`), the derivatives are very close to infinity, and so after some checks and mucking about, we should go straight to the result (`0`) with no iterations at all.  
  
If you define BOOST_MATH_INSTRUMENT when building and then do something like this:  
  
```  
   std::stringstream ss;  
   auto p = std::cout.rdbuf(ss.rdbuf());  
  
   std::cout << boost::math::ibeta_inv(1e-3, 20.0, 0.2) << std::endl;  
  
   std::cout.rdbuf(p);  
   // do something with ss.str() to get the output here...  
```  
  
We might hopefully get some useful information.  My guess is that something somewhere is ending up as a NaN, though I don't see how/why.

---

## Comment 4

> Username: linolinco  
> Created at: 2020-10-10 15:36:57 UTC  
> Updated at: 2020-10-10 15:37:40 UTC  
> Url: https://github.com/boostorg/math/issues/442#issuecomment-706567410  

I feel your pain. Qt helps insulate me somewhat from the hell of mobile development, though not nearly enough. (This also explains my interest in WebAssembly.) Yes the Android emulator only emulates the actual CPU it runs on... so to debug ARM code you need to physically hook up an ARM device via USB, very 20th century. Well at least my smartphone charges while it serves.  
  
Okay let's see. First, I verified that all instances where the iteration limit throws in my code are indeed ones where the result should be zero. Second, here is the output from the instrumented ibeta_inv(1e-3, 20.0, 0.2) call:  
  
Second order root iteration, guess = 3.3621031431120935062626778173217526e-4932, min = 3.3621031431120935062626778173217526e-4932, max = 1, digits = 84, max_iter = 200  
Second order root iteration, limit = 1.0339757656912845935892608650874536e-25  
boost/math/special_functions/beta.hpp:999 a = 0.0010000000000000000208166817117216851  
boost/math/special_functions/beta.hpp:1000 b = 20  
boost/math/special_functions/beta.hpp:1001 x = 3.3621031431120935062626778173217526e-4932  
boost/math/special_functions/beta.hpp:1002 inv = 0  
boost/math/special_functions/beta.hpp:1003 normalised = 1  
boost/math/special_functions/beta.hpp:1188 fract = 0  
boost/math/tools/roots.hpp:563 f0 = -0.2000000000000000111022302462515654  
boost/math/tools/roots.hpp:564 f1 = 2.1517460115917398440081138030859217e-4930  
boost/math/tools/roots.hpp:565 f2 = 0  
Second order root iteration, delta = -9.2947773074783736806962835155767105e+4928  
boost/math/tools/roots.hpp:_[exception triggered here]_  
  
Anything informative?

---

## Comment 5

> Username: jzmaddock  
> Created at: 2020-10-10 17:53:01 UTC  
> Url: https://github.com/boostorg/math/issues/442#issuecomment-706587348  

It might have been more useful to see the output from the first iteration, but whatever those derivative values (f1 and f2) are completely wrong :(  
  
Quick sanity check - what happens if you build with -DBOOST_MATH_PROMOTE_DOUBLE_POLICY=false ?  
  
That will stop the code from using long doubles internally, I'm guessing there's a bug in the std lib long double support code somewhere, but tracking it down might be a bind!

---

## Comment 6

> Username: linolinco  
> Created at: 2020-10-10 19:23:52 UTC  
> Updated at: 2020-10-10 19:37:34 UTC  
> Url: https://github.com/boostorg/math/issues/442#issuecomment-706599162  

What I wrote was the entire output - should there have been more?  
  
With -DBOOST_MATH_PROMOTE_DOUBLE_POLICY=false the call works and returns zero as expected.  
  
Happy to help - but since you've already gone through the pain of setting up Android Studio, if you have an Android phone lying around, might as well hook it up?  
  
FWIW, my workaround for the time being is:  
```  
try {  
    return boost::math::ibeta_inv(alpha, beta, x); }  
catch (boost::wrapexcept<boost::math::evaluation_error>) {  
    return (x > beta/(alpha + beta) ? 1.0 : 0.0);  
}  
```  
though that carries quite a performance penalty: 200 iterations plus an exception to arrive at zero/one.

---

## Comment 7

> Username: jzmaddock  
> Created at: 2020-10-11 09:41:19 UTC  
> Url: https://github.com/boostorg/math/issues/442#issuecomment-706678006  

>What I wrote was the entire output - should there have been more?  
  
Yes that was only the final iteration of the root finder's main loop.  
  
>With -DBOOST_MATH_PROMOTE_DOUBLE_POLICY=false the call works and returns zero as expected.  
  
I suspect that will have to become the "official" workaround.  
  
>Happy to help - but since you've already gone through the pain of setting up Android Studio, if you have an Android phone lying around, might as well hook it up?  
  
Sorry, no andriod devices around here :(  
  
>FWIW, my workaround for the time being is:  
  
It would be better to disable long double usage as noted above.  
  
BTW we have a long double test program to probe issues like this: https://github.com/boostorg/math/blob/develop/test/test_long_double_support.cpp.  However, figuring out how to run it on a mobile device might be a challenge!  
  
Can you also see if `-DBOOST_MATH_PROMOTE_DOUBLE_POLICY=false` fixes the web assembly issue?

---

## Comment 8

> Username: linolinco  
> Created at: 2020-10-12 16:09:15 UTC  
> Updated at: 2020-10-12 17:33:13 UTC  
> Url: https://github.com/boostorg/math/issues/442#issuecomment-707211483  

Yes, -DBOOST_MATH_PROMOTE_DOUBLE_POLICY=false also fixes the WebAssembly issue. Thanks!  
  
If it's straight C++ running the test program on arm64_v8a should be no problem, that's what Qt's for. I'll let you know.  
  
EDIT: compilation of the test program hangs with  
`handle_test_result.hpp:10: error: 'boost/math/tools/test.hpp' file not found`  
There were 3 other include files which I found and added to the project, but this one doesn't exist in the referenced location, neither in the official release nor in the git. What am I missing?
