# #112 - Inconsistent runtime assertion usage of BOOST_ASSERT and assert [Closed]

> Username: ygeunkim  
> Created at: 2024-10-23 04:59:57 UTC  
> Updated at: 2024-10-23 18:46:18 UTC  
> Closed at: 2024-10-23 18:46:18 UTC  
> Url: https://github.com/boostorg/random/issues/112  

I noticed inconsistent usage of assertion in this random library.  
While most distributions  use `BOOST_ASSERT` or static assert, some distributions use the standard `assert`:  
  
- [beta_distribution.hpp](https://github.com/boostorg/random/blob/dcaee178a68566bbe50c5454a8ff3db67412055e/include/boost/random/beta_distribution.hpp#L51)  
- [hyperexponential_distribution.hpp](https://github.com/boostorg/random/blob/dcaee178a68566bbe50c5454a8ff3db67412055e/include/boost/random/hyperexponential_distribution.hpp#L269)  
  
Is there any specific reason to use `assert` here?  
  
FYI, I'm using `BOOST_ENABLE_ASSERT_HANDLER` to customize `BOOST_ASSERT` behavior when using this library.  
I cannot control the standard assert calls in the same way.  
  
Would it be possible to standardize `BOOST_ASSERT` for consistency and better assertion control of users?

---

## Comment 1

> Username: mborland  
> Created at: 2024-10-23 12:59:40 UTC  
> Url: https://github.com/boostorg/random/issues/112#issuecomment-2432074073  

> I noticed inconsistent usage of assertion in this random library. While most distributions use `BOOST_ASSERT` or static assert, some distributions use the standard `assert`:  
>   
> * [beta_distribution.hpp](https://github.com/boostorg/random/blob/dcaee178a68566bbe50c5454a8ff3db67412055e/include/boost/random/beta_distribution.hpp#L51)  
> * [hyperexponential_distribution.hpp](https://github.com/boostorg/random/blob/dcaee178a68566bbe50c5454a8ff3db67412055e/include/boost/random/hyperexponential_distribution.hpp#L269)  
>   
> Is there any specific reason to use `assert` here?  
>   
  
No. My guess is it was written by a different author and nobody had brought it up in the last decade since it was merged.  
  
> FYI, I'm using `BOOST_ENABLE_ASSERT_HANDLER` to customize `BOOST_ASSERT` behavior when using this library. I cannot control the standard assert calls in the same way.  
>   
> Would it be possible to standardize `BOOST_ASSERT` for consistency and better assertion control of users?  
  
If you submit a PR I will take a look and merge it. Otherwise I'll make one in a few days.
