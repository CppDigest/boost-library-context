# #202 cmake install tests in drone [Merged]

> Username: alandefreitas  
> Created at: 2022-06-15 20:43:40 UTC  
> Updated at: 2022-08-03 21:07:28 UTC  
> Merged at: 2022-06-16 00:52:25 UTC  
> Closed at: 2022-06-16 00:52:25 UTC  
> Url: https://github.com/boostorg/url/pull/202  



---

## Comment 1

> Username: sdarwin  
> Created_at: 2022-06-15 21:15:29 UTC  
> Url: https://github.com/boostorg/url/pull/202#issuecomment-1156954117  

In the first test "Install Library" notice it goes "cd ../../../../.." which leads to BOOST_ROOT.  The other job cmake-superproject is  building in that location.   To be consistent, this test should be moved over to the cmake-superproject job.   Or if you prefer, delete cmake-superproject from .drone.star and .drone.sh and move all cmake tests into cmake1.  Either way.  
  
The second test labelled "CMake install tests" seems to be consistent with boost-ci.    Not found in boostorg/core .   if this should be included, ok.

---

## Comment 2

> Username: sdarwin  
> Created_at: 2022-06-15 21:36:40 UTC  
> Url: https://github.com/boostorg/url/pull/202#issuecomment-1156969545  

Another comment about "CMake install tests" - it's using the BOOST_CI_INSTALL_TEST variable which is defined in test/cmake_test/CMakeLists.txt.     If you are referencing BOOST_CI_INSTALL_TEST and expect it to have an effect, then the test should be run from the test/cmake_test directory, right?   Change  `cd ../libs/$SELF` to  `cd ../libs/$SELF/cmake_test`.

---

## Comment 3

> Username: alandefreitas  
> Created_at: 2022-06-15 22:04:31 UTC  
> Url: https://github.com/boostorg/url/pull/202#issuecomment-1156991868  

> In the first test "Install Library" notice it goes "cd ../../../../.." which leads to BOOST_ROOT.   
  
I tried `cd $BOOST_ROOT` at first but that didn't work (it executed only `cd `). `$SELF` works though.  
  
Then I noticed [`cd ../test/cmake_test`](https://github.com/CPPAlliance/url/blob/e80c4217319bcd5dd64c538ee20775fd9557e8f8/.drone/drone.sh#L195) instead of `cd "$cmake_test_folder"` (or `cd $BOOST_ROOT/libs/$SELF/test/cmake_test`) in the other script. I assumed that was just how things worked in this drone script.  
  
Is that wrong?  
  
> The other job cmake-superproject is building in that location. To be consistent, this test should be moved over to the cmake-superproject job.   
> Or if you prefer, delete cmake-superproject from .drone.star and .drone.sh and move all cmake tests into cmake1. Either way.  
  
All of these tests come after   
  
```bash  
elif [ "$DRONE_JOB_BUILDTYPE" == "cmake1" ]; then  
```  
  
including #201, which we just pushed.   
  
In the first case, should we move all of them, including #201 inside the `elif [ "$DRONE_JOB_BUILDTYPE" == "cmake-superproject" ]; then` block then?  
  
I'll try to do whatever looks more similar to the github scripts.  
  
> Not found in boostorg/core . if this should be included, ok.  
  
I'm not sure I understand this comment.

---

## Comment 4

> Username: alandefreitas  
> Created_at: 2022-06-15 22:40:00 UTC  
> Url: https://github.com/boostorg/url/pull/202#issuecomment-1157019101  

> Another comment about "CMake install tests" - it's using the BOOST_CI_INSTALL_TEST variable which is defined in test/cmake_test/CMakeLists.txt. If you are referencing BOOST_CI_INSTALL_TEST and expect it to have an effect, then the test should be run from the test/cmake_test directory, right? Change cd ../libs/$SELF to cd ../libs/$SELF/cmake_test.  
  
Fixed :+1:

---

## Comment 5

> Username: sdarwin  
> Created_at: 2022-06-15 23:39:12 UTC  
> Url: https://github.com/boostorg/url/pull/202#issuecomment-1157076763  

> should we move all of them into the cmake-superproject block?  
  
The test that runs "cd ../../../../.." is going to boost-root, which is by definition "the superproject".  That particular test could be moved to the cmake-superproject block, to be consistent.  Or (the other option mentioned, in the earlier comment)  
  
> I'm not sure I understand this comment "not found in boostorg/core".  
  
There are multiple sources of CI to use as inspiration.   To copy from.  I was saying that you are copying boost-ci and the test was "not found in boostorg/core".

---
