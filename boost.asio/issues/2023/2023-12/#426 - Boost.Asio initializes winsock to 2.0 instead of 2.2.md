# #426 - Boost.Asio initializes winsock to 2.0 instead of 2.2 [Closed]

> Username: jcelerier  
> Created at: 2023-12-21 04:03:39 UTC  
> Updated at: 2024-04-15 18:12:20 UTC  
> Closed at: 2024-04-15 18:12:20 UTC  
> Url: https://github.com/boostorg/asio/issues/426  

https://github.com/boostorg/asio/blob/2e49b21732e5d1bb3bb98f209164c30816b0bc79/include/boost/asio/detail/winsock_init.hpp#L51C1-L51C1  
  
Winsock 2.2 has apparently been available since Windows 95. Would it be possible to raise the version?

---

## Comment 1

> Username: xavier2k6  
> Created at: 2024-02-27 10:54:53 UTC  
> Url: https://github.com/boostorg/asio/issues/426#issuecomment-1966288472  

https://github.com/boostorg/asio/blob/2e49b21732e5d1bb3bb98f209164c30816b0bc79/include/boost/asio/detail/winsock_init.hpp#L51-L52

---

## Comment 2

> Username: xavier2k6  
> Created at: 2024-04-15 15:50:22 UTC  
> Url: https://github.com/boostorg/asio/issues/426#issuecomment-2057184580  

This can be closed, changes were implemented in https://github.com/chriskohlhoff/asio/commit/ee3bc5c89d310ee994073ba68ca35cb64332f8fd & will be included in Boost 1.85.0
