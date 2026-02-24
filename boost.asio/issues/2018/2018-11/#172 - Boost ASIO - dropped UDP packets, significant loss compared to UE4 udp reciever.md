# #172 - Boost ASIO - dropped UDP packets, significant loss compared to UE4 udp reciever [Closed]

> Username: syedalamabbas  
> Created at: 2018-11-28 19:55:03 UTC  
> Updated at: 2018-12-08 04:42:57 UTC  
> Closed at: 2018-12-08 04:42:57 UTC  
> Url: https://github.com/boostorg/asio/issues/172  

I am trying to use a simple async UDP listener using [this](https://www.boost.org/doc/libs/1_68_0/doc/html/boost_asio/example/cpp11/echo/async_udp_echo_server.cpp). I also use UE4 UDP Socket (FUdpSocketBuilder) in a game engine project. I don't run these two UDP listeners simultaneously. I get a series of byte array from a fixed udp port from another application in following order start 36, middle 65488 x 6 and end 400 bytes. But the problem is I miss 4 x 65488 bytes and sometimes I get the trailing 400 bytes in the boost asio example C++ app. I have also asked this question on [stack over flow](https://stackoverflow.com/questions/53526361/boost-asio-dropped-udp-packets-significant-loss-compared-to-ue4-udp-reciever).   
 I have tried increasing the received byte array size to a high number including 7-8 times what is declared below:  
```  
constexpr int udp_buffer_size = 65536;  // Max limit of each packet size  
 boost::array<char, udp_buffer_size> recv_buffer_;  
```  
What can I do, please advice ?  
Print from Boost ASIO UDP:  
```  
I just recieved 36 bytes of data !  
I just recieved 65488 bytes of data !  
I just recieved 65488 bytes of data !  
I just recieved 36 bytes of data !  
I just recieved 65488 bytes of data !  
I just recieved 65488 bytes of data !  
```  
Print from UE4:  
```  
[2018.11.28 - 17.18.29:724][829]SomeProjectLog:  Warning : 2297. Recieved Bytes from UDP 36.  
[2018.11.28 - 17.18.29:724][829]SomeProjectLog : Warning : 2298. Recieved Bytes from UDP 65488.  
[2018.11.28 - 17.18.29:724][829]SomeProjectLog : Warning : 2299. Recieved Bytes from UDP 65488.  
[2018.11.28 - 17.18.29:725][829]SomeProjectLog : Warning : 2300. Recieved Bytes from UDP 65488.  
[2018.11.28 - 17.18.29:725][829]SomeProjectLog : Warning : 2301. Recieved Bytes from UDP 65488.  
[2018.11.28 - 17.18.29:725][829]SomeProjectLog : Warning : 2302. Recieved Bytes from UDP 65488.  
[2018.11.28 - 17.18.29:725][829]SomeProjectLog : Warning : 2303. Recieved Bytes from UDP 65488.  
[2018.11.28 - 17.18.29:726][829]SomeProjectLog : Warning : 2304. Recieved Bytes from UDP 400.  
[2018.11.28 - 17.18.29:726][829]SomeProjectLog : Warning : 2305. Recieved Bytes from UDP 36.  
[2018.11.28 - 17.18.29:726][829]SomeProjectLog : Warning : 2306. Recieved Bytes from UDP 65488.  
[2018.11.28 - 17.18.29:726][829]SomeProjectLog : Warning : 2307. Recieved Bytes from UDP 65488.  
[2018.11.28 - 17.18.29:726][829]SomeProjectLog : Warning : 2308. Recieved Bytes from UDP 65488.  
[2018.11.28 - 17.18.29:727][829]SomeProjectLog : Warning : 2309. Recieved Bytes from UDP 65488.  
[2018.11.28 - 17.18.29:727][829]SomeProjectLog : Warning : 2310. Recieved Bytes from UDP 65488.  
[2018.11.28 - 17.18.29:727][829]SomeProjectLog : Warning : 2311. Recieved Bytes from UDP 65488.  
[2018.11.28 - 17.18.29:727][829]SomeProjectLog : Warning : 2312. Recieved Bytes from UDP 400.  
```

---

## Comment 1

> Username: syedalamabbas  
> Created at: 2018-12-08 04:42:57 UTC  
> Url: https://github.com/boostorg/asio/issues/172#issuecomment-445430470  

I was able to fix the UDP packets loss issue today. It is not a concurrency issue that can be solved by using a separate thread. It is the issue of data speed intra-epoch not inter-epoch. What I mean by that is, it is a data stream that dumps UDP packets on the port epoch(significant event) to epoch with some acquisition delay in between. That is the window of opportunity to process anything, not within the epoch, but just after the epoch which is the 400 trailing bytes. I am using a single threaded program but I have Boost ASIO i/o object for asynchronous operations. All I do now is a fast memory copy of every byte array received until I hit a modulo - N(epoch bytes total), then I process each packet from the accumulated array, then I am able to collect all the data without any packet loss!   
If you want to read more about why it is not advisable to use threads besides making application complicated here is the [link ](https://www.boost.org/doc/libs/1_68_0/doc/html/boost_asio/overview/core/async.html): The Proactor Design Pattern: Concurrency Without Threads.
