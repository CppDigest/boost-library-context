# #69 - Deadlock in named_condition.notify_one when waiting process dies [Closed]

> Username: KonstantinKhabarlak  
> Created at: 2018-11-28 11:42:46 UTC  
> Updated at: 2018-12-02 12:36:38 UTC  
> Closed at: 2018-12-02 12:36:38 UTC  
> Url: https://github.com/boostorg/interprocess/issues/69  

I've been using `boost::interprocess::message_queue_t` to implement communication between two processes. I need to have it resilient to process crashes, so I have defined: `BOOST_INTERPROCESS_ENABLE_TIMEOUT_WHEN_LOCKING`  
It helped in most cases, but occasionally `message_queue::timed_push` was still hanging with a stack trace leading to:  
```  
 	MyApp.dll!boost::interprocess::ipcdetail::try_based_lock<boost::interprocess::ipcdetail::spin_mutex>(boost::interprocess::ipcdetail::spin_mutex & m) Line 71	C++  
 	MyApp.dll!boost::interprocess::ipcdetail::spin_mutex::lock() Line 67	C++  
 	MyApp.dll!boost::interprocess::ipcdetail::spin_condition::notify(unsigned int command) Line 150	C++  
 	MyApp.dll!boost::interprocess::ipcdetail::spin_condition::notify_one() Line 133	C++  
 	MyApp.dll!boost::interprocess::interprocess_condition::notify_one() Line 93	C++  
	MyApp.dll!boost::interprocess::message_queue_t<boost::interprocess::offset_ptr<void,__int64,unsigned __int64,0> >::do_send(boost::interprocess::message_queue_t<boost::interprocess::offset_ptr<void,__int64,unsigned __int64,0> >::block_t block, const void * buffer, unsigned __int64 buffer_size, unsigned int priority, const boost::posix_time::ptime & abs_time) Line 817	C++  
 	MyApp.dll!boost::interprocess::message_queue_t<boost::interprocess::offset_ptr<void,__int64,unsigned __int64,0> >::timed_send(const void * buffer, unsigned __int64 buffer_size, unsigned int priority, const boost::posix_time::ptime & abs_time) Line 717	C++  
```  
  
  
I've investigated and tried to pinpoint where the problem is. I've come up with minimal code to have the problem reproduced stable:  
Producer:  
  
```cpp  
void spin_deadlock_test()  
{  
	using namespace boost::interprocess;  
  
	named_condition::remove("my_named_condition");  
	named_mutex::remove("my_named_mutex");  
  
	named_condition cond{ create_only, "my_named_condition" };  
	named_mutex mtx{ create_only, "my_named_mutex" };  
  
	try  
	{  
		while (true)  
		{  
			cond.notify_one();  
			std::this_thread::sleep_for(std::chrono::seconds(1));  
			std::cout << "P: Still alive!" << std::endl;  
		}  
	}  
	catch (const std::exception& e)  
	{  
		std::cout << e.what() << std::endl;  
	}  
  
	std::cout << "Press any key to continue" << std::endl;  
	getchar();  
}  
```  
  
Consumer:  
```cpp  
void spin_deadlock_test()  
{  
	using namespace boost::interprocess;  
	  
	named_condition cond{ open_only, "my_named_condition" };  
	named_mutex mtx{ open_only, "my_named_mutex" };  
  
	try  
	{  
		while (true)  
		{  
			scoped_lock<named_mutex> lock(mtx);  
  
			cond.wait(lock);  
			std::cout << "C: Still alive!" << std::endl;  
		}  
	}  
	catch (const std::exception& e)  
	{  
		std::cout << e.what() << std::endl;  
	}  
  
	std::cout << "Press any key to continue" << std::endl;  
	getchar();  
}  
```  
  
Producer output (started first):  
```  
P: Still alive!  
P: Still alive!  
P: Still alive!  
<consumer is started here>  
P: Still alive!  
P: Still alive!  
P: Still alive!  
<consumer app is closed here>  
P: Still alive!  
<no more output>  
```  
  
Expected: is to have exception thrown or for the app to continue working

---

## Comment 1

> Username: igaztanaga  
> Created at: 2018-12-02 12:36:38 UTC  
> Url: https://github.com/boostorg/interprocess/issues/69#issuecomment-443504342  

Fixed after applying Pull #70
