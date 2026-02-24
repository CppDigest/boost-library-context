# #816 - Suggestions about then(), when_all() and when_any() functions (like in concurrency TS) [Open]

> Username: Kristian-Popov  
> Created at: 2019-01-21 11:10:26 UTC  
> Updated at: 2019-01-21 15:18:02 UTC  
> Url: https://github.com/boostorg/compute/issues/816  

To whom it may concern,  
  
I was recently looking at concurrency TS for C++ [1] and then thought about a possibility to add similar functions to Boost compute futures.  
In fact, boost::compute::future already has then().  
I don't really like that this method returns future itself, which doesn't represent if callback finished or not.  
then() could return a future (either std or boost) built from a promise, callback will set a value for a promise at the end. Promise will have to be stored  somewhere, e.g. as a vector in future whose then() is called.  
  
I believe the easiest way to add when_all() is that is would be a redirection for boost::compute::command_queue::enqueue_marker() with a wait list. This will simplify for user to understand what this function does (e.g. one doesn't have to investigate difference between markers and barriers).  
This implementation is enough for many cases but user may want to make a future that waits for events from multiple queues (and possibly different devices or even platforms). I suggest the following algorithm (similar to select() in POSIX [2]):  
given event/future list to watch for:  
1. Check status for first event (using status()), if it is completed go to next event (or step 4), otherwise go to step 2  
2. If there are any unfinished events, make current thread sleep (possibly with increasing timeout up to some max amount)  
3. Go to step 1  
4. If all events are finished, call wait() on all of them (checking status() is not considered a synchronization point [3]).  
5. Done  
  
when_any() is going to be a little more interesting, algorithm could be something like this:  
1. Check status for first event (using status()), if it is completed go to step 4, otherwise go to next event (or step 2)  
2. If all events are in progress, make current thread sleep (possibly with increasing timeout up to some max amount)  
3. Go to step 1  
3. At least one event is finished, call wait() on it.  
4. Done  
  
I will have some time to implement all of this, feedback is very appreciated.  
  
[1] Concurrency TS https://en.cppreference.com/w/cpp/experimental/concurrency).  
[2] http://man7.org/linux/man-pages/man2/select.2.html  
[3] The OpenCL Specification Version: 2.2, 5.11, p. 196
