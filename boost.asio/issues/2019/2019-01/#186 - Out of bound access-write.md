# #186 - Out of bound access/write [Closed]

> Username: venjun2000  
> Created at: 2019-01-02 08:37:58 UTC  
> Updated at: 2020-12-30 00:58:40 UTC  
> Closed at: 2020-12-30 00:58:39 UTC  
> Url: https://github.com/boostorg/asio/issues/186  

Issue location :   
  
https://github.com/boostorg/asio/blob/boost-1.61.0/include/boost/asio/generic/detail/endpoint.hpp#L120  
Out-of-bounds write    /DeliSpace/TegraP1Lin.Rel/External/Boost/inc/boost/asio/local/detail/impl/endpoint.ipp    init  
  
https://github.com/boostorg/asio/blob/boost-1.61.0/include/boost/asio/generic/detail/impl/endpoint.ipp#L87  
Out-of-bounds access    DeliSpace/TegraP1Lin.Rel/External/Boost/inc/boost/asio/generic/detail/impl/endpoint.ipp    init  
  
  
Coverity cathces and reports this as a major exception. Please advise..

---

## Comment 1

> Username: venjun2000  
> Created at: 2019-01-03 06:06:29 UTC  
> Url: https://github.com/boostorg/asio/issues/186#issuecomment-451061949  

https://github.com/boostorg/asio/blob/boost-1.61.0/include/boost/asio/local/detail/impl/endpoint.ipp#L102  
  
void endpoint::init(const char* path_name, std::size_t path_length)  
{  
**1. Condition path_length > 107UL /* sizeof (this->data_.local.sun_path) - 1 */, taking true branch.  
2. cond_at_least: Checking path_length > 107UL implies that path_length is at least 108 on the true branch.**  
  if (path_length > sizeof(data_.local.sun_path) - 1)  
  {  
    // The buffer is not large enough to store this address.  
    boost::system::error_code ec(boost::asio::error::name_too_long);  
    boost::asio::detail::throw_error(ec);  
  }  
  using namespace std; // For memcpy.  
  data_.local = boost::asio::detail::sockaddr_un_type();  
  data_.local.sun_family = AF_UNIX;  
CID 5279614: Copy into fixed size buffer (BUFFER_SIZE) [select issue]  
  memcpy(data_.local.sun_path, path_name, path_length);  
  path_length_ = path_length;  
  // NUL-terminate normal path names. Names that start with a NUL are in the  
  // UNIX domain protocol's "abstract namespace" and are not NUL-terminated.  
**3. Condition path_length > 0, taking true branch.  
4. Condition this->data_.local.sun_path[0] == 0, taking true branch.**  
  if (path_length > 0 && data_.local.sun_path[0] == 0)  
CID 5279356 (#1 of 1): Out-of-bounds write (OVERRUN)  
**5. overrun-local: Overrunning array this->data_.local.sun_path of 108 bytes at byte offset 108 using index path_length (which evaluates to 108).**  
    data_.local.sun_path[path_length] = 0;  
}

---

## Comment 2

> Username: venjun2000  
> Created at: 2019-01-03 06:20:41 UTC  
> Updated at: 2019-01-03 07:53:55 UTC  
> Url: https://github.com/boostorg/asio/issues/186#issuecomment-451063522  

https://github.com/boostorg/asio/blob/boost-1.61.0/include/boost/asio/generic/detail/impl/endpoint.ipp#L87  
  
 87void endpoint::init(const void* sock_addr,  
 88    std::size_t sock_addr_size, int sock_protocol)  
 89{  
   	**1. Condition sock_addr_size > 128UL /* sizeof (boost::asio::detail::sockaddr_storage_type) */, taking true branch.  
   	2. cond_at_least: Checking sock_addr_size > 128UL implies that sock_addr_size is at least 129 on the true branch.**  
 90  if (sock_addr_size > sizeof(boost::asio::detail::sockaddr_storage_type))  
 91  {  
 92    boost::system::error_code ec(boost::asio::error::invalid_argument);  
 93    boost::asio::detail::throw_error(ec);  
 94  }  
 95  
 96  using namespace std; // For memset and memcpy.  
   	**3. write_constant: Write the value 0 into this->data_.generic.ss_family.  
   	4. write_constant: Write the value 0 into this->data_.generic.__ss_align.**  
 97  memset(&data_.generic, 0, sizeof(boost::asio::detail::sockaddr_storage_type));  
   	  
**CID 5279357 (#1 of 1): Out-of-bounds access (OVERRUN)  
5. overrun-buffer-arg: Overrunning struct type _ZN5boost4asio6detail21sockaddr_storage_typeE of 128 bytes by passing it to a function which accesses it at byte offset 128 using argument sock_addr_size (which evaluates to 129).**  
 98  memcpy(&data_.generic, sock_addr, sock_addr_size);  
 99  
100  size_ = sock_addr_size;  
101  protocol_ = sock_protocol;  
102}

---

## Comment 3

> Username: vinniefalco  
> Created at: 2019-01-06 15:03:49 UTC  
> Url: https://github.com/boostorg/asio/issues/186#issuecomment-451748329  

Do you have a minimal, compiling program that causes the coverity error?

---

## Comment 4

> Username: ghost  
> Created at: 2020-12-30 00:58:38 UTC  
> Url: https://github.com/boostorg/asio/issues/186#issuecomment-752290923  

This issue was moved by [chriskohlhoff](https://github.com/chriskohlhoff) to [chriskohlhoff/asio#662](https://github.com/chriskohlhoff/asio/issues/662).
