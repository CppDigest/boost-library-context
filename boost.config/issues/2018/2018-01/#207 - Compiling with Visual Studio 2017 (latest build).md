# #207 - Compiling with Visual Studio 2017 (latest build) [Closed]

> Username: ajtruckle  
> Created at: 2018-01-10 16:44:48 UTC  
> Updated at: 2018-04-03 08:32:01 UTC  
> Closed at: 2018-01-10 18:20:02 UTC  
> Url: https://github.com/boostorg/config/issues/207  

I get this compiler warning:  
  
> 5>Info: Boost.Config is older than your compiler version - probably nothing bad will happen - but you may wish to look for an update Boost version.  Define BOOST_CONFIG_SUPPRESS_OUTDATED_MESSAGE to suppress this message.  
  
I am using the latest version of Boost. VS2017 is 15.5.3. How do I fix?  
  
Thanks.

---

## Comment 1

> Username: jzmaddock  
> Created at: 2018-01-10 18:20:02 UTC  
> Url: https://github.com/boostorg/config/issues/207#issuecomment-356690325  

Yup, the last Boost version and msvc 15.5.3 were released at about the same time, so we didn't manage to get the many updates tested and in that release.  
You can either:  
* Do as the message suggests and define BOOST_CONFIG_SUPPRESS_OUTDATED_MESSAGE.  Everything will work just fine, but some features which could be enabled for 15.5 will still be disabled as they were in 15.4.  
* Download the zip file of Boost.Config develop and replace boost-root/boost/config with the files under include/boost/config in the zip.

---

## Comment 2

> Username: wtom76  
> Created at: 2018-04-03 08:32:00 UTC  
> Url: https://github.com/boostorg/config/issues/207#issuecomment-378171585  

> Download the zip file of Boost.Config develop and replace boost-root/boost/config with the files under include/boost/config in the zip.  
  
and revert config-develop\include\boost\version.hpp to version 66?
