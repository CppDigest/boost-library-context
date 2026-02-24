# #106 - Could multi-process write the same file use boost.log? [Closed]

> Username: mozeat-sun  
> Created at: 2020-03-04 03:21:05 UTC  
> Updated at: 2020-03-04 10:32:21 UTC  
> Closed at: 2020-03-04 10:31:32 UTC  
> Url: https://github.com/boostorg/log/issues/106  

hi   
   i just not sure about the question,when i apply it in my project  
    
   here is the case is :  
   1). a dynamic library based on boost.log for logging message to a file ;  
   2). multi-process will load the library at the same environment.  
  
   my question is  when multi-process work the same time(log) ,can the file would be wrote correctly?

---

## Comment 1

> Username: Lastique  
> Created at: 2020-03-04 10:31:32 UTC  
> Updated at: 2020-03-04 10:32:21 UTC  
> Url: https://github.com/boostorg/log/issues/106#issuecomment-594442956  

Boost.Log does not support multiple processes writing to the same file or collecting their files to the same directory. You will have to write [your own sink backend](https://www.boost.org/doc/libs/1_72_0/libs/log/doc/html/log/extension.html#log.extension.sinks) that implements inter-process synchronization. Alternatively, you could use [inter-process communication](https://www.boost.org/doc/libs/1_72_0/libs/log/doc/html/log/detailed/sink_backends.html#log.detailed.sink_backends.text_ipc_message_queue) or [syslog](https://www.boost.org/doc/libs/1_72_0/libs/log/doc/html/log/detailed/sink_backends.html#log.detailed.sink_backends.syslog) to collect log records from all your processes to a single logger process, which will then write them into files.
