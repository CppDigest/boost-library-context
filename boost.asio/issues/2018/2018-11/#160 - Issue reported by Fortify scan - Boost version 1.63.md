# #160 - Issue reported by Fortify scan - Boost version 1.63 [Closed]

> Username: TejasSonawan  
> Created at: 2018-11-01 18:05:28 UTC  
> Updated at: 2020-12-30 00:56:31 UTC  
> Closed at: 2020-12-30 00:56:30 UTC  
> Url: https://github.com/boostorg/asio/issues/160  

Static code analyser tool "Fortify" found critical error in boost/asio in following file  
  
Lib/boost/boost/asio/detail/impl/socket_ops.ipp : 2198  
  
Summary  
The function inet_pton() in socket_ops.ipp writes outside the bounds of base on line 2198, which could corrupt data, cause the program to crash, or lead to the execution of malicious code.The program writes outside the bounds of allocated memory, which could corrupt data, crash the program, or lead to the execution of malicious code.  
  
union  
  {  
    socket_addr_type base;  
    sockaddr_storage_type storage;  
    sockaddr_in4_type v4;  
    sockaddr_in6_type v6;  
  } address;  
  int address_length = sizeof(sockaddr_storage_type);  
#if defined(BOOST_NO_ANSI_APIS) || (defined(_MSC_VER) && (_MSC_VER >= 1800))  
  int num_wide_chars = static_cast<int>(strlen(src)) + 1;  
  LPWSTR wide_buffer = (LPWSTR)_alloca(num_wide_chars * sizeof(WCHAR));  
  ::MultiByteToWideChar(CP_ACP, 0, src, -1, wide_buffer, num_wide_chars);  
  int result = error_wrapper(::WSAStringToAddressW(  
        wide_buffer, af, 0, &address.base, &address_length), ec);  
#else  
  int result = error_wrapper(::WSAStringToAddressA(  
        const_cast<char*>(src), af, 0, &address.base, &address_length), ec);  
#endif  
  
------------  
By modifying the below line the error disappear.  
  
Original Line  
int address_length = sizeof(sockaddr_storage_type);  
  
changed to   
int address_length = sizeof(socket_addr_type);  
  
Could you please check and do the correction?

---

## Comment 1

> Username: youngwolf-project  
> Created at: 2018-11-05 05:35:54 UTC  
> Updated at: 2018-11-05 05:38:41 UTC  
> Url: https://github.com/boostorg/asio/issues/160#issuecomment-435760297  

I feel that 'int address_length = sizeof(sockaddr_storage_type);' is just to get the max size of the four members in union address, and sockaddr_storage_type should be the biggest one.  
If so, then original code is right, but I feel that we should change the code like this:  
int address_length = sizeof(address);

---

## Comment 2

> Username: ghost  
> Created at: 2020-12-30 00:56:29 UTC  
> Url: https://github.com/boostorg/asio/issues/160#issuecomment-752290530  

This issue was moved by [chriskohlhoff](https://github.com/chriskohlhoff) to [chriskohlhoff/asio#648](https://github.com/chriskohlhoff/asio/issues/648).
