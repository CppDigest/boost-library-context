# #260 - Stupid errors in documentation [Closed]

> Username: heretic13  
> Created at: 2025-04-14 16:14:47 UTC  
> Updated at: 2025-04-15 12:16:34 UTC  
> Closed at: 2025-04-15 12:16:34 UTC  
> Url: https://github.com/boostorg/interprocess/issues/260  

Hello.  
  
Document - https://www.boost.org/doc/libs/1_88_0/doc/html/interprocess/sharedmemorybetweenprocesses.html  
  
contain something similar:  
  
```  
using boost::interprocess;  
  
shared_memory_object shm_obj  
   (create_only                  //only create  
   ,"shared_memory"              //name  
   ,read_write                   //read-write mode  
   );  
```  
  
6 entries like this further down in the document.

---

## Comment 1

> Username: igaztanaga  
> Created at: 2025-04-14 19:26:12 UTC  
> Url: https://github.com/boostorg/interprocess/issues/260#issuecomment-2802781357  

I don't understand this report, could you clarify?

---

## Comment 2

> Username: heretic13  
> Created at: 2025-04-15 07:42:20 UTC  
> Url: https://github.com/boostorg/interprocess/issues/260#issuecomment-2804126972  

"using boost::interprocess;"  
  
or  
  
using namespace boost::interprocess;

---

## Comment 3

> Username: igaztanaga  
> Created at: 2025-04-15 12:16:13 UTC  
> Url: https://github.com/boostorg/interprocess/issues/260#issuecomment-2804851890  

Ah, thanks for the clarification, you are absolutely right, will fix it.
