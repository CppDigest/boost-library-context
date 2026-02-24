# #293 - The fuction boost::system::error_code win_iocp_serial_port_service::open( win_iocp_serial_port_service::implementation_type& impl, const std::string& device, boost::system::error_code& ec) returns error always [Closed]

> Username: dshadrin  
> Created at: 2019-10-23 07:59:54 UTC  
> Updated at: 2020-12-30 01:10:34 UTC  
> Closed at: 2020-12-30 01:10:33 UTC  
> Url: https://github.com/boostorg/asio/issues/293  

I boost version 1.71 in file boost\asio\detail\impl\win_iocp_serial_port_service.ipp on line 87 was inserted  
dcb.BaudRate = 0; // 0 baud by default  
but it is incorrected value for followed call of SetCommState.  
GetLastError() return 87.  
  
This is breakage because at least boost version 1.68 this function works correct.

---

## Comment 1

> Username: dshadrin  
> Created at: 2019-10-23 08:09:21 UTC  
> Url: https://github.com/boostorg/asio/issues/293#issuecomment-545323558  

Duplicate of #280

---

## Comment 2

> Username: Roman-Bober  
> Created at: 2020-09-08 16:09:10 UTC  
> Url: https://github.com/boostorg/asio/issues/293#issuecomment-688981430  

Same with me, when fixing dcb.BaudRate to correct value, "open" works flawlessly  
My workaround was :  
to take "open" method  code out and create HANDLE  to COM port separately   
and when create serial_port object with handle from above

---

## Comment 3

> Username: ghost  
> Created at: 2020-12-30 01:10:32 UTC  
> Url: https://github.com/boostorg/asio/issues/293#issuecomment-752293102  

This issue was moved by [chriskohlhoff](https://github.com/chriskohlhoff) to [chriskohlhoff/asio#726](https://github.com/chriskohlhoff/asio/issues/726).
