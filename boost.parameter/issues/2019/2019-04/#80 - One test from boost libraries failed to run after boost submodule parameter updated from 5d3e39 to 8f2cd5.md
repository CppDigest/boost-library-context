# #80 - One test from boost libraries failed to run after boost submodule parameter updated from 5d3e39 to 8f2cd5 [Closed]

> Username: spacelg  
> Created at: 2019-04-29 10:24:29 UTC  
> Updated at: 2021-12-08 15:48:58 UTC  
> Closed at: 2021-12-08 15:48:57 UTC  
> Url: https://github.com/boostorg/parameter/issues/80  

Environment:  
VS 2017 + Windows Server 2016  
  
Issue description:  
One test from boost libraries failed to run after boost submodule parameter updated from 5d3e39 to 8f2cd5  [boost/parameter@7b12552](https://github.com/boostorg/boost/commit/7b125522d8f1ce00a253c09fe16450a7acc043df).Could you please take a look?  
  
Reproduce steps:.  
1. git clone -c core.autocrlf=true --recursive ​https://github.com/boostorg/boost.git D:\Boost\src  
2. open a VS 2017 x64 command prompt and browse to D:\Boost\src  
3. .\bootstrap  
4. .\b2 headers variant=release --build-dir=..\out\x64rel address-model=64  
5. .\b2 variant=release --build-dir=..\out\x64rel address-model=64  
6. .\b2 -j4 variant=release --build-dir=..\out\x64rel libs\accumulators\test  
  
[log_x64_test_1.log](https://github.com/boostorg/parameter/files/3126973/log_x64_test_1.log)  
  
ErrorMessage:  
.\boost/parameter/aux_/arg_list.hpp(312): error C2664: 'boost::parameter::aux::tagged_argument_list_of_1<boost::parameter::aux::tagged_argument<Keyword,std::vector<double,std::allocator<_Ty>>>>::tagged_argument_list_of_1(const boost::parameter::aux::tagged_argument_list_of_1<boost::parameter::aux::tagged_argument<Keyword,std::vector<_Ty,std::allocator<_Ty>>>> &)': cannot convert argument 1 from 'const A1' to 'std::vector<double,std::allocator<_Ty>> &'  
.\boost/parameter/keyword.hpp(66): error C2039: 'qualifier': is not a member of 'boost::accumulators::tag::extended_p_square_probabilities_<0>'  
.\boost/parameter/keyword.hpp(98): error C2062: type 'unknown-type' unexpected  
.\boost/parameter/keyword.hpp(100): error C2334: unexpected token(s) preceding '{'; skipping apparent function body

---

## Comment 1

> Username: CromwellEnage  
> Created at: 2019-04-29 22:11:21 UTC  
> Url: https://github.com/boostorg/parameter/issues/80#issuecomment-487762203  

I'll take a look later this evening.

---

## Comment 2

> Username: CromwellEnage  
> Created at: 2019-04-30 03:38:16 UTC  
> Url: https://github.com/boostorg/parameter/issues/80#issuecomment-487812565  

This issue should be resolved once boostorg/accumulators#30 is approved and merged, but let me know if otherwise.

---

## Comment 3

> Username: QuellaZhang  
> Created at: 2019-06-27 08:17:49 UTC  
> Url: https://github.com/boostorg/parameter/issues/80#issuecomment-506243877  

Hello, when this fixes will be merged into master branch? It failed for a long time.

---

## Comment 4

> Username: CromwellEnage  
> Created at: 2019-06-27 09:59:02 UTC  
> Url: https://github.com/boostorg/parameter/issues/80#issuecomment-506280548  

@QuellaZhang, please forward your request to the maintainers of Boost.Accumulators, as they control that Github repository.

---

## Comment 5

> Username: QuellaZhang  
> Created at: 2019-07-03 06:48:14 UTC  
> Url: https://github.com/boostorg/parameter/issues/80#issuecomment-507963805  

Thank you for your reminder, we will.

---

## Comment 6

> Username: Lastique  
> Created at: 2021-12-08 15:48:57 UTC  
> Url: https://github.com/boostorg/parameter/issues/80#issuecomment-988933496  

Boost.Accumulators PR was merged. Closing as there seems to be nothing left to do in Boost.Parameter.
