# #280 - Cannot open serial ports under Windows (1.71.0 regression) [Closed]

> Username: endorph-soft  
> Created at: 2019-09-10 03:57:58 UTC  
> Updated at: 2020-12-30 01:09:10 UTC  
> Closed at: 2020-12-30 01:09:09 UTC  
> Url: https://github.com/boostorg/asio/issues/280  

The latest boost (1.71.0) can't open serial ports under Windows anymore. This is not desirable.  
  
The change to set default parameters (https://github.com/boostorg/asio/commit/c113adc19004770d945672009b3d26f9498099cd) uses 0 as the default baud rate. Windows does not like that value, and fails to open the serial port.  
  
This may be specific to the serial hardware, but it fails on everything I have access to.  
  
Another user has already reported via a pull request: https://github.com/boostorg/asio/pull/273. I'm creating this issue as per the bug reporting guidelines. That pull request also changes the default flow control, which does not appear to be necessary.

---

## Comment 1

> Username: JamesOldfield  
> Created at: 2019-12-20 11:31:08 UTC  
> Url: https://github.com/boostorg/asio/issues/280#issuecomment-567894540  

I added my comment about this to that pull request #273 : In short, I think fixing the problem by setting the Baud explicitly to 9600 is more confusing that leaving it at whatever value it had when opened.

---

## Comment 2

> Username: Superlokkus  
> Created at: 2020-01-14 12:03:10 UTC  
> Url: https://github.com/boostorg/asio/issues/280#issuecomment-574142297  

> I added my comment about this to that pull request #273 : In short, I think fixing the problem by setting the Baud explicitly to 9600 is more confusing that leaving it at whatever value it had when opened.  
  
I guess not working is the much bigger problem than debatable "confusing" style.

---

## Comment 3

> Username: Roman-Bober  
> Created at: 2020-09-08 16:11:48 UTC  
> Url: https://github.com/boostorg/asio/issues/280#issuecomment-688983146  

Same with me, when fixing dcb.BaudRate to correct value, "open" works flawlessly  
My workaround was :  
to take "open" method code out and create HANDLE to COM port separately  
and when create serial_port object with handle from above  
  
btw, my baud rate had to be 115200

---

## Comment 4

> Username: ghost  
> Created at: 2020-12-30 01:09:08 UTC  
> Url: https://github.com/boostorg/asio/issues/280#issuecomment-752292889  

This issue was moved by [chriskohlhoff](https://github.com/chriskohlhoff) to [chriskohlhoff/asio#717](https://github.com/chriskohlhoff/asio/issues/717).
