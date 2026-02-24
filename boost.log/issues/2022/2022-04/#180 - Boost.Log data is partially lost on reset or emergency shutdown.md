# #180 - Boost.Log data is partially lost on reset or emergency shutdown [Closed]

> Username: ikorneev  
> Created at: 2022-04-14 13:24:06 UTC  
> Updated at: 2022-04-18 22:34:52 UTC  
> Closed at: 2022-04-18 08:41:30 UTC  
> Url: https://github.com/boostorg/log/issues/180  

Boost log loses some entries if OS crashes or is reset by user, that data is replaced by nulls  
![image](https://user-images.githubusercontent.com/87642505/163399007-4472c69c-2a4f-486b-a51a-83b16e0df54f.png)  
Though those entries were shown in log file, they are (30+ lines, about 2 minutes of logging) lost after reset like file was being edited but not saved. Auto_flush option doesn't help in this case.  
  
Using text_file_backend, tried to set zero buf in implementation (by putting this `m_pImpl->m_File.rdbuf()->pubsetbuf(0, 0);` before `m_pImpl->m_File.open(...)` with no positive outcome.  
  
Adding `FILE_FLAG_WRITE_THROUGH | FILE_FLAG_NO_BUFFERING` flags to CreateFile functions doesn't help too.  
  
Any suggestions? Or is that by desing?

---

## Comment 1

> Username: Lastique  
> Created at: 2022-04-18 08:41:30 UTC  
> Updated at: 2022-04-18 08:47:19 UTC  
> Url: https://github.com/boostorg/log/issues/180#issuecomment-1101224940  

As soon as Boost.Log hands off the formatted content to the OS, it is OS' responsibility to reliably store it. There is no case (short of hardware error) when Boost.Log corrupts the data it passes to the OS with zeros - it will either pass the buffered data it has, or it doesn't. The buffering may be either in the sink frontend (if it is async frontend) or in the file stream (which you automatically flush). The data may also be in flight (e.g. being processed by the library) when the system shuts down. But in any case, at the point of passing it to the OS the data is valid, and what didn't manage to get to the OS will be definitely lost (and there's no way around it).  
  
I think, what you're seeing is filesystem corruption. I do not see what Boost.Log can do about this, sorry.  
  
> Adding `FILE_FLAG_WRITE_THROUGH | FILE_FLAG_NO_BUFFERING` flags to CreateFile functions doesn't help too.  
  
Boost.Log doesn't call `CreateFile`, so I'm not sure what you did here.

---

## Comment 2

> Username: ikorneev  
> Created at: 2022-04-18 09:00:37 UTC  
> Updated at: 2022-04-18 09:29:25 UTC  
> Url: https://github.com/boostorg/log/issues/180#issuecomment-1101237728  

OS - Windows 10 Pro, some other logs i used previously dont lose last logging info on that system (some of them lack rotation, though, which is good enough in boost), so i won't be so sure that it is 100% OS fault. Most of them use winapi and write directly to log file instead of using ofstream though, perhaps that could lead to data loss in boost.  
   
@Lastique   
Well, could you help me by some clarification about log file creation?  
It is created (if doesn't exist) when   
`m_pImpl->m_File.open(new_file_name, m_pImpl->m_FileOpenMode);`  
is called, am i right? (for text file backend at least)

---

## Comment 3

> Username: Lastique  
> Created at: 2022-04-18 10:04:56 UTC  
> Updated at: 2022-04-18 10:05:51 UTC  
> Url: https://github.com/boostorg/log/issues/180#issuecomment-1101281547  

> OS - Windows 10 Pro, some other logs i used previously dont lose last logging info on that system  
  
I don't think this is information lost. I think it is more likely incorrect tracking of file size on system crash. If the file is open during the crash, the OS may not write the correct file size to the filesystem. Then it gets restored to whatever value rounded up to the number of allocated sectors upon recovery, filling the excess with zeros.  
  
You can try `multifile_text_backend`. That backend opens and closes the log file on each log record.  
  
> It is created (if doesn't exist) when  
> `m_pImpl->m_File.open(new_file_name, m_pImpl->m_FileOpenMode);`  
> is called, am i right?  
  
Yes.

---

## Comment 4

> Username: ikorneev  
> Created at: 2022-04-18 13:41:24 UTC  
> Updated at: 2022-04-18 13:41:48 UTC  
> Url: https://github.com/boostorg/log/issues/180#issuecomment-1101418053  

Sadly, `multifile_text_backend` doesn't support rotation, which is kinda important part of logging lib i'm looking for. But perhaps with some tambourine-dancing...

---

## Comment 5

> Username: ikorneev  
> Created at: 2022-04-18 22:04:33 UTC  
> Updated at: 2022-04-18 22:06:21 UTC  
> Url: https://github.com/boostorg/log/issues/180#issuecomment-1101804644  

@Lastique i've fixed this issue by calling `close_file()` in the end of `text_file_backend::consume` (with 100ms+ between each to lessen disk overload).   
```  
    if (m_pImpl->m_AutoFlush)  
        m_pImpl->m_File.flush();      
    const auto mseconds_now = duration_cast<milliseconds>(system_clock::now().time_since_epoch()).count();  
    if (mseconds_now-m_pImpl->m_last_closing_time>=100)  
    {  
        close_file();  
        m_pImpl->m_last_closing_time = mseconds_now;  
    }  
```  
Perhaps it's worth it to add some flag like `auto_flush` to future versions (and timing attribute or counter between entries) to prevent such OS attitude?

---

## Comment 6

> Username: Lastique  
> Created at: 2022-04-18 22:16:03 UTC  
> Url: https://github.com/boostorg/log/issues/180#issuecomment-1101810590  

I don't think it's worth it. Ultimately, this is an OS issue, and that's where it should be fixed. Note that closing the file is not a 100% reliable solution, as there is still some amount of time when the file is open, and a system crash at this point could produce the same filesystem corruption.  
  
What could be useful is to use low level system APIs to writing files instead of iostreams, with custom implementation of buffering. This would allow to make it more deterministic as to which data is passed to the OS and eliminate the possibility of a partial log record being passed to the OS. But this is not going to eliminate the possibility of filesystem corruption in case of a system crash, and Boost.Log is not going to be able to guarantee that anyway.

---

## Comment 7

> Username: ikorneev  
> Created at: 2022-04-18 22:34:52 UTC  
> Url: https://github.com/boostorg/log/issues/180#issuecomment-1101821313  

Well, it's better to lose last 1-2 log entries than 100-200 at least (or 100-200 instead of 1000-2000 etc, depends on log entries amount). However, i'm mot sure that Microsoft is going to fix this issue anywhere except Win11 too.  
Thanks for pointing me to the right direction anyway!
