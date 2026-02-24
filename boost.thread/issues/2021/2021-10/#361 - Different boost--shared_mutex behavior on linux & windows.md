# #361 - Different boost::shared_mutex behavior on linux & windows [Open]

> Username: mmengelkoch-fa  
> Created at: 2021-10-27 14:20:44 UTC  
> Updated at: 2021-10-27 14:40:25 UTC  
> Url: https://github.com/boostorg/thread/issues/361  

After some long time, I was updating boost from 1.58 to the latest version.  
And after resolving some minor issues due to updating, everything worked fine on my windows machine and all tests of my application passed. Unfortunately, when using linux (RHEL 7), there was always some strange behavior with threads and mutexes.   
  
I created some minimal example to investigate the issues and I have the same application that runs 3 seconds on windows and returns properly but runs for hours on my linux system and never finishes.  
It seems that linux now prefers `boost::shared_lock<boost::shared_mutex>` over `boost::unique_lock<boost::shared_mutex>`. As long as there are shared locks that own the mutex, no unqiue lock will get the mutex. While new shared locks still get the mutex. windows still prefers  `boost::unique_lock<boost::shared_mutex>`  
  
  
  
Here is the application - sorry for all the additional defines - I also wanted to test it with stl which had the same behavior.  
Runtime on windows 10, msvc: 3s  
Runtime on RHEL 8.4 boost 1.66: 12s (still ok)  
Ubuntu WSL boost 1.71 with g++ or clang (same behavior), : stopped watching after > 20 minutes  
  
It seems that there was a commit for boost 1.68 that changed the shared mutex preference completely, so that unique locks now run into starvation, as long as shared locks hold a mutex: https://github.com/boostorg/thread/commit/eef72939322916a03f9d797793c755f57aa59f03#diff-d452c62020a873f8c21ba3df4841794a32183e83e50d52f7e58e7d81da389361  
  
Here is the minimal sample code - make sure you compile with "USE_BOOST":  
  
```  
// stl_vs_boost.cpp  
// g++ -g -O2 -DUSE_BOOST -o boostM  stl_vs_boost.cpp -std=c++17 -pthread -lboost_system  -lboost_thread -lboost_iostreams  -lboost_date_time -lboost_random -lboost_chrono && ./boostM  
  
#include <exception>  
#include <iostream>  
#include <chrono>  
#include <thread>  
#include <memory>  
#include <shared_mutex>  
  
#ifdef USE_BOOST  
	#include <boost/shared_ptr.hpp>  
	#include <boost/thread.hpp>  
	#include <boost/thread/shared_mutex.hpp>  
	typedef boost::thread Thread;  
	typedef boost::shared_mutex SharedMutex;  
	typedef boost::unique_lock<SharedMutex> UniqueLock;  
	typedef boost::shared_lock<SharedMutex> SharedLock;  
	#define MUTEX_STRING "boost"  
#else  
	typedef std::thread Thread;  
	typedef std::shared_mutex SharedMutex;  
	typedef std::unique_lock<SharedMutex> UniqueLock;  
	typedef std::shared_lock<SharedMutex> SharedLock;  
	#define MUTEX_STRING "STL"  
#endif  
  
SharedMutex myMutex;  
const size_t maxIterations = 20000000;  
  
int sharedLocking() {  
	for (size_t i = 1; i < maxIterations; ++i) {  
		SharedLock a(myMutex);  
		if (i % 200 == 0) {  
			std::cout << "." << std::flush;  
		}  
		std::this_thread::sleep_for(std::chrono::milliseconds(5));  
	}  
	std::cout << "Finished local" << std::flush;  
	return 0;  
}  
  
int uniqueLocking() {  
	std::cout << "_" << std::flush;  
	UniqueLock a(myMutex);  
	std::cout << "|" << std::flush;  
	std::cout << "Unique" << std::endl << std::flush;  
	std::this_thread::sleep_for(std::chrono::seconds(2));  
	exit(0);  
	return 0;  
}  
  
int main(int argc, char **argv) {  
	std::cout << "Using " << MUTEX_STRING << " mutexes" << std::endl;  
	std::cout << " starting ..." << std::endl;  
	for (int i = 0; i < 128; ++i) {  
		std::shared_ptr<Thread> tmpThread = std::make_shared<Thread>(&sharedLocking);  
		tmpThread->detach();  
	}  
	std::this_thread::sleep_for(std::chrono::seconds(1));  
	std::shared_ptr<Thread> myThread = std::make_shared<Thread>(&uniqueLocking);  
	std::shared_ptr<Thread> myThread2 = std::make_shared<Thread>(&uniqueLocking);  
	myThread->join();  
	return 0;  
	  
}  
```  
  
Using   
g++ -g -O2 -DUSE_BOOST -DBOOST_THREAD_V2_SHARED_MUTEX -o boostM  stl_vs_boost.cpp -std=c++17 -pthread -lboost_system  -lboost_thread -lboost_iostreams  -lboost_date_time -lboost_random -lboost_chrono && ./boostM  
  
solved my issue and linux and windows worked in the same way.  
I'm just not sure if using BOOST_THREAD_V2_SHARED_MUTEX is a valid and tested solution and if the compile flag will also be available in future boost releases.
