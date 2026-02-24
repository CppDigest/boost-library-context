# #917 - Boot Interproccess Message queue compiling with MSVS 2022 toolset [Closed]

> Username: avairavan  
> Created at: 2024-05-29 18:27:00 UTC  
> Updated at: 2024-06-10 18:20:14 UTC  
> Closed at: 2024-06-10 18:19:17 UTC  
> Url: https://github.com/boostorg/boost/issues/917  

We are using boost interprocess message queue to communicate between the process. The following code works fine in Linux   
  
```  
#include <boost/interprocess/permissions.hpp>  
#include <boost/interprocess/ipc/message_queue.hpp>  
#include <boost/throw_exception.hpp>  
  
std::string:Receive()  
{  
	.....  
  
	try  
	{  
		boost::interprocess::permissions perm{0600};  
		message_queue mq(open_or_create, "message_queue", queueSize, msgSize, perm);  
  
		msg.resize(msgSize);  
		mq.receive(msg.data(), msg.size(), rcvdSize, priority);  
		msg.resize(rcvdSize);  
	}  
	catch (const std::exception& e)  
	{  
		...  
	}  
}  
```  
  
but in Windows,  it fails as follows  
  
`Error	C2039	'Sleep': is not a member of 'boost::interprocess::winapi'  out\build\x64-debug\vcpkg_installed\x64-windows-static\include\boost\interprocess\detail\os_thread_functions.hpp	192`  
  
The thread sleep function defined in os_thread_functions.hpp:192 resolves against the sleep macro defined in win32defs.h  
  
os_thread_functions.hpp:192:  
```  
inline void thread_sleep(unsigned int ms)  
{  winapi::sleep(ms);  }	  
```  
  
win32defs.h:  
`#define sleep(seconds) Sleep(1000*(DWORD)seconds)`  
  
If I undef sleep before including <boost/interprocess/ipc/message_queue.hpp> then Windows compiles fine. Appreciate any help.

---

## Comment 1

> Username: avairavan  
> Created at: 2024-06-10 18:20:12 UTC  
> Url: https://github.com/boostorg/boost/issues/917#issuecomment-2159013827  

Issue is with our proprietary windows header. Closing the issue
