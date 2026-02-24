# #351 - Optimize away buffer rotations [Open]

> Username: mzimbres  
> Created at: 2025-11-19 11:54:33 UTC  
> Updated at: 2025-11-19 11:54:33 UTC  
> Url: https://github.com/boostorg/redis/issues/351  

Buffer rotations are unacceptably high at the moment when data read from the socket contain multiple messages. For example, our stress test outputs  
  
```  
$ ./test/boost_redis_test_conn_echo_stress  
Bytes received (response): 3210147  
Bytes received (push): 32250036  
Bytes rotated: 3122279452  
```  
  
That means although we receive only 36.4Mb of data we endup rotating 3.1Gb. This results in unacceptable latency for the domains where Boost.Redis is most likely to be used (as our issues show). This happens because data is rotate eagerly [here](https://github.com/boostorg/redis/blob/00f3ec9b78454211c7b8d461b11411c8b1ef1ad3/include/boost/redis/impl/read_buffer.ipp#L62)  
  
```  
<data1><data2><data3><data4><data5><data6><data7>  
<data2><data3><data4><data5><data6><data7>  
<data3><data4><data5><data6><data7>  
<data4><data5><data6><data7>  
<data5><data6><data7>  
<data6><data7>  
<data7>  
```  
  
If we are willing to trade rotations with memory consumption we could avoid this problem by introducing an offset and rotating lazily until a certain threshold in memory consumption is reached, for example  
  
```  
<data1><data2><data3><data4><data5><data6><data7>  
       <data2><data3><data4><data5><data6><data7>  
              <data3><data4><data5><data6><data7>  
                     <data4><data5><data6><data7>  
                            <data5><data6><data7>  
                                   <data6><data7>  
<data7>  
```  
  
Another optimization opportunity to avoid high memory consumption is to rotate before the threshold is reached if the size of unprocessed data is small enough. For example, if we only rotate when the threshold is reached we have  
  
```  
<data1><data2><data3><data4><data5><data6><data7>  
       <data2><data3><data4><data5><data6><data7>  
              <data3><data4><data5><data6><data7>  
                     <data4><data5><data6><data7>  
                            <data5><data6><data7>  
                                   <data6><data7>  
                                          <data7><data8><data9><data10>  
                                                 <data8><data9><data10>  
                                                        <data9><data10>  
<data10>  
```  
  
If we implement the optimization described above we get  
  
```  
<data1><data2><data3><data4><data5><data6><data7>  
       <data2><data3><data4><data5><data6><data7>  
              <data3><data4><data5><data6><data7>  
                     <data4><data5><data6><data7>  
                            <data5><data6><data7>  
                                   <data6><data7>  
<data7><data8><data9><data10>  
       <data8><data9><data10>  
              <data9><data10>  
                     <data10>  
```  
  
Which rotates 4 blocks instead of one but consumes only 7 blocks of memory instead of 10, this might perform better since it is cache friendlier.
