# #117 - GCC doesn't properly deserialize from shared library base class pointers [Open]

> Username: drivehappy  
> Created at: 2018-09-14 03:14:33 UTC  
> Updated at: 2024-10-10 13:16:00 UTC  
> Url: https://github.com/boostorg/serialization/issues/117  

This issue started around release 1.61 - this has held us back from updating Boost for sometime, however I never had time to report it since it seemed fairly complex.  
  
I was able to narrow this down to a simple reproduce project, attached here: [reproduce.zip](https://github.com/boostorg/serialization/files/2381698/reproduce.zip)  
  
To reproduce:  
> $ make  
> $ LD_LIBRARY_PATH=. ./main  
> main: main.cpp:36: int main(): Assertion `basePtrA->test() == basePtrB->test()' failed.  
> Aborted (core dumped)  
  
  
This issue appears to be related to the combination of the following:  
- Executable serializes through a base class pointer, and then deserializes into another base class pointer. This also occurs with shared_ptr serialization.  
- GCC with no optimizations (-O0), I suspect this is actually caused by linker optimizations around symbols. Building under Clang/MSVC I couldn't reproduce this. This same behavior has been observed through GCC 5.x to 7.x.  
- Shared library that contains the abstract base class and derived class definitions, each class is marked as exported (visibility="default").  
- `-fvisibility=hidden` compile flag set for the executable. Setting this to "default" seemed to allow it to work.  
- Derived class deserialized through `boost::archive::binary_iarchive` presence seems to trigger this behavior.  I suspect this causes a symbol to be emitted which actually triggers the root problem.  
  
I suspect this is in an issue with symbol visibility, where compiling/linking under -O1 or higher optimizes out symbols. I'm unsure why this resolves the issue however and at this point I can't seem to root cause the problem.  
  
After playing around with `BOOST_SYMBOL_VISIBLE` in archive/serialization, adding it to the following appears to allow this to work correctly:  
- `class BOOST_SYMBOL_VISIBLE singleton` (boost/serialization/singleton.hpp)  
- `class BOOST_SYMBOL_VISIBLE iserializer : public basic_iserializer` (boost/archive/detail/iserializer.hpp)  
  
When I initially investigated this ~2 years ago I had bisected this problem down to the commit in [61b81fad](https://github.com/boostorg/serialization/commit/61b81fad). I noticed there had been several symbol changes since, so I'm not certain if this is actually the problem anymore.  
  
Due to the nature of it touching singleton and symbols, this _might_ be related to #104.

---

## Comment 1

> Username: drivehappy  
> Created at: 2018-09-14 03:23:50 UTC  
> Url: https://github.com/boostorg/serialization/issues/117#issuecomment-421219380  

I should also note that after haphazardly applying the `BOOST_SYMBOL_VISIBLE` to the `singleton` and `iserializer` above for the actual code this was failing under, I now get further but see similar assertion failures and stack traces described in #104.

---

## Comment 2

> Username: drivehappy  
> Created at: 2018-11-20 06:36:28 UTC  
> Url: https://github.com/boostorg/serialization/issues/117#issuecomment-440160907  

I pulled down the recent `develop` branch with the changes applied for #131. Initially it runs into the same problem as above, however after I apply the `BOOST_SYMBOL_VISIBLE` as mentioned in https://github.com/boostorg/serialization/issues/117#issuecomment-421219380, the attached test case now passes successfully in GCC.  
  
@robertramey: Is there any additional information you might need to track this down further? I can try to convert the attached test case over to an actual test, although I'm unfamiliar with the code base.

---

## Comment 3

> Username: robertramey  
> Created at: 2019-02-24 19:10:37 UTC  
> Url: https://github.com/boostorg/serialization/issues/117#issuecomment-466806573  

I believe we've resolved issues related to visibility.  can this be closed now?

---

## Comment 4

> Username: drivehappy  
> Created at: 2019-02-26 06:25:29 UTC  
> Updated at: 2019-02-26 06:27:55 UTC  
> Url: https://github.com/boostorg/serialization/issues/117#issuecomment-467312159  

@robertramey unfortunately this still looks like it's failing - although with the singleton fixes a few months ago it does get past the initial problem, but I think the main issue for this remains:  
  
1. I pulled down the `develop` branch at 68492c7 (for all of boost, 7adafcb for Boost.Serialization) (latest as of 2/25/19)  
2. Updated the reproduce project slightly (Makefile is improved) here: [reproduce_v2.zip](https://github.com/boostorg/serialization/files/2904009/reproduce_v2.zip)  
3. Updated the Makefile `BOOST_INC_DIR` and `BOOST_LIB_DIR` to point to the header and lib folders for the `develop` branch built.  
4. From the repro's folder:  
`make`  
`LD_LIBRARY_PATH=.:$BOOST_LIB_DIR ./main`  
5. Observe the repro's assertion failed:   
```main: main.cpp:38: int main(): Assertion `basePtrA->test() == basePtrB->test()' failed.```  
6. It seems if the visibility is added for the following, then the issue is fixed:  
    a. `class BOOST_SYMBOL_VISIBLE singleton` (boost/serialization/singleton.hpp)  
    b. `class BOOST_SYMBOL_VISIBLE iserializer : public basic_iserializer` (boost/archive/detail/iserializer.hpp)  
7. I would normally open a PR, however the steps in (6) were guess work, and I don't know _why_ it fails currently and why they resolve the issue.

---

## Comment 5

> Username: sehe  
> Created at: 2024-10-10 13:15:02 UTC  
> Updated at: 2024-10-10 13:16:00 UTC  
> Url: https://github.com/boostorg/serialization/issues/117#issuecomment-2405065078  

Randomly ran into this today and tried. Modified the reproducer only to show boost version and refactor the make file so I could grok the lines. [reproduce_sehe.zip](https://github.com/user-attachments/files/17328724/reproduce_sehe.zip)  
  
  
Tested fine for several releases:  
  
```  
sehe@workstation:~/Projects/stackoverflow/deps/boost_serialization_bug$ make -B && LD_LIBRARY_PATH=. ./main  
g++ -I../boost/ -fvisibility=hidden -g -fPIC -fPIC -shared base.cpp -L. -L../boost/stage/lib/ -lboost_serialization -o libbase.so  
g++ -I../boost/ -fvisibility=hidden -g -fPIC main.cpp -L. -L../boost/stage/lib/ -lboost_serialization -lbase -o main  
Boost 107800  
Valid:  
  basePtrA = 5  
  basePtrB = 5  
```  
  
```  
sehe@workstation:~/Projects/stackoverflow/deps/boost_serialization_bug$ make -B && LD_LIBRARY_PATH=. ./main  
g++ -I../boost/ -fvisibility=hidden -g -fPIC -fPIC -shared base.cpp -L. -L../boost/stage/lib/ -lboost_serialization -o libbase.so  
g++ -I../boost/ -fvisibility=hidden -g -fPIC main.cpp -L. -L../boost/stage/lib/ -lboost_serialization -lbase -o main  
Boost 108300  
Valid:  
  basePtrA = 5  
  basePtrB = 5  
```  
  
```  
sehe@workstation:~/Projects/stackoverflow/deps/boost_serialization_bug$ make -B && LD_LIBRARY_PATH=. ./main  
g++ -I../boost/ -fvisibility=hidden -g -fPIC -fPIC -shared base.cpp -L. -L../boost/stage/lib/ -lboost_serialization -o libbase.so  
g++ -I../boost/ -fvisibility=hidden -g -fPIC main.cpp -L. -L../boost/stage/lib/ -lboost_serialization -lbase -o main  
Boost 108600  
Valid:  
  basePtrA = 5  
  basePtrB = 5  
```
