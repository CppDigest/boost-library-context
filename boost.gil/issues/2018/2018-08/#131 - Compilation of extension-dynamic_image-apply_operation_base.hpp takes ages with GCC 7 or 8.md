# #131 - Compilation of extension/dynamic_image/apply_operation_base.hpp takes ages with GCC 7 or 8 [Closed]

> Username: kivadiu  
> Created at: 2018-08-25 17:12:09 UTC  
> Updated at: 2019-02-05 00:47:09 UTC  
> Closed at: 2019-02-05 00:47:09 UTC  
> Url: https://github.com/boostorg/gil/issues/131  

The compilation of the io jpeg extension takes ages with g++ (boost 1.68.0):  
  
### Minimal Working Example (in C++)  
```  
#include <boost/gil/extension/io/jpeg.hpp>  
int main() {  
  return 0;  
}  
```  
### Actual behavior  
compiles on my x86_64 linux system:  
- in 2 min 37 s with g++ 8.2.0 in release mode:  
  `-O2 -DNDEBUG -Wall -Wextra -Wnon-virtual-dtor -Werror=delete-non-virtual-dtor -pipe -march=native -std=c++14 -DBOOST_DISABLE_ASSERTS`  
- in 2 min 37 s with g++ 8.2.0 in debug mode:  
  `-g3 -Wall -Wextra -Wnon-virtual-dtor -Werror=delete-non-virtual-dtor -pipe -march=native -std=c++14`  
  
But it is very fast with clang 5.0.2  
- in 5 s with clang 5.0.2 in release mode:  
  `-O2 -DNDEBUG -Wall -Wextra -Wnon-virtual-dtor -Werror=delete-non-virtual-dtor -pipe -march=native -std=c++14 -DBOOST_DISABLE_ASSERTS`  
- in 5 s with clang 5.0.2 in debug mode:  
  `-g3 -Wall -Wextra -Wnon-virtual-dtor -Werror=delete-non-virtual-dtor -pipe -march=native -std=c++14`  
  
-ftime-report gives the following time breakdown:  
```  
Time variable                                   usr           sys          wall               GGC  
 phase setup                        :   0.01 (  0%)   0.00 (  0%)   0.01 (  0%)    1646 kB (  0%)  
 phase parsing                      : 154.97 (100%)   2.28 ( 99%) 157.74 (100%)  412923 kB ( 98%)  
 phase lang. deferred               :   0.06 (  0%)   0.01 (  0%)   0.07 (  0%)    6184 kB (  1%)  
 phase opt and generate             :   0.01 (  0%)   0.01 (  0%)   0.01 (  0%)     561 kB (  0%)  
 |name lookup                       :   0.47 (  0%)   0.13 (  6%)   0.69 (  0%)   13160 kB (  3%)  
 |overload resolution               :   0.06 (  0%)   0.01 (  0%)   0.11 (  0%)    7204 kB (  2%)  
 garbage collection                 :   0.08 (  0%)   0.00 (  0%)   0.08 (  0%)       0 kB (  0%)  
 callgraph construction             :   0.01 (  0%)   0.01 (  0%)   0.00 (  0%)     484 kB (  0%)  
 preprocessing                      :   1.07 (  1%)   0.65 ( 28%)   1.89 (  1%)  190652 kB ( 45%)  
 parser (global)                    :   0.55 (  0%)   0.50 ( 22%)   0.90 (  1%)   91965 kB ( 22%)  
 parser struct body                 :   0.34 (  0%)   0.05 (  2%)   0.40 (  0%)   36645 kB (  9%)  
 parser function body               :   0.08 (  0%)   0.03 (  1%)   0.12 (  0%)    3138 kB (  1%)  
 parser inl. func. body             :   0.05 (  0%)   0.02 (  1%)   0.05 (  0%)    3248 kB (  1%)  
 parser inl. meth. body             :   1.39 (  1%)   0.10 (  4%)   1.49 (  1%)   55566 kB ( 13%)  
 template instantiation             : 151.46 ( 98%)   0.93 ( 40%) 152.87 ( 97%)   37564 kB (  9%)  
 constant expression evaluation     :   0.01 (  0%)   0.01 (  0%)   0.01 (  0%)     134 kB (  0%)  
 tree PTA                           :   0.00 (  0%)   0.00 (  0%)   0.01 (  0%)       0 kB (  0%)  
 TOTAL                              : 155.05          2.30        157.83         421325 kB  
```  
  
