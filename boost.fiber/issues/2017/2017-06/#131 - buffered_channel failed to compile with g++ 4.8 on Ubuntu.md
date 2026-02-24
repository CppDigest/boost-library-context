# #131 - buffered_channel failed to compile with g++ 4.8 on Ubuntu [Closed]

> Username: sdeber  
> Created at: 2017-06-23 08:42:16 UTC  
> Updated at: 2017-07-12 07:15:14 UTC  
> Closed at: 2017-07-12 07:15:14 UTC  
> Url: https://github.com/boostorg/fiber/issues/131  

I am trying to compile the code example with g++4.8 on Ubuntu 14.04 in the tutorial from boost website    
http://www.boost.org/doc/libs/1_63_0/libs/fiber/doc/html/fiber/synchronization/channels/buffered_channel.html  
  
And it failed with the following error:  
  
opt/third_party/include/boost/fiber/buffered_channel.hpp: In instantiation of ‘boost::fibers::buffered_channel<T>::buffered_channel(std::size_t) [with T = HAUD::SS7Wrapper*; std::size_t = long unsigned int]’:  
/home/ak70/HaudDevelopment/HaudCore/HaudCore/Services/HaudSMSService/HaudEndpointWorker.cpp:23:25:   required from here  
/opt/third_party/include/boost/fiber/buffered_channel.hpp:49:9: error: use of deleted function ‘std::atomic<long unsigned int>::atomic(const std::atomic<long unsigned int>&)’  
         slot() = default;  
         ^  
In file included from /home/ak70/HaudDevelopment/HaudCore/HaudCore/Services/HaudSMSService/HaudWorkerJob.h:11:0,  
                 from /home/ak70/HaudDevelopment/HaudCore/HaudCore/Services/HaudSMSService/HaudEndpointWorker.h:16,  
                 from /home/ak70/HaudDevelopment/HaudCore/HaudCore/Services/HaudSMSService/HaudEndpointWorker.cpp:7:  
/usr/include/c++/4.8/atomic:658:7: error: declared here  
       atomic(const atomic&) = delete;  
       ^  
In file included from /opt/third_party/include/boost/fiber/all.hpp:15:0,  
                 from /home/ak70/HaudDevelopment/HaudCore/HaudCore/Services/HaudSMSService/HaudWorkerJob.h:19,  
                 from /home/ak70/HaudDevelopment/HaudCore/HaudCore/Services/HaudSMSService/HaudEndpointWorker.h:16,  
                 from /home/ak70/HaudDevelopment/HaudCore/HaudCore/Services/HaudSMSService/HaudEndpointWorker.cpp:7:  
/opt/third_party/include/boost/fiber/buffered_channel.hpp:138:16: note: synthesized method ‘boost::fibers::buffered_channel<T>::slot::slot() [with T = HAUD::SS7Wrapper*]’ first required here   
         slots_ = new slot[capacity_]();  
                ^  
Any ideas?

---

## Comment 1

> Username: olk  
> Created at: 2017-07-12 07:15:14 UTC  
> Url: https://github.com/boostorg/fiber/issues/131#issuecomment-314677122  

it's a compiler problem - AFAIK gcc-4.8 is not fully C++11 compliant
