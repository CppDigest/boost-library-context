# #54 - Linking error to advapi32 when using BOOST_AUTO_LINK_SYSTEM [Open]

> Username: rlpires  
> Created at: 2019-04-04 14:15:46 UTC  
> Updated at: 2019-04-04 14:15:46 UTC  
> Url: https://github.com/boostorg/random/issues/54  

Under Windows, the code in random_device.cpp automatically links to the library advapi32.lib.  
This uses the auto_link.hpp header combined with the macro BOOST_AUTO_LINK_NOMANGLE to produce the correct library file name to link.  
  
As of version 1.69, it is also possible to use the macro BOOST_AUTO_LINK_SYSTEM, which is exclusive with the NOMANGLE macro.  
Using the former causes a link error as the library file name added by auto_link is incorrect.  
I would suggest undefining the BOOST_AUTO_LINK_SYSTEM before including the auto_link header in random_device.cpp.  
  
https://github.com/boostorg/random/blob/ed7ab807e6f9c8e99f380cd21f5a00058b985d49/src/random_device.cpp#L51
