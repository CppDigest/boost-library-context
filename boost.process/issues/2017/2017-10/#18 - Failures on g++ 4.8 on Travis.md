# #18 - Failures on g++ 4.8 on Travis [Closed]

> Username: pdimov  
> Created at: 2017-10-22 23:07:51 UTC  
> Updated at: 2017-12-19 20:46:06 UTC  
> Closed at: 2017-12-19 20:46:06 UTC  
> Url: https://github.com/boostorg/process/issues/18  

```  
./boost/process/detail/posix/sigchld_service.hpp: In member function ‘void boost::process::detail::posix::sigchld_service::_handle_signal(const boost::system::error_code&)’:  
./boost/process/detail/posix/sigchld_service.hpp:91:29: error: no matching function for call to ‘std::vector<std::pair<int, std::function<void(int, std::error_code)> > >::erase(__gnu_cxx::__normal_iterator<const std::pair<int, std::function<void(int, std::error_code)> >*, std::vector<std::pair<int, std::function<void(int, std::error_code)> > > >&)’  
         _receivers.erase(itr);  
                             ^  
```  
  
See https://travis-ci.org/boostorg/boost/jobs/291234390  
  
Apologies for enabling issues.

---

## Comment 1

> Username: klemens-morgenstern  
> Created at: 2017-10-23 12:33:30 UTC  
> Url: https://github.com/boostorg/process/issues/18#issuecomment-338643968  

Np, I guess I should just move the whole repository over here. Having it in two places is a pain.  
  
It's a compiler issue though, bc gcc 4.8 does not have the `erase(const_iterator)` implemented as seen here: https://godbolt.org/g/PWiCBm

---

## Comment 2

> Username: pdimov  
> Created at: 2017-10-23 14:36:56 UTC  
> Url: https://github.com/boostorg/process/issues/18#issuecomment-338680073  

I see. Are you willing to work around it, or are you not going to bother with 4.8? The superproject Travis jobs use the default g++ and it happens to be 4.8 at the moment.

---

## Comment 3

> Username: klemens-morgenstern  
> Created at: 2017-10-24 21:18:30 UTC  
> Url: https://github.com/boostorg/process/issues/18#issuecomment-339134842  

I added a workaround in 2c026e4 , wanna try?

---

## Comment 4

> Username: pdimov  
> Created at: 2017-10-24 21:21:58 UTC  
> Url: https://github.com/boostorg/process/issues/18#issuecomment-339135712  

I don't have g++ 4.8 handy but Travis will try it for me when it gets around to it and I'll let you know. :-)

---

## Comment 5

> Username: pdimov  
> Created at: 2017-10-25 12:01:26 UTC  
> Url: https://github.com/boostorg/process/issues/18#issuecomment-339307108  

g++ 4.8 looks happy now, thanks.  
  
https://travis-ci.org/boostorg/boost/builds/292311827  
  
clang still unhappy.  
  
```  
clang-linux.compile.c++.without-pth bin.v2/libs/process/test/extensions.test/clang-gnu-linux-3.9.0/debug/cxxstd-11/threadapi-pthread/extensions.o  
libs/process/test/extensions.cpp:80:38: error: pack expansion used as argument for non-pack parameter of alias template  
 void on_setup(ex::posix_executor<Args...>& exec) const  
 ^~~~~~~  
```  
  
https://travis-ci.org/boostorg/boost/jobs/292311835#L3632

---

## Comment 6

> Username: klemens-morgenstern  
> Created at: 2017-12-19 20:30:15 UTC  
> Url: https://github.com/boostorg/process/issues/18#issuecomment-352877494  

I forgot about that, does clang still cause problems?

---

## Comment 7

> Username: pdimov  
> Created at: 2017-12-19 20:46:06 UTC  
> Url: https://github.com/boostorg/process/issues/18#issuecomment-352881279  

Looking back at the builds, the last two for process are  
  
https://travis-ci.org/boostorg/boost/builds/297693689  
https://travis-ci.org/boostorg/boost/builds/300426781  
  
both of which pass.
