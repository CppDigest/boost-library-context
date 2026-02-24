# #33 - Default values for connect_properties and publish_props [Closed]

> Username: Stefan-Durstberger  
> Created at: 2025-07-03 15:20:37 UTC  
> Updated at: 2025-07-15 12:38:58 UTC  
> Closed at: 2025-07-15 12:38:58 UTC  
> Url: https://github.com/boostorg/mqtt5/issues/33  

Hi all,  
at the moment default values for connect_properties and publish_props are not document anywhere nor are they visible in code at a obvious location. Issue: have a common place where default values are defined or document them in doxygen.  
  
E.g. when constructing the boost::mqtt5::mqtt_client with defaulted connect_properties maximum_packet_size is set to 65'536 in this impl header.  
https://github.com/boostorg/mqtt5/blob/272217cbb3eb4c865f0d8cd8b67c055f56c55b88/include/boost/mqtt5/impl/assemble_op.hpp#L66  
  
Best,  
Stefan

---

## Comment 1

> Username: biljazovic  
> Created at: 2025-07-07 14:17:12 UTC  
> Url: https://github.com/boostorg/mqtt5/issues/33#issuecomment-3045383499  

You're right, the default value for maximum packet size we process is not documented anywhere. Furthermore, we leave connect properties empty by default which tells the broker we accept packets of any size even though that's not true. If broker tries to send a larger packet we disconnect with "Malformed Packet" reason code.  
  
We will include an appropriate value as `maximum_packet_size` in the connect properties and document this behaviour. I'm not sure yet if the change will make it into the Boost 1.89 release.  
  
Thanks a lot for raising this!

---

## Comment 2

> Username: Stefan-Durstberger  
> Created at: 2025-07-11 13:31:21 UTC  
> Url: https://github.com/boostorg/mqtt5/issues/33#issuecomment-3062356736  

Hi @biljazovic,  
I might have found a second problem.  
We are currently running multiple services (with individual mqtt5 clients) on a local machine.  
When setting maximum_packet_size to a big value, i.e. the maximum allowed size of 268'435'456 we recognise the following:  
* The memory consumption increases. This makes sense as a fixed size buffer with above size is allocated for each client  
* The CPU load is increasing heavily even with only small messages being sent. This is very unexpected and a problem for the overall client performance!  
  
The behaviour does not only occour with 268'435'456 but also when setting the value to 500KB we recognise a significant CPU load increase. I know this is a very rough problem description. Would be interesting, if this is expected behaviour from your side? And if not, if you can reproduce it.  
  
Thanks alot! Best,  
Stefan

---

## Comment 3

> Username: biljazovic  
> Created at: 2025-07-14 08:24:41 UTC  
> Url: https://github.com/boostorg/mqtt5/issues/33#issuecomment-3068366519  

I reproduced the issue, fix is on the way.  
  
Thanks again
