# #163 - boost message queue locked when one process crashes [Closed]

> Username: z23han  
> Created at: 2021-12-16 02:13:29 UTC  
> Updated at: 2021-12-26 22:06:50 UTC  
> Closed at: 2021-12-26 22:06:50 UTC  
> Url: https://github.com/boostorg/interprocess/issues/163  

Hi, boost community,   
  
I have an issue related to boost shared memory lock, hopefully someone can give me some helps.   
  
I used boost message_queue for producer-consumer model, basically multiple producers-single consumer. I need to re-open/resume the shared message queue if the consumer is killed/crashed for some reasons. So I have the following piece of code   
  
if (resume)  
{  
    m_queue = new boost::interprocess::message_queue(  
        boost::interprocess::open_only,   
        m_bundle->config.td_request_queue_name  
    );  
}  
else  
{      
    boost::interprocess::message_queue::remove(  
        m_bundle->config.td_request_queue_name  
    );  
     
    m_queue = new boost::interprocess::message_queue(  
        boost::interprocess::create_only,  
        m_bundle->config.td_request_queue_name,  
        MAX_NUM_MSG,  
        TD_MSG_SIZE  
    );  
}  
  
I noticed occasionally if I resumed the queue, I was locked at receiving messages or getting the size of the queue.   
After some investigation, I'm thinking it's likely that the lock isn't released when consumer is doing its try_receive. My consumer is consuming the queue in a while (true) like format, so it can happen sometimes.   
  
in the interprocess/ipc/message_queue.hpp, try_receive, try_send, get_num_msg all need to grab the interprocess_mutex   
scoped_lock<interprocess_mutex> lock(p_hdr->m_mutex);  
  
Is there a way to release the lock if the process restarts?   
I think the lock flag is on the shared memory, so wondering if there's a way to unlock if it's being grabbed when re-opening the shared memory address?   
  
Hope someone can shed some lights, thanks!!

---

## Comment 1

> Username: igaztanaga  
> Created at: 2021-12-26 22:06:50 UTC  
> Url: https://github.com/boostorg/interprocess/issues/163#issuecomment-1001247341  

Interprocess message queues don't support recovering from those situations, unless your platform supports robust mutexes (like in recent POSIX systems). Support for robust mutexes was added in Boost 1.78.