So it is mostly template instantiation although nothing is instantiated.  
  
### Expected  behavior  
I would hope that such file compile within a few seconds.  
  
### Environment  
Fedora 27 x86_64 linux  
  
All relevant information like:  
- Boost version (see `<boost/version.hpp>`): 1.68.0  
  
Is there anything that can be done to improve the compile time with g++?

---

## Comment 1

> Username: mloskot  
> Created at: 2018-08-25 20:27:01 UTC  
> Updated at: 2018-08-25 20:34:23 UTC  
> Url: https://github.com/boostorg/gil/issues/131#issuecomment-415994440  

I can only confirm your observations match my own - compilation times increases with every new GCC version:  
  
```  
$ cat gil-131.cpp  
#include <boost/gil/extension/io/jpeg.hpp>  
int main() { return 0; }  
```  
  
* Quick comparison of GCC 5, 6, 7, 8  
  
```  
$ for v in {5..8}; do echo && echo "GCC $v" && time g++-$v -O2 -DNDEBUG -Wall -Wextra -Wnon-virtual-dtor -Werror=delete-non-virtual-dtor -pipe -march=native -std=c++14 -DBOOST_DISABLE_ASSERTS -I/mnt/d/boost.wsl gil-131.cpp; done  
  
GCC 5  
  
real    0m9.148s  
user    0m6.938s  
sys     0m2.141s  
  
GCC 6  
  
real    3m14.485s  
user    3m12.313s  
sys     0m2.109s  
  
GCC 7  
  
real    2m49.457s  
user    2m47.313s  
sys     0m1.859s  
  
GCC 8  
  
real    2m38.740s  
user    2m36.688s  
sys     0m2.047s  
```  
  
* Quick comparison of clang 5, 6  
  
```  
$ for v in 5 6; do echo && echo "clang $v" && time clang++-$v.0 -O2 -DNDEBUG -Wall -Wextra -Wnon-virtual-dtor -Werror=delete-non-virtual-dtor -pipe -march=native -std=c++14 -DBOOST_DISABLE_ASSERTS -I/mnt/d/boost.wsl gil-131.cpp; done  
  
clang 5  
  
real    0m4.536s  
user    0m2.922s  
sys     0m1.578s  
  
clang 6  
  
real    0m4.697s  
user    0m3.031s  
sys     0m1.656s  
```  
  
Finally, you can see exactly the same trend in Boost.GIL builds on Circle CI. Check the currently latest workflow with GCC & clang builds at https://circleci.com/workflow-run/c6b11033-65cb-4f2c-a4f9-c2e43ee4fb9b . For convenience, here is CircleCI screenshot with compilers/versions and build times for GIL core tests only:  
  
<img src="https://user-images.githubusercontent.com/80741/44622279-cc9a2080-a8b5-11e8-9087-5ac7353de063.png" width=300>  
  
Although I'd like to know, I have not tried to investigate why GCC becomes slower with every new version.

---

## Comment 2

> Username: kivadiu  
> Created at: 2018-08-26 06:00:45 UTC  
> Url: https://github.com/boostorg/gil/issues/131#issuecomment-416016170  

I found this similar bug:  
https://gcc.gnu.org/bugzilla/show_bug.cgi?id=86320  
but it says it was fixed before 8.2.0 was out (not sure it is actually included in 8.2.0 though).

---

## Comment 3

> Username: kivadiu  
> Created at: 2018-08-26 07:35:09 UTC  
> Url: https://github.com/boostorg/gil/issues/131#issuecomment-416020212  

