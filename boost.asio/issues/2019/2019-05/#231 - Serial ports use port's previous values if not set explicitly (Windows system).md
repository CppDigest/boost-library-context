# #231 - Serial ports use port's previous values if not set explicitly (Windows system) [Closed]

> Username: jrctrimble  
> Created at: 2019-05-03 14:17:12 UTC  
> Updated at: 2020-12-30 01:03:47 UTC  
> Closed at: 2020-12-30 01:03:46 UTC  
> Url: https://github.com/boostorg/asio/issues/231  

This haunted me for about 10 months before I tracked it down. It's been present in every version since at least 1.50; it's probably always been there.  
  
When opening Asio serial ports after a fresh boot, my first write to the port would never work. I'd have to use another program or a different library to open the port before an Asio port would write correctly.  
  
With debugging, I found an Asio port would inherit all of its values from the last values used on the port if not set explicitly, even if Asio claims it provides a default value for that setting. After a fresh boot, the port has no values to inherit. I fixed this by explicitly defining every variable on a port (parity, flow control, stop bits, byte size, etc).  
  
Could be either a bug or a feature depending on how you look at it, but if it's a feature, the documentation should mention it or the default values should specify that they are actually "last used" rather than the values they currently show.  
  
EDIT: This is on a Windows system, so mileage may vary on other systems.

---

## Comment 1

> Username: jrctrimble  
> Created at: 2019-05-15 13:59:51 UTC  
> Url: https://github.com/boostorg/asio/issues/231#issuecomment-492666951  

Update: It's a Windows thing, the options aren't set explicitly in win_iocp_serial_port_service, but they are set explicitly on other platforms. I'll put something together to address this ASAP.

---

## Comment 2

> Username: ghost  
> Created at: 2020-12-30 01:03:44 UTC  
> Url: https://github.com/boostorg/asio/issues/231#issuecomment-752291991  

This issue was moved by [chriskohlhoff](https://github.com/chriskohlhoff) to [chriskohlhoff/asio#687](https://github.com/chriskohlhoff/asio/issues/687).
