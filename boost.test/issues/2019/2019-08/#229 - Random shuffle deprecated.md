# #229 - Random shuffle deprecated [Closed]

> Username: NAThompson  
> Created at: 2019-08-21 11:08:18 UTC  
> Updated at: 2020-01-07 06:44:04 UTC  
> Closed at: 2019-10-21 18:27:53 UTC  
> Url: https://github.com/boostorg/test/issues/229  

On clang 6, on [this build](https://travis-ci.org/boostorg/math/jobs/574293707), we have  
  
```  
In file included from ../../../libs/test/src/framework.cpp:16:  
  
../../../boost/test/impl/framework.ipp:784:30: warning: 'random_shuffle<std::__1::__wrap_iter<unsigned long *>, const boost::unit_test::framework::state::random_generator_helper &>' is deprecated [-Wdeprecated-declarations]  
  
                        std::random_shuffle( children_with_the_same_rank.begin(), children_with_the_same_rank.end(), rand_gen );  
  
                             ^  
  
/Applications/Xcode-11.0.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain/usr/bin/../include/c++/v1/algorithm:3003:1: note: 'random_shuffle<std::__1::__wrap_iter<unsigned long *>, const boost::unit_test::framework::state::random_generator_helper &>' has been explicitly marked deprecated here  
  
_LIBCPP_DEPRECATED_IN_CXX14 void  
```  
  
Low priority, obviously.

---

## Comment 1

> Username: raffienficiaud  
> Created at: 2019-10-07 22:35:03 UTC  
> Url: https://github.com/boostorg/test/issues/229#issuecomment-539232974  

Thanks for the report!  
Removing completely the call to `std::random_shuffle` in favour of the drop-in (and required) replacement. Testing now.

---

## Comment 2

> Username: raffienficiaud  
> Created at: 2019-10-21 18:27:53 UTC  
> Url: https://github.com/boostorg/test/issues/229#issuecomment-544645575  

In master, closing.
