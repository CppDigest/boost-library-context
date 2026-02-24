# #232 - Eigen + Boost.Python + gcc-7 = crash [Open]

> Username: matpen  
> Created at: 2018-09-12 16:10:15 UTC  
> Updated at: 2021-05-01 17:36:53 UTC  
> Url: https://github.com/boostorg/python/issues/232  

I am using Boost.Python to provide bindings for a scientific app which uses [Eigen](http://eigen.tuxfamily.org/) for linear algebra. I am experiencing a segfault with some Eigen types, and I managed to isolate the code that causes the crash.  
  
Please consider the [attached script](https://github.com/boostorg/python/files/2375991/test.txt) which:  
* prepares a cpp file with a simple test case;  
* builds the code with gcc-5, gcc-7, and gcc-8;  
* runs the code from python.  
  
As you can see from the comments at the bottom of the script, the test builds, but leads to a crash when run. This does not happen with gcc-5.  
  
Interestingly, I can reproduce the problem by using `Matrix2d` and `Vector2d`; but it does **not** happen when using `Matrix3d` or `Vector3d` (this can be easily verified by replacing in the attached script).  
  
Now, while the problem can be in any of the 3 components (Eigen, Boost.Python, gcc-7), I decided to post an issue here first because the project we are building uses Eigen extensively, and I never experienced any problem on "C++ side", even while building with gcc-7.  
  
I hope that someone with experience with the Boost.Python internals can help me shed some light on this.  
  
Some more info about the build system, in case it might be useful:  
* `Ubuntu 16.04` running kernel `4.4.0-134-generic`  
* `gcc version 5.5.0 20171010 (Ubuntu 5.5.0-12ubuntu1~16.04)` from apt package `build-essentials`  
* `gcc version 7.3.0 (Ubuntu 7.3.0-21ubuntu1~16.04) ` from [PPA](https://launchpad.net/~ubuntu-toolchain-r/+archive/ubuntu/test)  
* `gcc version 8.1.0 (Ubuntu 8.1.0-5ubuntu1~16.04)` from [PPA](https://launchpad.net/~ubuntu-toolchain-r/+archive/ubuntu/test)  
* `boost 1.66` from [here](https://dl.bintray.com/boostorg/release/1.66.0/source/boost_1_66_0.tar.bz2) built with b2 (just as test, I tried building it with both gcc-5 and gcc-7, with no difference);  
* `boost 1.58` from apt package `libboost-all-dev`.

---

## Comment 1

> Username: stefanseefeld  
> Created at: 2018-09-12 17:18:10 UTC  
> Url: https://github.com/boostorg/python/issues/232#issuecomment-420728292  

Thanks for the report. So for avoidance of doubt: you tested with gcc-5, gcc-7, and gcc-8, but you only observe the crash with gcc-7 ? You mention different Boost versions. Does it crash with all of them ?  
If the crash happens with one compiler version only, perhaps it would be useful to try different optimization levels, to see whether they affect the behaviour. (The bug could be caused by undefined behaviour, which often trigger crashes only with aggressive optimization turned on.)  
Thanks,

---

## Comment 2

> Username: matpen  
> Created at: 2018-09-12 18:40:16 UTC  
> Updated at: 2018-09-12 18:40:39 UTC  
> Url: https://github.com/boostorg/python/issues/232#issuecomment-420754809  

Hi @stefanseefeld, thanks for the quick reaction.  
  
> So for avoidance of doubt: you tested with gcc-5, gcc-7, and gcc-8, but you only observe the crash with gcc-7 ?  
  
The crash happens with both gcc-7 and gcc-8, while building with `-std=c++17` or `-std=c++11`.  
  
> You mention different Boost versions. Does it crash with all of them ?  
  
That is correct: it crashes both with boost 1.58 and boost 1.66.   
  
Sorry for not being completely clear in the issue description: You can see the full list of combinations I tried at the bottom of the attached script.  
  
> If the crash happens with one compiler version only, perhaps it would be useful to try different optimization levels, to see whether they affect the behaviour.  
  
I was so busy fiddling with the various compiler versions, that I did not even think about this: good call. Here is what happens when changing the `-On` optimization flag:  
  
* gcc-7 does **not** crash (under all combinations) with `-O1` or `-O0`; it still crashes with `-O2` and  `-O3`;  
* gcc-8 does **not** crash (under all combinations) with `-O0`; it still crashes with `-O1`, `-O2` and  `-O3`;  
  
What is your advice? Does it look more like a compiler problem, or a boost problem? I did see some warnings about the `register` keyword being deprecated (in the python 2.7 lib, not boost) but I cannot reproduce them right now...  
  
Anyway, if you have further ideas about things to try, feel free to share: the test is ready to be run from my IDE, so it will be very quick to experiment.

---

## Comment 3

> Username: stefanseefeld  
> Created at: 2018-09-12 18:47:23 UTC  
> Url: https://github.com/boostorg/python/issues/232#issuecomment-420756972  

Hi @matpen , I haven't had the time yet to look at your script (or code). But the general pattern of "works with some compilers but not with others" sounds familiar, so "undefined behaviour" was my first thought. (And the fact that the behaviour depends on optimization level further reinforces that suspicion. gcc-8 may just to more optimization even with `-O1`, triggering the crash even earlier than gcc-7.)  
I would thus suspect the problem to be in the code (either yours or Boost.Python), rather than the compiler. Note that nowadays there are a number of quite powerful Free tools to validate source code against known sources for undefined behaviour. For example: https://clang.llvm.org/docs/UndefinedBehaviorSanitizer.html. You may want to give that a try.

---

## Comment 4

> Username: matpen  
> Created at: 2018-09-12 20:38:59 UTC  
> Url: https://github.com/boostorg/python/issues/232#issuecomment-420789370  

That was another good suggestion. I found the same tool (UndefinedBehaviorSanitizer) in gcc, and activated it with `-fsanitize=undefined` and `-lubsan`.  
  
At runtime, I get the error `constructor call on misaligned address`, see output [here](https://github.com/boostorg/python/files/2376744/output.txt). As mentioned earlier, this does not happen with `Vector3d` (but only with `Vector2d`).  
  
As a final test, I tried the very same code in "C++ only": no such warning are issued, and no crash. This would exclude problems with the compiler, with Eigen or with my code.  
  
My guess at this point is that, due to some internal logic, the wrapping python object for `Vector2d` is generated in a wrong way. Appreciate any further ideas.

---

## Comment 5

> Username: stefanseefeld  
> Created at: 2018-09-12 20:54:22 UTC  
> Url: https://github.com/boostorg/python/issues/232#issuecomment-420794255  

I'm glad to see we are making progress narrowing down possible causes of the issue.  
I'll try to look into this as soon as possible.

---

## Comment 6

> Username: matpen  
> Created at: 2018-09-12 20:55:44 UTC  
> Url: https://github.com/boostorg/python/issues/232#issuecomment-420794634  

I am thankful for this! If you think I can be of any help, just let me know!

---

## Comment 7

> Username: matpen  
> Created at: 2018-09-12 21:13:35 UTC  
> Url: https://github.com/boostorg/python/issues/232#issuecomment-420799895  

> I am thankful for this! If you think I can be of any help, just let me know!  
  
Thinking of which, some instructions to quickly reproduce my tests might be useful:  
* the only dependencies are Boost.Python and Eigen;  
* Eigen is header only: you can find a link to the archive [here](http://eigen.tuxfamily.org) (I am using 3.3.1, latest is 3.3.5), just unpack it anywhere and you will be good to go;  
* you can run my script by replacing the install locations of boost and eigen, and then`bash script.sh`;  
* however, my script was intended as a reproducible way to share my attempts: instead of dealing with that, you can just build the following code "however you are used to" (just add the Eigen `include` dir)   
  
Bindings code:  
```  
#include <Eigen/Core>  
#include <boost/python.hpp>  
  
BOOST_PYTHON_MODULE(test)  
{  
    boost::python::class_<Eigen::Vector2d>("Vector2d")  
        .def("__neg__", +[](const Eigen::Vector2d& self) -> Eigen::Vector2d { return -1 * self; })  
        .def("Zero", +[]() -> Eigen::Vector2d { return Eigen::Vector2d::Zero(); })  
        .staticmethod("Zero")  
    ;  
}  
```  
  
Python code  
```  
import test  
zero = test.Vector2d.Zero()  
matrix = -zero # the call to __neg__() crashes  
```  
  
Equivalent C++ code (works):  
```  
#include <Eigen/Core>  
  
int main()  
{  
    Eigen::Vector2d zero = Eigen::Vector2d::Zero();  
    Eigen::Vector2d matrix = -zero;  
}  
```

---

## Comment 8

> Username: stefanseefeld  
> Created at: 2021-05-01 17:36:53 UTC  
> Url: https://github.com/boostorg/python/issues/232#issuecomment-830667071  

I merged some changes to make sure by-value-stored objects use the correct alignment. It would be great if you could try the current `develop` branch to see whether that resolves the issues you were observing.
