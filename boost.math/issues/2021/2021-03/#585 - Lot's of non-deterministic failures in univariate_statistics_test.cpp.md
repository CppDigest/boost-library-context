# #585 - Lot's of non-deterministic failures in univariate_statistics_test.cpp [Closed]

> Username: jzmaddock  
> Created at: 2021-03-28 11:03:18 UTC  
> Updated at: 2021-05-03 16:58:26 UTC  
> Closed at: 2021-05-03 16:58:26 UTC  
> Url: https://github.com/boostorg/math/issues/585  

These seem to be related to gini calculation, I changed all the BOOST_TEST's to BOOST_TEST_LT so we could see how close the failures were, here's a typical run with msvc-14.2:  
  
```  
D:\data\boost\boost\libs\math\test\univariate_statistics_test.cpp(694): test 'abs(gini - expected) < Real(0.03)' ('0.0487317' < '0.03') failed in function 'void __cdecl test_gini_coefficient<float,const class std::execution::parallel_policy&>(const class std::execution::parallel_policy &)'  
D:\data\boost\boost\libs\math\test\univariate_statistics_test.cpp(694): test 'abs(gini - expected) < Real(0.03)' ('0.0593992' < '0.03') failed in function 'void __cdecl test_gini_coefficient<long double,const class std::execution::parallel_policy&>(const class std::execution::parallel_policy &)'  
D:\data\boost\boost\libs\math\test\univariate_statistics_test.cpp(659): test 'abs(gini - expected) < tol' ('0.666667' < '5.34553e-50') failed in function 'void __cdecl test_gini_coefficient<class boost::multiprecision::number<class boost::multiprecision::backends::cpp_bin_float<50,10,void,int,0,0>,0>,const class std::execution::parallel_policy&>(const class std::execution::parallel_policy &)'  
D:\data\boost\boost\libs\math\test\univariate_statistics_test.cpp(682): test 'abs(gini) < tol' ('0.222222' < '5.34553e-50') failed in function 'void __cdecl test_gini_coefficient<class boost::multiprecision::number<class boost::multiprecision::backends::cpp_bin_float<50,10,void,int,0,0>,0>,const class std::execution::parallel_policy&>(const class std::execution::parallel_policy &)'  
D:\data\boost\boost\libs\math\test\univariate_statistics_test.cpp(694): test 'abs(gini - expected) < Real(0.03)' ('0.144181' < '0.03') failed in function 'void __cdecl test_gini_coefficient<class boost::multiprecision::number<class boost::multiprecision::backends::cpp_bin_float<50,10,void,int,0,0>,0>,const class std::execution::parallel_policy&>(const class std::execution::parallel_policy &)'  
D:\data\boost\boost\libs\math\test\univariate_statistics_test.cpp(629): test 'abs(gini - 1) < tol' ('1' < '5.34553e-50') failed in function 'void __cdecl test_sample_gini_coefficient<class boost::multiprecision::number<class boost::multiprecision::backends::cpp_bin_float<50,10,void,int,0,0>,0>,const class std::execution::parallel_policy&>(const class std::execution::parallel_policy &)'  
6 errors detected.  
```  
  
However, the output is different every time!  Also note how large some of the errors are, something grievous is going on here, but I don't see it at present.

---

## Comment 1

> Username: mborland  
> Created at: 2021-03-28 11:46:31 UTC  
> Url: https://github.com/boostorg/math/issues/585#issuecomment-808885549  

I remember seeing similar egregious failures with GCC9 which used TBB instead of it's own implementation of the parallelism TS. TBB fails tsan horribly, so I wonder if something similar is going on with MSVC?

---

## Comment 2

> Username: jzmaddock  
> Created at: 2021-03-28 13:06:52 UTC  
> Url: https://github.com/boostorg/math/issues/585#issuecomment-808894690  

I don't know, but I went back through the history and the issue is still present at 1eb3c71f8a52e709fc4342d9e22d89f196c424ae which is the original #434 "Implement Policies in Statistics" commit.

---

## Comment 3

> Username: mborland  
> Created at: 2021-03-28 13:45:15 UTC  
> Url: https://github.com/boostorg/math/issues/585#issuecomment-808899279  

Can you please give PR #586 a try?

---

## Comment 4

> Username: jzmaddock  
> Created at: 2021-03-28 18:12:10 UTC  
> Url: https://github.com/boostorg/math/issues/585#issuecomment-808935189  

Looking at:  
  
