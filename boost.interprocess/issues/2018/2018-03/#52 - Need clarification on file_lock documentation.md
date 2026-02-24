# #52 - Need clarification on file_lock documentation [Open]

> Username: rconde01  
> Created at: 2018-03-31 17:59:51 UTC  
> Updated at: 2018-03-31 17:59:51 UTC  
> Url: https://github.com/boostorg/interprocess/issues/52  

The documentation of boost::interprocess::file_lock has some cautions on synchronization limitations:   
  * It's unspecified if a file_lock synchronizes two threads from the same process.   
  * It's unspecified if a process can use two file_lock objects pointing to the same file.   
  
But then gives the guidance:   
  * For each file, use a single file_lock object per process.   
  * Use the same thread to lock and unlock a file.   
  
I then created the following example:   
<https://wandbox.org/permlink/dO2rRDCHVWBE3pGr>https://wandbox.org/permlink/3CNfkA3RcMkwbd2v   
  
```  
#include <fstream>   
#include <iostream>   
#include <cstdlib>   
#include <boost/interprocess/sync/file_lock.hpp>   
#include <boost/interprocess/sync/scoped_lock.hpp>   
#include <boost/filesystem.hpp>   
#include <mutex>   
#include <thread>   
#include <chrono>   
int main()   
{   
    std::ofstream("f.txt");   
    boost::interprocess::file_lock lock("f.txt");   
    std::mutex mtx;   
    const size_t max = 100;   
    size_t t1Count = 0;   
    std::thread   
        t1   
        (   
            [&lock,&t1Count,&mtx,max]()   
            {   
                while(t1Count < max)   
                {   
                    //std::lock_guard<std::mutex> mtxLock(mtx);   
                    boost::interprocess::scoped_lock<boost::interprocess::file_lock> guard(lock);   
                    std::cout << "Number 1" << std::endl;   
                    t1Count++;   
                    std::this_thread::sleep_for(std::chrono::milliseconds(1));   
                }   
            }   
        );   
    size_t t2Count = 0;   
    std::thread   
        t2   
        (   
            [&lock,&t2Count,&mtx,max]()   
            {   
                while(t2Count < max)   
                {   
                    //std::lock_guard<std::mutex> mtxLock(mtx);   
                    boost::interprocess::scoped_lock<boost::interprocess::file_lock> guard(lock);   
                    std::cout << "Number 2" << std::endl;   
                    t2Count++;   
                    std::this_thread::sleep_for(std::chrono::milliseconds(1));   
                }   
            }   
        );   
    t1.join();   
    t2.join();   
    return 0;   
}   
```  
  
It only works properly if the std::mutex is used in addition to the file_lock. That was originally my thought when I read "It's unspecified if a file_lock synchronizes two threads from the same process.", but it seemed like I was following the guidance "Use the same thread to lock and unlock a file.". If I haven't messed anything up, I would probably update the documentation to say something like:   
"If utilitizing the file_lock from multiple threads within the same process, synchronize access to the file_lock with an intraprocess synchronization mechanism such as a mutex."   
  
Otherwise, is it a bug?   
  
Thanks,   
Rob
