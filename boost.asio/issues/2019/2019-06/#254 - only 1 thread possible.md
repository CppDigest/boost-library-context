# #254 - only 1 thread possible [Closed]

> Username: xNxExOx  
> Created at: 2019-06-21 13:12:45 UTC  
> Updated at: 2019-06-21 16:55:02 UTC  
> Closed at: 2019-06-21 16:55:01 UTC  
> Url: https://github.com/boostorg/asio/issues/254  

We have very weird problem, we manage to create very small somehow reproducible(on some PCs) example:  
[main.cpp](https://pastebin.com/vBf11LS1) [Dockerfile](https://pastebin.com/z98J1ajF)  
We use boost 1.70 (Asio 1.14.0)  
  
I tested on 3 computes so far:  
  
my desktop Ryzen 1950X 64 GB RAM mostly end up with:  
---  
```root@ac8be398de48:/app/test/bin# ./test  
started for 1 threads  
Listener started on thread: 140593064609536  
started for 2 threads  
started for 3 threads  
started for 4 threads  
started for 5 threads  
started for 6 threads  
started for 7 threads  
```  
(sometimes 2, or 3 also works, but mostly not)  
I tested on this machine also build with msvc on Windows and result was that it run fine, so the problem is somehow linux+core count specific.  
  
main server 2x E5-2630 v3 128 GB RAM mostly end up with:  
---  
```  
root@cf8c892390ce:/app/test/bin# ./test  
started for 1 threads  
Listener started on thread: 140062574507776  
started for 2 threads  
started for 3 threads  
started for 4 threads  
started for 5 threads  
started for 6 threads  
started for 7 threads  
```  
(once in over 100 tests 2 also worked, but never more)  
  
old test server 2x old intel 2 core CPU 4 GB RAM most results look like this:  
---  
```  
root@f06821a4cbc8:/app/test/bin# ./test  
started for 1 threads  
Listener started on thread: 140650646316800  
started for 2 threads  
Listener started on thread: 140650621138688  
started for 3 threads  
Listener started on thread: 140650246829824  
started for 4 threads  
Listener started on thread: 140650213259008  
started for 5 threads  
Listener started on thread: 140649944823552  
started for 6 threads  
Listener started on thread: 140649743496960  
started for 7 threads  
Listener started on thread: 140649726711552  
```  
(sometimes 5, 6, or 7 threads do not work)  
  
We tested on few other systems and only 1 thread works reliably.  
  
Can you please look at the code and tell me if we have some stupid mistake there?, or if this is bug in asio?  
  
And most importantly can we fix it somehow?

---

## Comment 1

> Username: xNxExOx  
> Created at: 2019-06-21 16:55:01 UTC  
> Url: https://github.com/boostorg/asio/issues/254#issuecomment-504496029  

solved on [stackoverflow](https://stackoverflow.com/questions/56704296/only-1-thread-possible-with-boost-asio-on-machines-with-many-cores) my mistake :(
