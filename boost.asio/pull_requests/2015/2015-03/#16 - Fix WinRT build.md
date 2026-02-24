# #16 Fix WinRT build [Closed]

> Username: john-peterson  
> Created at: 2015-03-09 20:47:28 UTC  
> Updated at: 2017-12-02 07:53:38 UTC  
> Closed at: 2017-12-02 07:53:37 UTC  
> Url: https://github.com/boostorg/asio/pull/16  

should boostorg/build be fixed or is this correct?  
## WinRT detection  
  
with this build command and with /ZW https://github.com/boostorg/build/pull/62 WINAPI_FAMILY_ONE_PARTITION(WINAPI_FAMILY, WINAPI_PARTITION_APP) is false  
  
```  
git clone --depth=1 --no-single-branch http://github.com/boostorg/boost.git .  
git checkout boost-1.57.0  
git submodule update --init  
call "%VS120COMNTOOLS%..\..\vc\vcvarsall.bat" amd64  
bootstrap.bat  
b2 -j8 toolset=msvc-12.0 address-model=64 variant=release,debug link=shared runtime-link=shared windows-api=store  
```  
  
the macro is in https://github.com/jp9000/OBS/blob/master/extras/winapifamily.h. the detection is discussed in https://svn.boost.org/trac/boost/ticket/9195

---

## Comment 1

> Username: john-peterson  
> Created_at: 2015-03-20 15:33:20 UTC  
> Url: https://github.com/boostorg/asio/pull/16#issuecomment-84050250  

## name conflict  
  
the name s_addr is already taken https://msdn.microsoft.com/en-us/library/windows/hardware/ff556972(v=vs.85).aspx  
  
```  
D:\file\code\lib\boost\boost/asio/detail/socket_types.hpp(96) : error C2143: syntax error : missing ';' before '.'  
```  
  
this is fixed http://stackoverflow.com/questions/19355634/boost-c-libraries-generating-strange-errors with  
  
```  
#undef s_addr  
```

---

## Comment 2

> Username: chriskohlhoff  
> Created_at: 2017-12-02 07:53:37 UTC  
> Url: https://github.com/boostorg/asio/pull/16#issuecomment-348675731  

Closing as this no longer seems to be applicable.

---
