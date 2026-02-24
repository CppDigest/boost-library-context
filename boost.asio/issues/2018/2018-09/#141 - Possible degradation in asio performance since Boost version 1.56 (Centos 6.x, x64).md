# #141 - Possible degradation in asio performance since Boost version 1.56 (Centos 6.x, x64) [Closed]

> Username: plynch1976  
> Created at: 2018-09-04 14:59:12 UTC  
> Updated at: 2020-12-30 00:52:52 UTC  
> Closed at: 2020-12-30 00:52:51 UTC  
> Url: https://github.com/boostorg/asio/issues/141  

Hi,  
We’ve been using Boost for the last number of years (specifically Boost.asio) and for the most part it’s been fantastic for us.  We’ve just upgraded an older service to use a later Boost version (“service A” was written 5 years ago and it was based on boost.asio using ssl).  
  
We noticed a significant drop in Queries Per Second when we performance tested the service with the newer Boost, so we investigated further.  See below for details.  
  
Is there something simple that we are missing – e.g. the compiler flags with the newer Boost?  Has this issue been seen before?    
  
**“Service A”** code is closely based on the example shown here  
https://www.boost.org/doc/libs/1_39_0/doc/html/boost_asio/example/ssl/server.cpp  
  
Any help or pointers here would be greatly appreciated.  For the moment, I have had to drop the services back to use Boost 1.55  
  
Thanks,  
Paul.  
  
**Summary of the findings:**  
Boost ASIO performance has degraded significantly for us since Boost version 1.55, with further performance drops until 1.63.  I have not tried the latest boost version 1.68  
  
We have a service that was built 5 years ago & it used Boost 1.51.  In a cleanup operation, we upgraded Boost to version 1.63 and noticed significant performance drops against what we had previously (throughput on the server dropped from ~50k per second to ~16k per second with no other code changes).  
  
Further investigation using iterative upgrades from **1.51**, to **1.52** all the way to **1.63** showed that the first performance impact was in **1.56** where performance dropped from **~50k QPS** to **31k QPS**.  By **1.63**, the performance had dropped to **~16k** as mentioned above.  
  
We have noticed this same drop across two different services (both deployed & built on Centos 6.x, x64):  
  
1)	**“Service A”** (mentioned above) is a binary service over SSL using Boost.Asio.  We use custom load test tools to test the maximum threshold of the service.  Each request payload is < 1kb.  
- AcceptComplete:  
  - m_sslSocket.async_handshake(HandshakeComplete)  
- HandshakeComplete:  
  - m_sslSocket.async_read_some(ReadComplete)  
- ReadSomeComplete:  
  - CreateRequest()  
    - Post to Boost IoService for async processing of request  
    - m_sslSocket.async_read_some(ReadComplete)  
  
  
2. **“Service B”** is a service based on RESTful service based on nginx & Boost.Asio.  We use Tsung to test the maximum threshold of this service.   Nginx does the SSL termination in this service and forwards to the FCGI library written using boost asio.  Each request payload is < 1kb.  
  
- AcceptComplete:   
  - m_socket.async_handshake(HandshakeComplete)  
- HandshakeComplete:  
  - m_socket.async_read_some(ReadComplete)  
- ReadSomeComplete:  
  - CreateRequest()  
    - Post to Boost IoService for async processing of request  
  - m_socket.async_read_some(ReadComplete)  
  
There is no shared code in either of the services apart from the fact that they are both based on ASIO TCP servers  
  
  
**Boost version | Performance (QPS)**  
-- | --  
**1.63 performance** | Total:  15,749  
**1.57 performance** | Total:  30,923  
**1.51 performance** | Total:  49,421  
  
**Compiler**  
We have tried with both gcc/g++ 4.4.7 and dev toolset 3.  The same performance impacts are seen  
  
**dev toolset3**  
$ gcc --version  
gcc (GCC) 4.9.2 20150212 (Red Hat 4.9.2-6)  
Copyright (C) 2014 Free Software Foundation, Inc.  
  
  
**Build**  
We build Boost from source for every build and link dynamically.  Note that to build some of the later boost versions I removed the "cat" command.  
  
cat <<EOF > ./tools/build/v2/user-config.jam  
using gcc : : ${_GCC_HOME_}/g++;  
EOF  
./bootstrap.sh --prefix=$temp_install_dir  
./b2 install --prefix=$temp_install_dir --toolset=gcc link=static  
  
**Server config**  
- Centos 6.x x64  
- 32 cores  
- 96 GB RAM

---

## Comment 1

> Username: ghost  
> Created at: 2020-12-30 00:52:50 UTC  
> Url: https://github.com/boostorg/asio/issues/141#issuecomment-752289837  

This issue was moved by [chriskohlhoff](https://github.com/chriskohlhoff) to [chriskohlhoff/asio#635](https://github.com/chriskohlhoff/asio/issues/635).
