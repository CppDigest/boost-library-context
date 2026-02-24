# #170 - Adding standart baudrate 1.5Mbps to the baudrate list [Closed]

> Username: mkleox  
> Created at: 2018-11-21 19:17:13 UTC  
> Updated at: 2020-12-30 00:57:34 UTC  
> Closed at: 2020-12-30 00:57:33 UTC  
> Url: https://github.com/boostorg/asio/issues/170  

As the 1500000 bps is a standard baud rate and it is not defined in "serial_port_base.ipp", I strongly suggest adding this baud rate in the part which has a label with "extended ones conditionally".    
  
This baud rate, for example, is the maximum baud rate in NANO pi and most of the embedded boards which boost does not support opening this baud rate.  
  
So the change will be like:   
  
ifdef B1152000  
  case B1152000: value_ = 1152000; break;  
endif  
 ifdef B1500000  
  case B1500000: value_ = 1500000; break;  
endif  
ifdef B2000000  
  case B2000000: value_ = 2000000; break;  
endif  
  
  
BR,  
Leox

---

## Comment 1

> Username: ghost  
> Created at: 2020-12-30 00:57:32 UTC  
> Url: https://github.com/boostorg/asio/issues/170#issuecomment-752290683  

This issue was moved by [chriskohlhoff](https://github.com/chriskohlhoff) to [chriskohlhoff/asio#655](https://github.com/chriskohlhoff/asio/issues/655).
