# #2190 - Can't get a simple synchronous server to work [Closed]

> Username: paulSerre  
> Created at: 2021-03-15 19:59:32 UTC  
> Updated at: 2022-01-09 05:17:23 UTC  
> Closed at: 2022-01-09 05:17:23 UTC  
> Url: https://github.com/boostorg/beast/issues/2190  

### Version of Beast  
  
306  
  
### Steps necessary to reproduce the problem  
  
Just try to run a server with the file https://www.boost.org/doc/libs/develop/libs/beast/example/http/server/sync/http_server_sync.cpp  
  
Hello,  
  
I'm new with ASIO and beast. I need to make a simple HTTP server for sending images from a client.   
Thing is ,I can't even make the example work (http_server_sync.cpp [here](https://www.boost.org/doc/libs/develop/libs/beast/example/http/server/sync/http_server_sync.cpp)).   
I just run the server on 0.0.0.0 port 8080 and do curl `localhost:8080`. What I get is a server that stops without any error message and without response... Seems that te problem come from the "send" call in handle_request, but I cannot figure out why...  
  
It's been a while I didn't do C++ so if somebody can help me with that I would be grateful...

---

## Comment 1

> Username: madmongo1  
> Created at: 2021-03-15 20:05:34 UTC  
> Url: https://github.com/boostorg/beast/issues/2190#issuecomment-799715489  

- What was the exact curl command line you entered?  
- Was there any output to stdout or stderror?

---

## Comment 2

> Username: paulSerre  
> Created at: 2021-03-15 20:10:20 UTC  
> Updated at: 2021-03-15 20:37:30 UTC  
> Url: https://github.com/boostorg/beast/issues/2190#issuecomment-799718354  

Hi,   
  
Thanks for your answer.  
  
- Just a `curl http://localhost:8080` with the following output : `(56) Recv failure: Connection was reset`  
- I get warning when I build (seems normal), but don't have any out or error, even when running the server

---

## Comment 3

> Username: vinniefalco  
> Created at: 2021-03-15 21:14:43 UTC  
> Url: https://github.com/boostorg/beast/issues/2190#issuecomment-799756903  

Are you pointing the server to a directory with HTML files? And an index.html ?

---

## Comment 4

> Username: paulSerre  
> Created at: 2021-03-15 21:18:21 UTC  
> Updated at: 2021-03-15 21:20:18 UTC  
> Url: https://github.com/boostorg/beast/issues/2190#issuecomment-799758948  

Yes, I do with the commande line `./SyncServer.exe 0.0.0.0 8080 .` in the directory which contains both the SyncServer.exe and the index.html file.  
Such an error would have return me an error since it's well-coded. But in my case, I don't have anything..

---

## Comment 5

> Username: madmongo1  
> Created at: 2021-03-15 21:21:06 UTC  
> Url: https://github.com/boostorg/beast/issues/2190#issuecomment-799760482  

Last thing before I try it locally.   
  
If you enable debug output from curl, what do you get?

---

## Comment 6

> Username: paulSerre  
> Created at: 2021-03-15 21:27:38 UTC  
> Updated at: 2021-03-15 21:28:47 UTC  
> Url: https://github.com/boostorg/beast/issues/2190#issuecomment-799764030  

With verbosity -v:  
  
` C:\Users\pserre>curl -v localhost:8080                                                                                  * Rebuilt URL to: localhost:8080/                                                                                       *   Trying ::1...                                                                                                       * TCP_NODELAY set                                                                                                       *   Trying 127.0.0.1...                                                                                                 * TCP_NODELAY set                                                                                                       * Connected to localhost (127.0.0.1) port 8080 (#0)                                                                     > GET / HTTP/1.1                                                                                                        > Host: localhost:8080                                                                                                  > User-Agent: curl/7.55.1                                                                                               > Accept: */*                                                                                                           >                                                                                                                       * Recv failure: Connection was reset                                                                                    * Closing connection 0                                                                                                  curl: (56) Recv failure: Connection was reset   `  
  
