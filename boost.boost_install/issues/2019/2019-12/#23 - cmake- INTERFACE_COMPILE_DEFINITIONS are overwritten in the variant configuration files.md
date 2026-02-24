# #23 - cmake: INTERFACE_COMPILE_DEFINITIONS are overwritten in the variant configuration files [Closed]

> Username: david-sinuela-pix4d  
> Created at: 2019-12-12 09:43:31 UTC  
> Updated at: 2019-12-13 14:49:35 UTC  
> Closed at: 2019-12-13 14:49:21 UTC  
> Url: https://github.com/boostorg/boost_install/issues/23  

There is a conflict between the common config file and the variants. The `INTERFACE_COMPILE_DEFINITIONS` set in:  
  
https://github.com/boostorg/boost_install/blob/8ca34abf2ce90dc6f90123b6d4fbde096e21f75e/boost-install.jam#L623-L625  
  
are overwritten in the variant files in:  
  
https://github.com/boostorg/boost_install/blob/8ca34abf2ce90dc6f90123b6d4fbde096e21f75e/boost-install.jam#L368-L370  
  
This does not show up as a bug because most targets depend on `Boost::headers` and they will get `BOOST_ALL_NO_LIB` transitively from it. On the other hand some libraries do not depend on Boost::headers (I could see `Boost::math` an `Boost::exception`).  
  
I am not sure if the intention was to extend or overwrite but it sounds more logical to me to provide common properties in the common config file and specialize (extend) in the variant files. In any case I noticed this when I was patching the common file and the extra settings did not appear in the final build.  
  
I will start a PR with a fix.

---

## Comment 1

> Username: pdimov  
> Created at: 2019-12-13 14:49:35 UTC  
> Url: https://github.com/boostorg/boost_install/issues/23#issuecomment-565468201  

Thanks.