I found the code that triggers this bad behaviour with gcc. It is in file `boost/gil/extension/dynamic_image/apply_operation_base.hpp` at line 125:  
```  
GIL_GENERATE_APPLY_FWD_OPS(99)  
```  
This single macro generates a line of 2701372 characters (nearly 3 millions)!  
But it is not the macro itself that takes time, it's the template instantiation of it content.  
  
Note that clang generates the exact same extra long line.

---

## Comment 4

> Username: kivadiu  
> Created at: 2018-08-26 08:08:39 UTC  
> Url: https://github.com/boostorg/gil/issues/131#issuecomment-416021721  

I was able to make a _minimal_ example that does not depend any more on `boost::gil`:  
[gcc-is-long.cpp.gz](https://github.com/boostorg/gil/files/2321341/gcc-is-long.cpp.gz)  
To go further to be able to post a bug at gcc, it would be nice to also remove the remaining references to `boost::mpl`:  
```  
#include <boost/mpl/begin.hpp>  
#include <boost/mpl/deref.hpp>  
#include <boost/mpl/next.hpp>  
```  
Then we could also remove part of the lines as we probably do not need the 99 generated structs to show the problem.

---

## Comment 5

> Username: kivadiu  
> Created at: 2018-08-26 08:26:22 UTC  
> Url: https://github.com/boostorg/gil/issues/131#issuecomment-416022588  

If we just keep the `<99>` instanciation, clang++ takes 0.2 s while g++ takes 8.3 s (x42).  
That is probably enough to show the problem. There is just the dependency on `boost::mpl` that is problematic to submit a bug to gcc.

---

## Comment 6

> Username: mloskot  
> Created at: 2018-08-26 09:32:31 UTC  
> Updated at: 2018-08-26 09:47:19 UTC  
> Url: https://github.com/boostorg/gil/issues/131#issuecomment-416025787  

Good effort and interesting findings!  
  
Regarding MPL, I've started removing it from GIL, but it's still long way to go https://github.com/boostorg/gil/pull/107 We definitely aim to replace as many Boost dependencies with C++11 as possible.  
  
*NOTE: The PR #107 has got broken by recent master vs develop shuffles before the 1.68 release*  
  
Here I made a new version of PR #107 but against old develop from before 1.68 release  
https://github.com/mloskot/gil/pull/1 - it should make it easier to see how far we got with MPL removal.

---

## Comment 7

> Username: kivadiu  
> Created at: 2018-08-26 12:24:01 UTC  
> Updated at: 2018-08-26 12:26:46 UTC  
> Url: https://github.com/boostorg/gil/issues/131#issuecomment-416035250  

OK, I see. This is a difficult job.  
  
Meanwhile, I have been able to make a minimal standalone example (no boost dependency) that I think I can submit to the gcc team. Because it is still a good thing if gcc can be improved for speed.  
[gcc-is-long-minimum-and-standalone.cpp.gz](https://github.com/boostorg/gil/files/2321495/gcc-is-long-minimum-and-standalone.cpp.gz)  
  
On my computer clang++ 6.0 is 53x quicker than g++ 8.2:  
  
```  
$ for cc in clang++ g++-8.2.0; do echo $cc; time $cc -o gcc-is-long2-$cc.o -c -O2 -DNDEBUG -std=c++14 Progs/gcc-is-long-minimum-and-standalone.cpp; done  
clang++  
  
real	0m0,152s  
user	0m0,102s  
sys	0m0,038s  
g++-8.2.0  
  
real	0m8,175s  
user	0m8,146s  
sys	0m0,015s  
```

---

## Comment 8

> Username: kivadiu  
> Created at: 2018-08-26 12:47:27 UTC  
> Url: https://github.com/boostorg/gil/issues/131#issuecomment-416036598  

Submitted to the gcc team: https://gcc.gnu.org/bugzilla/show_bug.cgi?id=87107

---

## Comment 9

> Username: mloskot  
> Created at: 2018-08-26 17:37:51 UTC  
> Updated at: 2018-08-27 07:20:32 UTC  
> Url: https://github.com/boostorg/gil/issues/131#issuecomment-416055530  

@kivadiu good stuff! Thank you. I've added myself to CC in the GCC bug.  
  
I've forwarded your bug report to Boost ML: https://lists.boost.org/Archives/boost/2018/08/242919.php  
  
Do you agree to close this issue as delegated to GCC developers?

---

## Comment 10

> Username: kivadiu  
> Created at: 2018-08-28 19:24:24 UTC  
> Url: https://github.com/boostorg/gil/issues/131#issuecomment-416710076  

> Do you agree to close this issue as delegated to GCC developers?  
  
Are you sure the `boost::gil` code I identified cannot be simplified?

---

## Comment 11

> Username: mloskot  
> Created at: 2018-08-29 07:31:38 UTC  
> Url: https://github.com/boostorg/gil/issues/131#issuecomment-416854397  

@kivadiu   
> Are you sure the boost::gil code I identified cannot be simplified?  
  
Good question. Let me think, or @chhenning & @stefanseefeld in case they're available to chime in.  
September is going to be a busy month, so no short term promises.  
  
Let's keep it open then.

---

## Comment 12

> Username: sdebionne  
> Created at: 2019-02-01 16:49:43 UTC  
> Url: https://github.com/boostorg/gil/issues/131#issuecomment-459788337  

I am impacted by this issue as well and unfortunately I see no progress on the GCC front.  
  
#### Boost 1.68  
```  
time $CXX O2 -DNDEBUG -Wall -Wextra -Wnon-virtual-dtor -Werror=delete-non-virtual-dtor -pipe -march=native -std=c++14 -DBOOST_DISABLE_ASSERT test_io_1_68.cpp  
  
real    1m58.244s  
user    1m57.266s  
sys     0m0.656s  
````  
  
#### Boost 1.67  
```  
time $CXX O2 -DNDEBUG -Wall -Wextra -Wnon-virtual-dtor -Werror=delete-non-virtual-dtor -pipe -march=native -std=c++14 -DBOOST_DISABLE_ASSERT test_io_1_67.cpp  
  
real    0m1.587s  
user    0m1.016s  
sys     0m0.547s  
```  
  
I guess you are not going to like this, but could we reintroduce the old I/O extension as an option?

---

## Comment 13

> Username: mloskot  
> Created at: 2019-02-01 17:05:01 UTC  
> Updated at: 2019-02-01 17:09:47 UTC  
> Url: https://github.com/boostorg/gil/issues/131#issuecomment-459793436  

@sdebionne   
> I guess you are not going to like this, but could we reintroduce the old I/O extension as an option?  
  
(I'd suggest to open an issue with suggestion/request)  
  
I**M**HO: I see no problem in hosting the old I/O v1 in the Boost.GIL source tree, iff:  
  
* it does not require any changes to existing GIL headers   
* it lives completely in `/include/boost/gil/extension/io_v1` and tests in `/io_v1/test`  
    * or make a new name, e.g.  `/include/boost/gil/extension/nanoio` :-)  
* someone takes care of it  
    * re-introduction of headers  
    * re-introduction of tests (optional?)  
    * updates documentation, incl. docs of the `io_v1` (optional? shall it be a hidden feature?)  
    * updates `Jamfile`-s (optional? shall we have it in but not CI-tested?)  
* someone basically is going to maintain it  
  
If we re-introduce it and it stops passing its own tests, and nobody chimes in to maintain it, it probably will remain as an abandoned artefact in the GIL source tree. I personally have no interest in `io_v1` and I have no resources to offer to maintain it. Other than that, I see no reason to not to re-introduce it.  
  
Regardless of my personal opinion, I will delegate and leave the decision to maintainers of higher seniority, to @stefanseefeld and @chhenning.

---

## Comment 14

> Username: sdebionne  
> Created at: 2019-02-01 17:17:44 UTC  
> Updated at: 2019-02-01 17:21:44 UTC  
> Url: https://github.com/boostorg/gil/issues/131#issuecomment-459797526  

I just had a look at the implementation of `apply_visitor` in [Boost.Variant](https://www.boost.org/doc/libs/1_69_0/boost/variant/detail/apply_visitor_unary.hpp) and found out that there is a modern implementation that is not based on MPL (requires C++14 `decltype(auto)`). That might be a better option.

---

## Comment 15

> Username: mloskot  
> Created at: 2019-02-01 17:20:34 UTC  
> Url: https://github.com/boostorg/gil/issues/131#issuecomment-459798422  

I am very very close to start actually replacing all Boost.MPL with Boost.MP11 (I've set myself a deadline until end of the Feb).  
Even if not directly related to this particular issue, Peter Dimov on cpplang.slack.com suggested we should expect compilation boost anyway.

---

## Comment 16

> Username: sdebionne  
> Created at: 2019-02-01 17:48:26 UTC  
> Url: https://github.com/boostorg/gil/issues/131#issuecomment-459807180  

Sounds good! I use Boost.Hana in some projects and have zero experience with Boost.MP11. Do you think importing the newer `apply_visitor` implementation is worth a try?

---

## Comment 17

> Username: mloskot  
> Created at: 2019-02-01 17:53:09 UTC  
> Url: https://github.com/boostorg/gil/issues/131#issuecomment-459808762  

To me, everything that has potential to improve GIL is worth a try. Especially if there is an actual user who would benefit from such improvement, you :-)  
  
It's just that I'm judging that particular one as layman - I have not looked into that code (too busy right now),  so you have to judge if the potential is real/you can take risk of wasting time, etc.  
  
However, I appreciate your help @sdebionne !

---

## Comment 18

> Username: sdebionne  
> Created at: 2019-02-04 12:15:46 UTC  
> Url: https://github.com/boostorg/gil/issues/131#issuecomment-460228392  

I read the Boost.Variant code too fast, MPL was actually introduced with the C++14 `decltype(auto)` implementation. I am looking into other solutions to workaround this GCC bug. One of them is to remove GIL's variant implementation completely and use Boost.Variant instead (better maintained). `std::variant` is an option to, but would require C++17.

---

## Comment 19

> Username: mloskot  
> Created at: 2019-02-04 13:08:56 UTC  
> Url: https://github.com/boostorg/gil/issues/131#issuecomment-460244059  

@sdebionne   
> std::variant is an option to, but would require C++17.  
  
Personally, I'd welcome jump to C++17.  
Practically, there could be in-house implementation and the `std::variant`-based for C++17 compilation mode. Then, the former could be removed once we make the switch.  
  
@stefanseefeld Comments?

---

## Comment 20

> Username: sdebionne  
> Created at: 2019-02-04 13:11:34 UTC  
> Url: https://github.com/boostorg/gil/issues/131#issuecomment-460244769  

I can confirm that Boost.Variant does not trigger the GCC bug (using the code in #231):  
  
```  
time $CXX -O2 -DNDEBUG -Wall -Wextra -Wnon-virtual-dtor -Werror=delete-non-virtual-dtor -pipe -march=native -std=c++14 -DBOO  
ST_DISABLE_ASSERT test_io_1_68.cpp  
  
real    0m3.782s  
user    0m1.156s  
sys     0m0.641s  
```

---

## Comment 21

> Username: sdebionne  
> Created at: 2019-02-04 13:17:24 UTC  
> Url: https://github.com/boostorg/gil/issues/131#issuecomment-460246474  

The jump from GIL's variant to `std::variant` is a bit harder. And I would expect Boost.Variant to use `std::variant` under the hood when available just like other Boost libraries do when they are integrated to the standard library.

---

## Comment 22

> Username: mloskot  
> Created at: 2019-02-04 22:33:59 UTC  
> Url: https://github.com/boostorg/gil/issues/131#issuecomment-460440344  

The compilation speed up is very good news! Thanks
