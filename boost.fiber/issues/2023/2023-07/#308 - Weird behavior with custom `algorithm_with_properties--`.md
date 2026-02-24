# #308 - Weird behavior with custom `algorithm_with_properties<>` [Open]

> Username: Christian-Stieber  
> Created at: 2023-07-13 10:29:29 UTC  
> Updated at: 2023-07-13 12:40:29 UTC  
> Url: https://github.com/boostorg/fiber/issues/308  

I've been trying to hack up a way to wait for all fibers to complete, and I always end up with a count of 2 instead of 1 at the end. Here's a full example -- sorry, it's a couple of lines.  
  
The basic idea is: I don't have a whole lot of hooks to know about the creation and destruction of fibers, and the scheduler itself only knows about ready fibers, not about waiting ones. So, I'm creating a new scheduler with "properties" -- the scheduler itself just calls an embedded `round_robin`, and the "properties" are used to track the creation/destruction of fibers, so I can update a counter in the constructor/destructor.  
  
```  
#include <boost/fiber/properties.hpp>  
#include <boost/fiber/algo/algorithm.hpp>  
#include <boost/fiber/algo/round_robin.hpp>  
#include <boost/fiber/mutex.hpp>  
#include <boost/fiber/condition_variable.hpp>  
#include <boost/version.hpp>  
  
#include <thread>  
#include <iostream>  
  
/************************************************************************/  
  
class Scheduler;  
  
/************************************************************************/  
  
class Properties : public boost::fibers::fiber_properties  
{  
private:  
    Scheduler* scheduler=nullptr;  
  
public:  
    Properties(boost::fibers::context* context)  
        : fiber_properties(context)  
    {  
        assert(false);  
    }  
  
    Properties(boost::fibers::context*, Scheduler&);  
    ~Properties();  
};  
  
/************************************************************************/  
  
class Scheduler : public boost::fibers::algo::algorithm_with_properties<Properties>  
{  
public:  
    boost::fibers::condition_variable condition;  
    unsigned int counter=0;  
  
    boost::fibers::algo::round_robin scheduler;  
  
public:  
    Scheduler(Scheduler*& self)  
    {  
        self=this;  
    }  
  
public:  
    virtual void awakened(boost::fibers::context* context, Properties&) noexcept // override  
    {  
        return scheduler.awakened(context);  
    }  
  
    virtual boost::fibers::context* pick_next() noexcept override  
    {  
        return scheduler.pick_next();  
    }  
  
    virtual bool has_ready_fibers() const noexcept override  
    {  
        return scheduler.has_ready_fibers();  
    }  
  
    virtual void suspend_until(std::chrono::steady_clock::time_point const& when) noexcept override  
    {  
        return scheduler.suspend_until(when);  
    }  
  
    virtual void notify() noexcept override  
    {  
        return scheduler.notify();  
    }  
  
public:  
    virtual boost::fibers::fiber_properties* new_properties(boost::fibers::context* context) override  
    {  
        std::cout << "new_properties(" << context << ")" << std::endl;  
        return new Properties(context, *this);  
    }  
  
    void wait()  
    {  
        boost::fibers::mutex mutex;  
        std::unique_lock<decltype(mutex)> lock(mutex);  
        condition.wait(lock, [this](){ return counter==1; });  
    }  
};  
  
/************************************************************************/  
  
Properties::Properties(boost::fibers::context* context, Scheduler& scheduler_)  
    : fiber_properties(context), scheduler(&scheduler_)  
{  
    scheduler->counter++;  
    std::cout << "Fiber count: " << scheduler->counter << std::endl;  
}  
  
/************************************************************************/  
  
Properties::~Properties()  
{  
    scheduler->counter--;  
    std::cout << "Fiber count: " << scheduler->counter << std::endl;  
    scheduler->condition.notify_one();  
}  
  
/************************************************************************/  
  
int main()  
{  
    std::cout << "BOOST_VERSION: " << BOOST_VERSION << std::endl;  
  
    std::thread([](){  
        Scheduler* scheduler=nullptr;  
        boost::fibers::use_scheduling_algorithm<Scheduler>(scheduler);  
        for (int i=0; i<5; i++)  
        {  
            boost::fibers::fiber([i](){  
                boost::this_fiber::sleep_for(std::chrono::seconds(2+i));  
            }).detach();  
        }  
        std::cout << "Waiting..." << std::endl;  
        scheduler->wait();  
        std::cout << "All done!" << std::endl;  
    }).join();  
    return 0;  
}  
```  
  
This is on a Debian "testing", to get a halfway-recent g++ and boost; I'm running this as:  
  
```  
stieber@debian:~$ g++ --version  
g++ (Debian 12.2.0-14) 12.2.0  
Copyright (C) 2022 Free Software Foundation, Inc.  
This is free software; see the source for copying conditions.  There is NO  
warranty; not even for MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  
  
stieber@debian:~$ g++ -ggdb -std=c++20 Test.cpp -l boost_fiber -l boost_context && ./a.out  
BOOST_VERSION: 108100  
new_properties(0x7f76cc020c00)  
Fiber count: 1  
new_properties(0x7f76d18c9f00)  
Fiber count: 2  
new_properties(0x7f76d18a8f00)  
Fiber count: 3  
new_properties(0x7f76d1887f00)  
Fiber count: 4  
new_properties(0x7f76d1866f00)  
Fiber count: 5  
new_properties(0x7f76d1845f00)  
Fiber count: 6  
Waiting...  
Fiber count: 5  
new_properties(0x7f76cc000b70)  
Fiber count: 6  
Fiber count: 5  
Fiber count: 4  
Fiber count: 3  
Fiber count: 2  
^C  
stieber@debian:~$  
```  
  
Up until "waiting", all is expected -- I launch 5 fibers, and I have my initial fiber, for a total of 6.  
  
Things start getting weird right away, though: I have no explanation for the "5 6 5" sequence, but I'm pretty sure I haven't started another fiber to explain the additional `new_properties()` that shows up in that phase. And, apparently, that ghost fiber doesn't end, so it's stuck at 2.  
  
Also, if I change the `wait()` call to wait for 2 instead of 1, it counts down to 0, then segfaults. Maybe the "fake" property that showed up, getting cleaned up after the scheduler or something like that. I'll probably just try to wait for the 2 and replace the `Scheduler*` with a `shared_ptr` construct for now -- even if this is indeed a bug and not just me being dumb again, it would take an estimated 5-10 years for a corrected version to show up on Debian... and I don't want to wait that long.  
  
Related observation:  
* if I put another fiber-related operation other than launching fibers into the main fiber, it it seems to create the ghost fiber/context on the first such call. For example, starting the thread with a simple `boost::this_fiber::sleep_for(...)` will create the fake fiber there, and the "waiting..." phase will just count down (but, of course, also stop at 2).  
* on the positive side, it appears to be "guaranteed" that there's exactly one such ghost fiber, so doing the workaround with waiting for 2 should be reliable. I should also be able to use this at runtime to determine whether the ghost fiber is created or not, so I can adapt to a potential change (doing a quick fiber-related call and taking the resulting fiber count as the base value to wait for before launching more fibers).  
  
Unreleated notes:  
* the documentation often leaves out the `algo::` namespace part  
* the documentation says `awakened` is `virtual`, but... it's not. That's why the `override` is commented in my code.