```  
template<typename ReturnType, typename ExecutionPolicy, typename RandomAccessIterator>  
ReturnType gini_coefficient_parallel_impl(ExecutionPolicy&& exec, RandomAccessIterator first, RandomAccessIterator last)  
{  
    using Real = typename std::iterator_traits<RandomAccessIterator>::value_type;  
      
    ReturnType i = 1;  
    ReturnType num = 0;  
    ReturnType denom = 0;  
      
    std::for_each(exec, first, last, [&i, &num, &denom](const Real& val)  
    {  
        num = num + val * i;  
        denom = denom + val;  
        i = i + 1;  
    });  
  
    if(denom == 0)  
    {  
        return ReturnType(0);  
    }  
  
    return ((2*num)/denom - i)/(i-1);  
}  
```  
  
I have 2 comments:  
  
1) How is there *not* a race condition here accessing `num`, `denom` and `i` ?  
2) Does the order of the elements matter for the result?  I ask because the parallel for_each can call the functor in _any order_, so `i*val` will be different for each run.

---

## Comment 5

> Username: jzmaddock  
> Created at: 2021-03-29 10:36:42 UTC  
> Url: https://github.com/boostorg/math/issues/585#issuecomment-809273648  

OK, looking at this some more I think we have several issues:  
  
* Yes there really is a race condition on modifying the shared variables.  
* The input data must be sorted, but parallel std::for_each can call the functor in any order, which effectively "unsorts" it.  
* I'm not convinced that gcc/libstdc++ is actually running the tests in parallel: there's some complicated logic going on, but on mingw at least, the test code ends up in a sequential/serial for_each which is why the tests pass there.   More investigation needed there.  
  
I fear the only correct way to implement this as a parallel algorithm, is to divide the input range into N segments and create a future for each one, each of which returns a pair of partial sums, which can then be accumulated at the end.  Any other thoughts?  
  
We should probably review the other parallel algorithms for similar issues too.

---

## Comment 6

> Username: jzmaddock  
> Created at: 2021-03-29 10:52:26 UTC  
> Url: https://github.com/boostorg/math/issues/585#issuecomment-809282422  

A quick eyeball over the code found nothing else obvious.  @mborland , has everything (regression tests and performance tests) been run through clang's thread sanitizer just to be on the safe side?

---

## Comment 7

> Username: NAThompson  
> Created at: 2021-03-29 13:14:39 UTC  
> Url: https://github.com/boostorg/math/issues/585#issuecomment-809367428  

Do we have a threadsanitizer build in CI?

---

## Comment 8

> Username: jzmaddock  
> Created at: 2021-03-29 14:02:05 UTC  
> Url: https://github.com/boostorg/math/issues/585#issuecomment-809401970  

>Do we have a threadsanitizer build in CI?  
  
No, I know I added some sanitizer to the multiprecision CI tests, but we have nothing here yet I think?  
  
In general they're quite hard to run as CI jobs: either the VM rejects them or there are false positives.  But yes we should try and set something up.

---

## Comment 9

> Username: mborland  
> Created at: 2021-03-29 16:32:14 UTC  
> Url: https://github.com/boostorg/math/issues/585#issuecomment-809526623  

> I fear the only correct way to implement this as a parallel algorithm, is to divide the input range into N segments and create a future for each one, each of which returns a pair of partial sums, which can then be accumulated at the end. Any other thoughts?  
>   
> We should probably review the other parallel algorithms for similar issues too.  
  
The bulk of the other algorithms use exactly what you are describing. They would serve as an effective starting point.  
  
> A quick eyeball over the code found nothing else obvious. @mborland , has everything (regression tests and performance tests) been run through clang's thread sanitizer just to be on the safe side?  
  
In the original PR I ran everything through GCC's asan and tsan. Have not tried the Clang equivalent.  
  
> > Do we have a threadsanitizer build in CI?  
>   
> No, I know I added some sanitizer to the multiprecision CI tests, but we have nothing here yet I think?  
  
There is currently nothing in the CI to run asan/tsan/ubsan. Perhaps we could add a CircleCI config to only run these? The runs for GHA already take many times longer now that most of Boost has moved to it so I would recommend against adding more to GHA. GCC11 and Clang12 should be mainstream pretty soon here too...

---

## Comment 10

> Username: jzmaddock  
> Created at: 2021-03-29 18:26:14 UTC  
> Url: https://github.com/boostorg/math/issues/585#issuecomment-809609577  

Tentative CircleCI run added here: https://github.com/boostorg/math/pull/592  
  
This also adds doc building and inspection report run.

---

## Comment 11

> Username: jzmaddock  
> Created at: 2021-03-29 18:30:02 UTC  
> Url: https://github.com/boostorg/math/issues/585#issuecomment-809612191  

Haha, well CircleCI might not have been the best choice, apparently I have -24K free credits left!

---

## Comment 12

> Username: mborland  
> Created at: 2021-05-03 16:28:54 UTC  
> Url: https://github.com/boostorg/math/issues/585#issuecomment-831377460  

@jzmaddock This issue can be closed.
