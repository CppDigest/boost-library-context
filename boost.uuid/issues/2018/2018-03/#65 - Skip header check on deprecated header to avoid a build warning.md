# #65 - Skip header check on deprecated header to avoid a build warning [Closed]

> Username: jeking3  
> Created at: 2018-03-27 12:34:15 UTC  
> Updated at: 2018-05-08 17:50:26 UTC  
> Closed at: 2018-05-08 17:50:26 UTC  
> Url: https://github.com/boostorg/uuid/issues/65  

See: http://www.boost.org/development/tests/develop/developer/uuid.html  
```  
Warnings: teeks99-02-dc5-98-Docker-64on64 - uuid / clang-linux-5.0~c++98  
Rev 399d61cd34a3a573f1cc083115dd793e68c11cd1 / Wed, 21 Mar 2018 23:55:58 +0000  
~hdr-decl-sha1~hpp  
../boost/uuid/sha1.hpp:13:9: warning: This header is implementation detail and provided for backwards compatibility. [-W#pragma-messages]  
1 warning generated.  
```
