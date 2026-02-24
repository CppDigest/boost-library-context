# #17 Make find-replace unconditional, so that an empty replace will also work... [Closed]

> Username: oe1rsa  
> Created at: 2014-07-10 12:36:51 UTC  
> Updated at: 2021-10-02 22:35:56 UTC  
> Closed at: 2014-07-11 16:17:29 UTC  
> Url: https://github.com/boostorg/build/pull/17  

....  
  
I needed this to make  
type.change-generated-target-prefix SHARED_LIB : <toolset>gcc <target-os>windows : "" ;  
work.

---

## Comment 1

> Username: vprus  
> Created_at: 2014-07-10 18:45:49 UTC  
> Url: https://github.com/boostorg/build/pull/17#issuecomment-48646799  

At least on Linux, running the tests should be a matter of  
  
```  
./bootstrap.sh  
cd tests  
python test_all.py  
```  
  
I run than, and there's one failure in property.jam unit tests; I will take a look in detail tomorrow.

---

## Comment 2

> Username: oe1rsa  
> Created_at: 2014-07-11 07:34:10 UTC  
> Url: https://github.com/boostorg/build/pull/17#issuecomment-48702209  

I also tried to do it (today I am on a Linux machine).  
I tried to run the tests on a pristine checkout, but unfortuantely I  
cannot get around the startup_v2 test. As you can see I already tried to  
manually override my global boost-build dir, but it does not work :-(  
Any hints?  
  
roland@maxwell:~/Source/modular-boost/tools/build/test$  
BOOST_BUILD_PATH=/home/roland/Source/modular-boost/tools/build,  
DO_DIFF=1 python test_all.py  
  
Note: skipping extra tests  
unit_tests                        : PASSED  
module_actions                    : PASSED  
startup_v2                        : DIFFERENCE  
--- /tmp/tmpkILksqexpected  2014-07-11 09:27:35.324517427 +0200  
+++ /tmp/tmpCL6MdTactual    2014-07-11 09:27:35.324517427 +0200  
@@ -1,2 +1,11 @@  
-Unable to load Boost.Build: could not find "boost-build.jam"  
-._Attempted search from ._ up to the root  
\ Kein Zeilenumbruch am Dateiende.  
+warning: mismatched versions of Boost.Build engine and core  
+warning: Boost.Build engine  
(/home/roland/Source/modular-boost/tools/build/test/../src/engine/bin.linuxx86_64/bjam)  
is 2014.03.00  
+warning: Boost.Build core (at /usr/share/boost-build) is 2011.12-svn  
+warning: No toolsets are configured.  
+warning: Configuring default toolset "gcc".  
+warning: If the default is wrong, your build may not work correctly.  
+warning: Use the "toolset=xxxxx" option to override our guess.  
+warning: For more configuration options, please consult  
+warning:  
http://boost.org/boost-build2/doc/html/bbv2/advanced/configuration.html  
+error: no Jamfile in current directory found, and no target references  
specified.  
+  
Unable to compute difference: diff -u "/tmp/tmpkILksqexpected"  
"/tmp/tmpCL6MdTactual"  
FAILED

---

## Comment 3

> Username: vprus  
> Created_at: 2014-07-11 16:17:25 UTC  
> Url: https://github.com/boostorg/build/pull/17#issuecomment-48750967  

Roland,  
  
I've pushed a slightly modified version of your patch - instead of making find-replace unconditional, I check for empty, but explicitly passed, value. Please check whether it works on your end. Thanks for the patch!  
  
Regarding testsuite -- that one appear to fails to me, looks like a recent problem, I will look into that. Do other tests pass for you?

---
