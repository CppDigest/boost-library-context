# #314 - Iteration through directory fails on Linux, if file is removed during iteration [Closed]

> Username: martin5233  
> Created at: 2024-06-27 07:46:08 UTC  
> Updated at: 2024-06-29 11:52:33 UTC  
> Closed at: 2024-06-29 11:52:33 UTC  
> Url: https://github.com/boostorg/filesystem/issues/314  

The following small test program fails on Linux with Boost 1.85, but not with 1.81:  
  
```  
bfs::ofstream out("/tmp/mytest");  
out.close();  
  
bfs::directory_iterator it("/tmp");  
bfs::remove("/tmp/mytest");  
  
while(it != bfs::directory_iterator())  
 {  
     if (bfs::is_regular_file(it->status()))  
     {  
     }  
   ++it;  
}  
```  
  
While the code above does not make much sense, it also occurs, if a file is removed by a separate process, while a directory iteration is ongoing. The generated exception is the following:  
```  
terminate called after throwing an instance of 'boost::filesystem::filesystem_error'  
  what():  boost::filesystem::directory_entry::refresh: No such file or directory [system:2]: "/tmp/mytest"  
```

---

## Comment 1

> Username: Lastique  
> Created at: 2024-06-29 11:18:10 UTC  
> Url: https://github.com/boostorg/filesystem/issues/314#issuecomment-2198110044  

Thanks for the report. I've made a change that should fix the issue in your test example.  
  
> While the code above does not make much sense, it also occurs, if a file is removed by a separate process, while a directory iteration is ongoing.  
  
Note that filesystem modifications concurrent with Boost.Filesystem operations constitute a filesystem race, and there are no guarantees on the operation results in these conditions.
