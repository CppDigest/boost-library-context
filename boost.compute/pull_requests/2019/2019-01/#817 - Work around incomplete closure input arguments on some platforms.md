# #817 Work around incomplete closure input arguments on some platforms [Closed]

> Username: rosenrodt  
> Created at: 2019-01-22 01:01:04 UTC  
> Updated at: 2020-07-27 13:53:47 UTC  
> Closed at: 2020-07-27 13:53:47 UTC  
> Url: https://github.com/boostorg/compute/pull/817  

This fixes all the kernel compile errors I encountered when running `test_closure`, one example:  
  
```  
--- build log ---  
BC-src-code:6:75: error: use of undeclared identifier 'pi'  
 inline float add_two_and_pi(float x, int two, float  p){ return x + two + pi; }  
                                                                           ^  
1 diagnostic(s) generated.  
```  
  
I encountered this curious case when cross-compiling with the standalone toolchain generated from [android NDK](https://developer.android.com/ndk/guides/standalone_toolchain). Most tests pass. But closure is among one of the few that fails. It seems like boost::trim() from the main boost library somehow cuts off more character than it should and also leaves a leading whitespace.   
  
```log  
two, pi          // original string     
[two,  pi]       // vector after split()  
  
two              // after trim()  
int two          // capture_traits<T>::type_name() << " " << variable_name  
  
 p               // after trim(), there is a leading space before 'pi' and 'i' is cut off   
float  p         // capture_traits<T>::type_name() << " " << variable_name  
```

---

## Comment 1

> Username: jszuppe  
> Created_at: 2019-01-22 09:11:12 UTC  
> Url: https://github.com/boostorg/compute/pull/817#issuecomment-456324307  

That's odd that such an old function works incorrectly (`boost::trim()`). You should report that to https://github.com/boostorg/algorithm.

---

## Comment 2

> Username: coveralls  
> Created_at: 2019-01-22 12:13:13 UTC  
> Updated_at: 2019-02-20 07:02:06 UTC  
> Url: https://github.com/boostorg/compute/pull/817#issuecomment-456378153  

[![Coverage Status](https://coveralls.io/builds/21736372/badge)](https://coveralls.io/builds/21736372)  
  
Coverage decreased (-0.002%) to 84.017% when pulling **73d978b0846f533936c40ba8ecd6d9c3341e201f on rosenrodt:fix-closure-argument-cutoff** into **36c89134d4013b2e5e45bc55656a18bd6141995a on boostorg:develop**.

---

## Comment 3

> Username: rosenrodt  
> Created_at: 2019-01-28 03:22:30 UTC  
> Url: https://github.com/boostorg/compute/pull/817#issuecomment-457987908  

Thank @jszuppe. I have opened a ticket here see how this goes https://github.com/boostorg/algorithm/issues/58

---
