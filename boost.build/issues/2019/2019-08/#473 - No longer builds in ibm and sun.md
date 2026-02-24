# #473 - No longer builds in ibm and sun [Closed]

> Username: juanalday  
> Created at: 2019-08-27 13:24:43 UTC  
> Updated at: 2020-12-22 20:48:50 UTC  
> Closed at: 2020-12-22 20:48:50 UTC  
> Url: https://github.com/boostorg/build/issues/473  

Both xlc and sunstudio use different flags for version. '--version' doesn't work there.  
  
tools/build/src/engine/build.sh essentially breaks in the initial bootstrapping, before even trying to build b2.  
  
for Sun, it should be CC -V  
For ibm, it should be xlC_r -qversion  
  
I can submit a pull request for this, but I'd like to know if the (intentional, as documented in the release note) move to C++11 takes into account that it essentially removes boost support for 98/03. Even if I patch this flag, build will still fail in those compilers.

---

## Comment 1

> Username: grafikrobot  
> Created at: 2019-08-27 18:12:16 UTC  
> Url: https://github.com/boostorg/build/issues/473#issuecomment-525420116  

It doesn't remove Boost support for 98/03. B2 itself can be compiled with a C++11 compiler and then used to compile 98/03 code. And I would love a PR to correctly support Sun and IBM compilers :-)

---

## Comment 2

> Username: juanalday  
> Created at: 2019-08-27 18:32:47 UTC  
> Url: https://github.com/boostorg/build/issues/473#issuecomment-525428175  

Sure it does. How can I built a (supported) 98-mode library in boost with a compiler that supports only C++98 if the build tool can only be built with C++11?   
  
Of course I can do that at home, but corporate environments are difficult to modify; there are lots of reasons (including legal) why I can't (for example) install gcc in an AIX box and use it to bootstrap bjam before passing control to xlC_r. The build tool should be built with the lowest standard supported by the library.   
  
I will see if I can backport the changes to 98 via ifdef and send a PR, otherwise I will send you one just for the initial build.sh file and stick to reverse-patching build to the previous version...

---

## Comment 3

> Username: grafikrobot  
> Created at: 2019-08-29 03:51:34 UTC  
> Url: https://github.com/boostorg/build/issues/473#issuecomment-526009806  

As far as I know XL C/C++ supports C++ 11 and 14.. https://www.ibm.com/us-en/marketplace/xl-cpp-aix-compiler-power -- Perhaps there's a missing option to enable the language level?

---

## Comment 4

> Username: juanalday  
> Created at: 2019-08-29 14:14:43 UTC  
> Url: https://github.com/boostorg/build/issues/473#issuecomment-526204779  

Of course there are versions of SunCC and xlC that support 11 and 14. I believe my employer is one of the main sponsors of the clang effort for xlc. It doesn't mean I can use it.   
Same can be applied to gcc: If you have a production environment where you are not allowed to use gcc 4.xx or higher (again, it can be due to legal reasons) you don't get C++11, although the latest version has 2a support.

---

## Comment 5

> Username: juanalday  
> Created at: 2019-08-29 14:16:34 UTC  
> Url: https://github.com/boostorg/build/issues/473#issuecomment-526205638  

In any case, the patch is 5 lines (in sysinfo.cpp and sysinfo.h) so I can live with it. Feel free to close this issue.

---

## Comment 6

> Username: tanzislam  
> Created at: 2019-09-17 21:51:19 UTC  
> Updated at: 2019-09-25 20:08:17 UTC  
> Url: https://github.com/boostorg/build/issues/473#issuecomment-532416323  

The only use of C++11 is [`std::thread::hardware_concurrency()` in `src/engine/sysinfo.cpp`](https://github.com/boostorg/build/commit/c27d575fb32fb0f48327cc459031e1f4e629a29b#diff-982fa8ccbcf7d79c82fa753069fb9562) and a couple of [member-initializers in `src/engine/sysinfo.h`](https://github.com/boostorg/build/commit/150d69bd57b70d5a717cc14d15af076ea9d5258d#diff-b8dbc6312925f5296d1152ac9abbd377). Should be very easy to rewrite to be C++98/03-compliant.

---

## Comment 7

> Username: grafikrobot  
> Created at: 2020-12-22 20:48:50 UTC  
> Url: https://github.com/boostorg/build/issues/473#issuecomment-749768775  

We are now using more C++11 features, and more to come. It's not tractable to support ongoing b2 engine development below C++11.
