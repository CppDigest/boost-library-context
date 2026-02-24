# #99 - boost/log throw exception [Closed]

> Username: 0x007004  
> Created at: 2020-02-07 02:37:30 UTC  
> Updated at: 2020-02-07 11:26:51 UTC  
> Closed at: 2020-02-07 11:26:50 UTC  
> Url: https://github.com/boostorg/log/issues/99  

I use boost 1.57.0 .  
  
- 1. start my program , will write log (use boost/log).  
- 2. delete the current log file , which is the program is writing.  
- 3. the program will  termination,because the file will close first  and then will call "last_write_time" but the file has been deleted ,so it throw "no file or dictionary" ,and the "errno = 2".  
  
question:  
- what can I do about this ?   
now I can do only guarantee don`t delete the last file .    
   
thank you

---

## Comment 1

> Username: Lastique  
> Created at: 2020-02-07 11:26:50 UTC  
> Url: https://github.com/boostorg/log/issues/99#issuecomment-583350847  

The exception indicates failure to rotate the log file. You can ignore exceptions thrown by the library by setting up an [exception handler](https://www.boost.org/doc/libs/1_72_0/libs/log/doc/html/log/detailed/utilities.html#log.detailed.utilities.exception_handlers) to the sink or the logging core.  
  
But you should also avoid messing with the log files while the application is running. Boost.Log does not detect filesystem changes by external parties, so it will not, for example, re-create the log file it was writing, or it will not update its information about the number and total size of the rotated files in the target storage. You will likely lose logs (aside from what was already written in the deleted files) and may see unexpected behavior on log rotation.
