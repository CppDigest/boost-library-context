# #290 - Unable to open boost::asio::serial_port in boost 1.71 [Closed]

> Username: cingelmarekmt  
> Created at: 2019-09-27 19:49:58 UTC  
> Updated at: 2020-12-30 01:10:10 UTC  
> Closed at: 2020-12-30 01:10:09 UTC  
> Url: https://github.com/boostorg/asio/issues/290  

I'm trying to open port but open function generate error code: 87 (The parameter is incorrect)  
  
Boost build settings:   
> b2.exe -a -q -d0 -j12 toolset=msvc-14.2 address-model=64 architecture=x86 link=shared runtime-link=shared threading=multi optimization=speed --build-type=complete variant=release stage   
  
Code:   
    asio::io_service io;  
    asio::serial_port port(io);  
  
    port.open("COM3");  
    port.set_option(asio::serial_port_base::baud_rate(115200));  
  
    char c;  
  
    // Read 1 character into c, this will block  
    // forever if no character arrives.  
    asio::read(port, asio::buffer(&c,1));  
  
    port.close();  
  
What is wrong in new version?

---

## Comment 1

> Username: endorph-soft  
> Created at: 2019-10-14 06:23:54 UTC  
> Url: https://github.com/boostorg/asio/issues/290#issuecomment-541517133  

Duplicate of https://github.com/boostorg/asio/issues/280?

---

## Comment 2

> Username: ghost  
> Created at: 2020-12-30 01:10:08 UTC  
> Url: https://github.com/boostorg/asio/issues/290#issuecomment-752293035  

This issue was moved by [chriskohlhoff](https://github.com/chriskohlhoff) to [chriskohlhoff/asio#723](https://github.com/chriskohlhoff/asio/issues/723).
