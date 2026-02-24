# #2180 - Possible performance issue with new beast [Closed]

> Username: shuras109  
> Created at: 2021-03-06 14:02:13 UTC  
> Updated at: 2022-01-09 05:17:19 UTC  
> Closed at: 2022-01-09 05:17:19 UTC  
> Url: https://github.com/boostorg/beast/issues/2180  

Hi there!  
I've recently try to recompile my service from boost 169 to new boost 175.  
Boost 169 builds done on Debian 9 with default gcc 6.3 (if I remember version correctly) and boost 175 builds done with Debian 11 (not yet released) default gcc 10.    
Upgrade demands some changes to compile and additionally I've also drop all deprecated features by forcing BOOST_ASIO_NO_DEPRECATED.  
The only intensive work my service do is continuous bidirectional exchange of data blocks (packets) between  websocket::stream<boost::beast::tcp_stream> and boost::asio::posix::stream_descriptor running on unix file descriptor.  
There are two threads. One asynchronously reading/writing from websocket ("session" thread) and another asynchronously reading/writing from stream_descriptor ("tunnel" thread). Both threads exchange data with each other.   
With old boost I've use combination of some dynamic buffer storage and std::vector on opposite data flows. It was a mess but it was working for me.  
With upgrade to new version of boost I've finally start to use boost::beast::flat_static_buffer in both directions.  
To my surprise after boost upgrade performance of whole system worsened.   
With boost 169 both threads take same amount of cpu time.  
With boost 175 websocket thread eats considerably more cpu time while stream_descriptor thread eats slightly less. Overall throughput decreased (at least 15-20%) with websocket connections buffers starting to piling up early before websocket thread become 100% of cpu core.  
  
I've put some effort to profile this situation and here is that I've found.  
Flame graph for boost 169:  
![With boost 169](https://user-images.githubusercontent.com/23736445/110208641-756b6280-7e99-11eb-8c94-d1658ce6a24e.png)  
  
Flame graph for boost 175:  
![With boost 175](https://user-images.githubusercontent.com/23736445/110208655-8ae08c80-7e99-11eb-95f3-e98be2ea6725.png)  
  
With boost 175 stack depth is considerably deeper for "session" thread operations while "tunnel" thread looks pretty much the same. And it looks like deeper stack of calls takes more time to process causing performance to degrade.  
  
Is there something to be done to avoid current situation? My knowledge of c++ is limited and maybe I've use bad coding patterns in my service.  
  
I am also attaching gdb stack traces of deepest parts of "session" thread for both boost versions. But beware. Stack traces here are not for weak souls :)  
It take 79 layers to async read from websocket with boost 175, comparing to 52 layers with boost 169.  
[with169.recv.bt.txt](https://github.com/boostorg/beast/files/6095424/with169.recv.bt.txt)  
[with169.send.bt.txt](https://github.com/boostorg/beast/files/6095425/with169.send.bt.txt)  
[with175.recv.bt.txt](https://github.com/boostorg/beast/files/6095426/with175.recv.bt.txt)  
[with175.send.bt.txt](https://github.com/boostorg/beast/files/6095427/with175.send.bt.txt)

---

## Comment 1

> Username: stale[bot]  
> Created at: 2021-05-29 16:10:58 UTC  
> Url: https://github.com/boostorg/beast/issues/2180#issuecomment-850857865  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 2

> Username: stale[bot]  
> Created at: 2022-01-09 05:16:54 UTC  
> Url: https://github.com/boostorg/beast/issues/2180#issuecomment-1008232399  

It looks like this issue has either been abandoned or resolved so I will go ahead and close it. Feel free to re-open this issue if you feel it needs attention, or open new issues as needed. Thanks!