With --trace :  
`C:\Users\pserre>curl --trace - localhost:8080                                                                           == Info: Rebuilt URL to: localhost:8080/                                                                                == Info:   Trying ::1...                                                                                                == Info: TCP_NODELAY set                                                                                                == Info:   Trying 127.0.0.1...                                                                                          == Info: TCP_NODELAY set                                                                                                == Info: Connected to localhost (127.0.0.1) port 8080 (#0)                                                              => Send header, 78 bytes (0x4e)                                                                                         0000: 47 45 54 20 2f 20 48 54 54 50 2f 31 2e 31 0d 0a GET / HTTP/1.1..                                                  0010: 48 6f 73 74 3a 20 6c 6f 63 61 6c 68 6f 73 74 3a Host: localhost:                                                  0020: 38 30 38 30 0d 0a 55 73 65 72 2d 41 67 65 6e 74 8080..User-Agent                                                  0030: 3a 20 63 75 72 6c 2f 37 2e 35 35 2e 31 0d 0a 41 : curl/7.55.1..A                                                  0040: 63 63 65 70 74 3a 20 2a 2f 2a 0d 0a 0d 0a       ccept: */*....                                                    == Info: Recv failure: Connection was reset                                                                             == Info: Closing connection 0                     `

---

## Comment 7

> Username: vinniefalco  
> Created at: 2021-03-15 21:28:37 UTC  
> Updated at: 2021-03-15 21:28:49 UTC  
> Url: https://github.com/boostorg/beast/issues/2190#issuecomment-799764565  

The http-server-sync example works for me, with this command line :  
```  
http-server-sync.exe 0.0.0.0 8080 C:\Users\vinnie\src\www  
```  
This is version 310. Tested with Chrome.

---

## Comment 8

> Username: paulSerre  
> Created at: 2021-03-15 21:34:48 UTC  
> Url: https://github.com/boostorg/beast/issues/2190#issuecomment-799768261  

I have Visual Studio 2019 with  C++17, could be a problem ?

---

## Comment 9

> Username: vinniefalco  
> Created at: 2021-03-15 22:07:26 UTC  
> Url: https://github.com/boostorg/beast/issues/2190#issuecomment-799786782  

I used Visual Studio 2019. Should not be a problem.

---

## Comment 10

> Username: paulSerre  
> Created at: 2021-03-16 10:57:59 UTC  
> Updated at: 2021-03-16 11:08:22 UTC  
> Url: https://github.com/boostorg/beast/issues/2190#issuecomment-800159803  

Since it doesn't work on my side, I'm going to work with [websocket](https://www.boost.org/doc/libs/develop/libs/beast/example/websocket/server/async/websocket_server_async.cpp) which is enough in my case.  
I have now the same issue as #2014 even with VS2019 (don't know if i'm gonna have an example working one day..)., but with VS2019... Hasn't this bug fixed previously ?  
  
[EDIT] I made it work by changing from c++17 to c++14. It's weird, doesn't it?

---

## Comment 11

> Username: madmongo1  
> Created at: 2021-03-16 11:34:54 UTC  
> Url: https://github.com/boostorg/beast/issues/2190#issuecomment-800183353  

What happens if you make it `/std:latest` ?

---

## Comment 12

> Username: vinniefalco  
> Created at: 2021-03-16 16:40:36 UTC  
> Url: https://github.com/boostorg/beast/issues/2190#issuecomment-800424290  

>  I'm going to work with websocket which is enough in my case.  
  
You need to figure out why it isn't working, otherwise you will be unable to explain or diagnose your program(s) if it happens again. Unsolved mysteries have no place in computer science.

---

## Comment 13

> Username: stale[bot]  
> Created at: 2021-05-29 16:10:54 UTC  
> Url: https://github.com/boostorg/beast/issues/2190#issuecomment-850857852  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 14

> Username: stale[bot]  
> Created at: 2022-01-09 05:16:58 UTC  
> Url: https://github.com/boostorg/beast/issues/2190#issuecomment-1008232412  

It looks like this issue has either been abandoned or resolved so I will go ahead and close it. Feel free to re-open this issue if you feel it needs attention, or open new issues as needed. Thanks!
