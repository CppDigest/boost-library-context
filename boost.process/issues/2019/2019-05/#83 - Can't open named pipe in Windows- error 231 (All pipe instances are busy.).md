# #83 - [Windows][Pipes] Can't open named pipe in Windows: error 231 (All pipe instances are busy.) [Closed]

> Username: leha-bot  
> Created at: 2019-05-05 14:56:43 UTC  
> Updated at: 2022-05-15 23:57:38 UTC  
> Closed at: 2020-01-26 08:12:44 UTC  
> Url: https://github.com/boostorg/process/issues/83  

Hello! Thanks a lot for your library! I have an issue with Windows named pipes.  
Please see test repo for detais.  
  
How to reproduce:  
1. Compile client and server;  
2. Run server with pipe name (e.g. "\\.\pipe\test_pipe");  
3. Run client with same pipe name;  
4. Get an pipe open error 231.  
  
The test example can be found at https://github.com/leha-bot/test-boost-process-pipes

---

## Comment 1

> Username: klemens-morgenstern  
> Created at: 2019-05-05 15:30:53 UTC  
> Url: https://github.com/boostorg/process/issues/83#issuecomment-489436888  

I tend to think this is desired behaviour. Since I don't want a client to connect (e.g. by accident) I want the pipe to only be available in the process and it's subprocesses.  
  
What is your use-case?

---

## Comment 2

> Username: leha-bot  
> Created at: 2019-05-05 17:06:00 UTC  
> Url: https://github.com/boostorg/process/issues/83#issuecomment-489444554  

I use named pipes for sending data between processes. There is a server which creates named pipe and waits for input and there is a client which opens named pipe and writes data into

---

## Comment 3

> Username: klemens-morgenstern  
> Created at: 2019-05-05 20:00:35 UTC  
> Url: https://github.com/boostorg/process/issues/83#issuecomment-489458534  

Right, I think you're right if you use explicit naming. The implicit names (is you just create an `async_pipe` on windows without passing a name in) must not be opened from another process. Will look into it this. week.

---

## Comment 4

> Username: klemens-morgenstern  
> Created at: 2019-05-07 04:01:01 UTC  
> Url: https://github.com/boostorg/process/issues/83#issuecomment-489898669  

Heya, can you check if [this](https://github.com/klemens-morgenstern/boost-process/tree/win_pipe_unlimited_instances) solves the issue?

---

## Comment 5

> Username: anydream  
> Created at: 2019-06-24 08:13:15 UTC  
> Updated at: 2019-06-24 08:22:52 UTC  
> Url: https://github.com/boostorg/process/issues/83#issuecomment-504906991  

@klemens-morgenstern   
This merge is unable to be used, it lacks of calling `ConnectNamedPipe` makes it impossible to create connection across processes at all.

---

## Comment 6

> Username: anydream  
> Created at: 2019-06-24 08:15:31 UTC  
> Updated at: 2019-06-25 01:03:10 UTC  
> Url: https://github.com/boostorg/process/issues/83#issuecomment-504907752  

See https://docs.microsoft.com/en-us/windows/desktop/api/namedpipeapi/nf-namedpipeapi-connectnamedpipe  
  
https://docs.microsoft.com/en-us/windows/desktop/ipc/named-pipe-server-using-completion-routines  
  
https://docs.microsoft.com/en-us/windows/desktop/ipc/named-pipe-server-using-overlapped-i-o

---

## Comment 7

> Username: wildwild  
> Created at: 2019-07-09 02:26:34 UTC  
> Url: https://github.com/boostorg/process/issues/83#issuecomment-509461871  

I recommend it to support multiply processes like socket connect ,under windows it can be used as MSDN examples ,under linux I don't know whether it has such kind of effect or equal functions ^.^

---

## Comment 8

> Username: Xaymar  
> Created at: 2020-01-23 12:31:51 UTC  
> Url: https://github.com/boostorg/process/issues/83#issuecomment-577661900  

It seems that in Boost 1.72 there are still no calls to ConnectNamedPipe. I'm guessing I have to manually call that for now?

---

## Comment 9

> Username: anydream  
> Created at: 2020-01-26 03:14:04 UTC  
> Url: https://github.com/boostorg/process/issues/83#issuecomment-578464608  

@Xaymar I suggest you use libuv instead of call ConnectNamedPipe yourself.

---

## Comment 10

> Username: klemens-morgenstern  
> Created at: 2020-01-26 08:09:53 UTC  
> Url: https://github.com/boostorg/process/issues/83#issuecomment-578478533  

Named pipes are not a portable feature, since there is no equivalent on windows (Domain Sockets fill the role, but work quite different). If you want to to use async-pipe servers and stuff, you can do that with boost.asio, by using the normal winapi functions and then assign the open pipe to a [`stream_handle`](https://www.boost.org/doc/libs/1_72_0/doc/html/boost_asio/reference/windows__stream_handle.html)  
  
The way I am using them is like unnamed pipes, that need overlapped I/O. Actual unnamed pipes on windows do not support that, while unix ones do.  
  
I think a library that wraps around named pipes as you want them AND unix domain sockets would be useful, but falls more under IPC than this process library. The pipes here are mainly meant for parent-child I/O.  
  
Also sorry for the long delay, I have been utterly swamped with work.

---

## Comment 11

> Username: capsocrates  
> Created at: 2021-01-26 00:06:22 UTC  
> Updated at: 2021-01-26 00:08:07 UTC  
> Url: https://github.com/boostorg/process/issues/83#issuecomment-767192500  

> Named pipes are not a portable feature, since there is no equivalent on windows  
  
Did you mean unnamed pipes? Windows literally does have named pipes; [anydream linked to some of the documentation for them](https://github.com/boostorg/process/issues/83#issuecomment-504907752).  
  
I see you were saying that unnamed pipes in Windows do not support overlapped I/O. I can't comment on unnamed pipes in that regard, but the first link anydream referred to is how to write overlapped I/O with *named* pipes in Windows.

---

## Comment 12

> Username: Capital-Z  
> Created at: 2022-05-15 23:57:38 UTC  
> Url: https://github.com/boostorg/process/issues/83#issuecomment-1127090833  

>  The pipes here are mainly meant for parent-child I/O.  
  
It would be helpful to mention this in the documentation. The way I read it, it looks quite the opposite. I spent some time figuring out how to do IPC till I found this. It might help the next guy if such limitation was made more visible.
