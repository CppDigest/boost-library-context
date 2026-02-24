# #392 - C4242 warning at line 511 in serial_port_base.ipp [Open]

> Username: trekkie34  
> Created at: 2022-06-28 08:37:36 UTC  
> Updated at: 2022-06-28 12:49:15 UTC  
> Url: https://github.com/boostorg/asio/issues/392  

File asio\impl\serial_port_base.ipp generates warning C4242 at line 511 due to conversion between `unsigned int` and `BYTE`. I have observed this when compiling with Visual Studio 2019 in an MFC project.  
  
the original line:  
`  storage.ByteSize = value_;`  
  
the addition of a `static cast` fixes the issue in my local copy of the source code.  
`  storage.ByteSize = static_cast<BYTE>(value_);`  
  
Strictly speaking there is no run time bug, however this issue is troublesome for developers treating warnings as errors.
