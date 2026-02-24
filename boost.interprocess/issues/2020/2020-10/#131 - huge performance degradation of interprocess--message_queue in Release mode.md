# #131 - huge performance degradation of interprocess::message_queue in Release mode [Closed]

> Username: ksubox  
> Created at: 2020-10-06 07:18:03 UTC  
> Updated at: 2021-04-21 09:34:26 UTC  
> Closed at: 2021-04-21 09:34:25 UTC  
> Url: https://github.com/boostorg/interprocess/issues/131  

Hello,  
I made tests of boost::interprocess::message_queue from boost 1.74 in Debug and Release mode with Visual Studio 2019 Preview 3.1 on Windows 10 and found big performance difference in Debug and Release mode.  
**Problem is: Debug build is 3-4 times FASTER then Release.**  
Can somebody explain ?  
Here is my test code:  
```  
#include <cstdio>  
#include <cstdint>  
#include <chrono>  
#include <thread>  
#include <conio.h>  
#include <boost/interprocess/ipc/message_queue.hpp>  
  
using namespace boost::interprocess;  
  
int active{ 0 };  
  
void ReceiverThread(const char* inQueueName, size_t maxInBuf, size_t maxInCnt) {  
  printf("ReceiverThread: %s\n", inQueueName);  
  uint8_t* inBuf = new uint8_t[maxInBuf + 128];  
  int totalPackets{ 0 };  
  int64_t totalSize{ 0 };  
  while (active) {  
    try {  
      message_queue::remove(inQueueName);  
      permissions p;  
      p.set_unrestricted();  
      message_queue mqIn(create_only, inQueueName, maxInCnt, maxInBuf, p);  
      while (active) {  
        unsigned int got = 0, prio = 0;  
        if (mqIn.try_receive(inBuf, maxInBuf, got, prio)) {  
        //if (mqIn.timed_receive(inBuf, maxInBuf, got, prio, boost::posix_time::ptime(microsec_clock::universal_time()) + boost::posix_time::milliseconds(300))) {  
          totalPackets++;  
          totalSize += got;  
        }  
        else {  
          std::this_thread::sleep_for(std::chrono::milliseconds(1));  
        }  
      }  
    }  
    catch (interprocess_exception& ex1) {  
      printf("Server exception %s\n", ex1.what());  
    }  
  }  
  message_queue::remove(inQueueName);  
  printf("ReceiverThread: %s completed, received %d packets, %lld size\n", inQueueName, totalPackets, totalSize);  
}  
  
void SenderThread(const char* outQueueName, size_t maxOutBuf, size_t maxOutCnt) {  
  printf("SenderThread: %s\n", outQueueName);  
  uint8_t* outBuf = new uint8_t[maxOutBuf + 128];  
  int totalPackets{ 0 };  
  int64_t totalSize{ 0 };  
  std::chrono::duration<double> elapsed;  
  while (active) {  
    try {  
      message_queue mqOut(open_only, outQueueName);  
      auto start = std::chrono::system_clock::now();  
      while (active) {  
        unsigned int got = 0, prio = 0;  
        if (mqOut.try_send(outBuf, maxOutBuf, 0)) {  
          totalPackets++;  
          totalSize += maxOutBuf;  
        }  
        else {  
          std::this_thread::sleep_for(std::chrono::milliseconds(1));  
        }  
      }  
      auto end = std::chrono::system_clock::now();  
      elapsed = end - start;  
    }  
    catch (interprocess_exception& ex1) {  
      printf("Server exception %s\n", ex1.what());  
    }  
  }  
  printf("SenderThread: %s completed, sent %d packets, %lld size; %d pack/sec, %lld bytes/sec\n", outQueueName, totalPackets, totalSize, (int)(totalPackets/elapsed.count()), (int64_t)(totalSize/elapsed.count()));  
}  
  
int main(int argc, char *argv[])  
{  
  int mode{ 0 };  
  int maxPacketSize{ 0 }, maxQueueSize{ 0 };  
  for (int i = 1; i < argc; ++i) {  
    int par = atoi(argv[i]);  
    if (par > 0) {  
      if (maxPacketSize == 0)  
        maxPacketSize = par;  
      else if (maxQueueSize == 0) {  
        maxQueueSize = par;  
      }  
    }  
    else if (strcmp(argv[i], "s") == 0) {  
      mode = 2;  
    }  
    else if (strcmp(argv[i], "r") == 0) {  
      mode = 1;  
    }  
  }  
  if (mode == 0 || maxPacketSize == 0 || maxQueueSize == 0) {  
    printf("wrong parameters\n");  
    exit(0);  
  }  
  std::thread worker;  
  active = 1;  
  if (mode == 2) {  
    worker = std::thread(SenderThread, "TestChannel", maxPacketSize, maxQueueSize);  
  }  
  else if (mode == 1) {  
    worker = std::thread(ReceiverThread, "TestChannel", maxPacketSize, maxQueueSize);  
  }  
  while (true) {  
    auto c = _getch();  
    if (c == 27 || c == 81 || c == 113) break;  
  }  
  active = 0;  
  worker.join();  
}  
```

---

## Comment 1

> Username: igaztanaga  
> Created at: 2021-02-01 14:05:20 UTC  
> Url: https://github.com/boostorg/interprocess/issues/131#issuecomment-770881022  

How should your test be executed? It makes no sense Debug to be faster than Release mode

---

## Comment 2

> Username: igaztanaga  
> Created at: 2021-04-21 09:34:25 UTC  
> Url: https://github.com/boostorg/interprocess/issues/131#issuecomment-823923711  

Closing the issue due to no recent feedback on how to reproduce it.
