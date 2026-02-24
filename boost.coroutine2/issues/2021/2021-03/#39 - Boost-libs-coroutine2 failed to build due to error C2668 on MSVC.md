# #39 - Boost\libs\coroutine2 failed to build due to error C2668 on MSVC. [Closed]

> Username: QuellaZhang  
> Created at: 2021-03-16 07:08:51 UTC  
> Updated at: 2021-10-02 20:36:46 UTC  
> Closed at: 2021-10-02 20:36:46 UTC  
> Url: https://github.com/boostorg/coroutine2/issues/39  

Issue description:  
Boost\libs\coroutine2 failed to build due to error C2668 on MSVC. Could you please take a look? We ues  boostorg/boost@ea9f9fb from Boost master branch. from Boost master branch.  
  
**Build step:**  
  
1. git clone -c core.autocrlf=true --recursive ​https://github.com/boostorg/boost.git boost  
2. open a VS 2019 x64 command prompt and browse to boost  
3. .\bootstrap  
4. .\b2 headers variant=release --build-dir=..\out\x64rel address-model=64  
5. .\b2 variant=release --build-dir=..\out\x64rel address-model=64  
6. .\b2 -j4 variant=release --build-dir=..\out\x64rel libs\coroutine2\test  
  
**Error info:**  
test_coroutine.cpp  
libs\coroutine2\test\test_coroutine.cpp(538): error C2668: 'std::begin': ambiguous call to overloaded function  
.\boost/coroutine2/detail/pull_coroutine.hpp(301): note: could be 'boost::coroutines2::detail::pull_coroutine<T>::iterator std::begin<T>(boost::coroutines2::detail::pull_coroutine<T> &)' [found using argument-dependent lookup]  
        with  
        [  
            T=int  
        ]  
.\boost/coroutine2/detail/pull_coroutine.hpp(285): note: or       'boost::coroutines2::detail::pull_coroutine<T>::iterator boost::coroutines2::detail::begin<T>(boost::coroutines2::detail::pull_coroutine<T> &)' [found using argument-dependent lookup]  
        with  
        [  
            T=int  
        ]  
libs\coroutine2\test\test_coroutine.cpp(539): note: while trying to match the argument list '(boost::coroutines2::detail::pull_coroutine<T>)'  
        with  
        [  
            T=int  
        ]  
libs\coroutine2\test\test_coroutine.cpp(538): error C2668: 'std::end': ambiguous call to overloaded function  
.\boost/coroutine2/detail/pull_coroutine.hpp(307): note: could be 'boost::coroutines2::detail::pull_coroutine<T>::iterator std::end<T>(boost::coroutines2::detail::pull_coroutine<T> &)' [found using argument-dependent lookup]  
        with  
        [  
            T=int  
        ]

---

## Comment 1

> Username: jwakely  
> Created at: 2021-08-11 10:57:14 UTC  
> Url: https://github.com/boostorg/coroutine2/issues/39#issuecomment-896721697  

This is a dup of #42
