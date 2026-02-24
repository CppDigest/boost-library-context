# #60 - uuid tests don't build as part of 'status' [Closed]

> Username: beddingfield  
> Created at: 2018-03-22 14:19:26 UTC  
> Updated at: 2018-03-28 17:10:37 UTC  
> Closed at: 2018-03-28 17:10:36 UTC  
> Url: https://github.com/boostorg/uuid/issues/60  

When trying to run all tests in boost 1.66.0, the build description for the UUID tests fail:  
  
```  
$ cd libs/uuid/test  
$ ../../../bjam  
  
Performing configuration checks  
    - 32-bit                   : no  (cached)  
    - 64-bit                   : yes (cached)  
    - arm                      : no  (cached)  
    - mips1                    : no  (cached)  
    - power                    : no  (cached)  
    - sparc                    : no  (cached)  
    - x86                      : yes (cached)  
    - symlinks supported       : yes (cached)  
error: Unable to find file or target named  
error:     '/libs/uuid/test//../include/boost/uuid/uuid.hpp'  
error: referred to from project at  
error:     '.'  
  
```  
  
The following patch resolves it for me:  
  
```  
diff -Nurp boost_1_66_0.orig/libs/uuid/test/Jamfile.v2 boost_1_66_0/libs/uuid/test/Jamfile.v2  
--- boost_1_66_0.orig/libs/uuid/test/Jamfile.v2 2017-12-13 15:56:49.000000000 -0800  
+++ boost_1_66_0/libs/uuid/test/Jamfile.v2      2018-03-16 09:06:28.569850135 -0700  
@@ -41,12 +41,12 @@ rule test_all  
     all_rules += [ compile compile/decl_header.cpp :  
         <define>"BOOST_UUID_TEST_HEADER=uuid.hpp"  
         <define>"BOOST_UUID_NO_TYPE_TRAITS"  
-        <dependency>../include/boost/uuid/uuid.hpp :  
+        <dependency>../../../boost/uuid/uuid.hpp :  
             compile_uuid_no_type_traits ] ;  
     all_rules += [ compile compile/decl_header.cpp :  
         <define>"BOOST_UUID_TEST_HEADER=uuid.hpp"  
         <define>"BOOST_UUID_NO_SIMD"  
-        <dependency>../include/boost/uuid/uuid.hpp :  
+        <dependency>../../../boost/uuid/uuid.hpp :  
             compile_uuid_no_simd ] ;  
     # ECHO All rules: $(all_rules) ;  
  
```

---

## Comment 1

> Username: mclow  
> Created at: 2018-03-22 14:54:58 UTC  
> Url: https://github.com/boostorg/uuid/issues/60#issuecomment-375335416  

A better question is WHY you don't have a file `$BOOST/libs/uuid/include/boost/uuid/uuid.hpp`  
  
If you look here: https://github.com/boostorg/uuid/tree/master/include/boost/uuid , you should have it.

---

## Comment 2

> Username: swatanabe  
> Created at: 2018-03-22 15:00:01 UTC  
> Url: https://github.com/boostorg/uuid/issues/60#issuecomment-375337383  

AMDG  
  
On 03/22/2018 08:55 AM, Marshall Clow wrote:  
> A better question is WHY you don't have a file `$BOOST/libs/uuid/include/boost/uuid/uuid.hpp`  
>   
> If you look here: https://github.com/boostorg/uuid/tree/master/include/boost/uuid , you should have it.  
>   
  
This is only in git.  The packaged release merges  
all the headers into boost/  
  
In Christ,  
Steven Watanabe

---

## Comment 3

> Username: jeking3  
> Created at: 2018-03-22 15:38:12 UTC  
> Url: https://github.com/boostorg/uuid/issues/60#issuecomment-375351644  

Is this a case of forgetting to run "b2 headers"?

---

## Comment 4

> Username: beddingfield  
> Created at: 2018-03-22 15:52:09 UTC  
> Url: https://github.com/boostorg/uuid/issues/60#issuecomment-375356808  

I am indeed running from the tarball, not from git.  
  
Running "b2 headers" from the root of the source tree does not seem to make a difference.

---

## Comment 5

> Username: mclow  
> Created at: 2018-03-22 17:13:09 UTC  
> Url: https://github.com/boostorg/uuid/issues/60#issuecomment-375385610  

Ok, then - in that case, you probably want to use (something like) `$(BOOST_ROOT)/boost/uuid/uuid.hpp` instead of a relative path.

---

## Comment 6

> Username: beddingfield  
> Created at: 2018-03-22 17:31:50 UTC  
> Url: https://github.com/boostorg/uuid/issues/60#issuecomment-375392575  

$(BOOST_ROOT) seems to work for me

---

## Comment 7

> Username: jeking3  
> Created at: 2018-03-22 21:24:38 UTC  
> Url: https://github.com/boostorg/uuid/issues/60#issuecomment-375462383  

That makes sense.  Thanks.  If you want to file a pull request the CI build will qualify it automatically....

---

## Comment 8

> Username: jeking3  
> Created at: 2018-03-26 15:12:09 UTC  
> Url: https://github.com/boostorg/uuid/issues/60#issuecomment-376201282  

@danieljames given this is a build-script issue and not a code change, is it okay to fix this in develop and move it to master for 1.67.0?

---

## Comment 9

> Username: danieljames  
> Created at: 2018-03-26 18:01:17 UTC  
> Url: https://github.com/boostorg/uuid/issues/60#issuecomment-376257104  

Sure, I'd leave it a few days to check the testers are okay, but it looks safe.

---

## Comment 10

> Username: jeking3  
> Created at: 2018-03-26 23:18:33 UTC  
> Url: https://github.com/boostorg/uuid/issues/60#issuecomment-376342769  

I am reopening this to track the results in the matrix.  After a few days if it looks good I will merge to master for 1.67.0.

---

## Comment 11

> Username: jeking3  
> Created at: 2018-03-28 17:10:36 UTC  
> Url: https://github.com/boostorg/uuid/issues/60#issuecomment-376963518  

@danieljames I have cherry-picked this specific fix into uuid master for 1.67.0.
