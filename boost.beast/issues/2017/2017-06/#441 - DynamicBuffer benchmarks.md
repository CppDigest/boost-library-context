# #441 - DynamicBuffer benchmarks [Closed]

> Username: vinniefalco  
> Created at: 2017-06-08 14:45:16 UTC  
> Updated at: 2017-06-09 22:07:48 UTC  
> Closed at: 2017-06-09 22:07:48 UTC  
> Url: https://github.com/boostorg/beast/issues/441  

Add a simple benchmarking function to measure the performance of a dynamic buffer in bytes prepared and bytes consumed per second. Add a core-bench target which compares the various buffers.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2017-06-08 16:46:51 UTC  
> Updated at: 2017-06-08 19:27:39 UTC  
> Url: https://github.com/boostorg/beast/issues/441#issuecomment-307161410  

```  
Peformance of DynamicBuffer implementations  
  
count=1024, size=1024            prepare      with hint         random  
multi_buffer            :     16083 MB/s     15920 MB/s     12601 MB/s   51ms  
multi_buffer            :     16098 MB/s     15929 MB/s     12657 MB/s   51ms  
multi_buffer            :     16099 MB/s     15958 MB/s     12660 MB/s   51ms  
flat_buffer             :      7962 MB/s      8047 MB/s      1472 MB/s   232ms  
flat_buffer             :      8043 MB/s      7997 MB/s      1450 MB/s   235ms  
flat_buffer             :      8018 MB/s      8009 MB/s      1567 MB/s   221ms  
boost::asio::streambuf  :      7093 MB/s      6755 MB/s      4296 MB/s   130ms  
boost::asio::streambuf  :      6964 MB/s      6562 MB/s      3999 MB/s   136ms  
boost::asio::streambuf  :      5777 MB/s      4928 MB/s      3163 MB/s   173ms  
  
count=512, size=4096             prepare      with hint         random  
multi_buffer            :     18386 MB/s     18288 MB/s     17296 MB/s   83ms  
multi_buffer            :     18317 MB/s     18294 MB/s     17425 MB/s   83ms  
multi_buffer            :     18386 MB/s     18354 MB/s     17599 MB/s   82ms  
flat_buffer             :      6853 MB/s      6961 MB/s      1478 MB/s   483ms  
flat_buffer             :      6958 MB/s      6002 MB/s      1337 MB/s   528ms  
flat_buffer             :      6869 MB/s      6976 MB/s      1266 MB/s   539ms  
boost::asio::streambuf  :      5752 MB/s      5612 MB/s      3882 MB/s   304ms  
boost::asio::streambuf  :      6272 MB/s      5886 MB/s      3981 MB/s   290ms  
boost::asio::streambuf  :      6238 MB/s      5978 MB/s      3919 MB/s   291ms  
  
count=256, size=32768            prepare      with hint         random  
multi_buffer            :     18382 MB/s     18448 MB/s     17957 MB/s   329ms  
multi_buffer            :     18124 MB/s     18503 MB/s     18034 MB/s   329ms  
multi_buffer            :     18815 MB/s     18702 MB/s     17664 MB/s   326ms  
flat_buffer             :      3671 MB/s      3551 MB/s      2477 MB/s   1916ms  
flat_buffer             :      3636 MB/s      3393 MB/s      2315 MB/s   2006ms  
flat_buffer             :      3193 MB/s      3313 MB/s      2360 MB/s   2079ms  
boost::asio::streambuf  :      2794 MB/s      3173 MB/s      2228 MB/s   2246ms  
boost::asio::streambuf  :      3070 MB/s      3017 MB/s      2181 MB/s   2233ms  
boost::asio::streambuf  :      2881 MB/s      2834 MB/s      2090 MB/s   2356ms  
```
