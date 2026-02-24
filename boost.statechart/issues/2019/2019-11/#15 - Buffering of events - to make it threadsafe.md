# #15 - Buffering of events - to make it threadsafe [Open]

> Username: Laro88  
> Created at: 2019-11-13 14:32:53 UTC  
> Updated at: 2019-11-13 14:32:53 UTC  
> Url: https://github.com/boostorg/statechart/issues/15  

I am trying to familiarize myself sc, however I keep ending up with issues.  
  
I have multiple threads that generate events and I am trying to buffer the events in a std::queue and have a thread call process_event with whatever is in the queue.  
   
```  
//typedef  
typedef boost::statechart::state_machine<SMVesselServer, SVesselServer>::event_base_type VSEvent;   
--  
//queue def  
std::queue<std::shared_ptr<VSEvent>> events;  
--  
//enqueue function  
void HandleEvent(std::shared_ptr<VSEvent> e);  
```  
  
  
Does this approach make sense at all - any references to thread safety would be great.  
  
Besides, getting interactions with the outside world from the states seems non trivial. I have a static struct with thread safe getters and setters at the moment.
