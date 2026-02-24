# #117 - Using boost.log inside docker container [Closed]

> Username: Avm07  
> Created at: 2020-04-20 13:19:44 UTC  
> Updated at: 2020-04-20 15:11:42 UTC  
> Closed at: 2020-04-20 14:19:06 UTC  
> Url: https://github.com/boostorg/log/issues/117  

Seems like boost.log has troubles with launching inside docker container.  
  
Problem is then i launch my cpp app direct from docker container or local logs prints successful. Boost version 1.70.  
  
```  
root@e75cf463d108:/app/build# ./blockchain-wallet 8089  
  
[2020-04-20 13:15:38.058084] [0x00007fa457326780] [info]    blockchain-wallet service start  
[2020-04-20 13:15:38.058299] [0x00007fa457326780] [info]    Waiting for new request...  
```  
  
But then i launch it from docker-compose, console logs are empty, same situation then i launching my app in kubernetes. I didnt understand problem is in std::cout or virtual terminal? How to forward logs to std::cout?

---

## Comment 1

> Username: Lastique  
> Created at: 2020-04-20 14:19:06 UTC  
> Url: https://github.com/boostorg/log/issues/117#issuecomment-616584761  

I'm sorry, I have no idea how Docker works. If it intercepts or blocks console output, you need to find a way to change that behavior. Or configure Boost.Log so that it writes logs into a file e.g. by using a [text file](https://www.boost.org/doc/libs/1_72_0/libs/log/doc/html/log/detailed/sink_backends.html#log.detailed.sink_backends.text_file) sink backend.

---

## Comment 2

> Username: Avm07  
> Created at: 2020-04-20 14:47:54 UTC  
> Url: https://github.com/boostorg/log/issues/117#issuecomment-616602371  

> I'm sorry, I have no idea how Docker works. If it intercepts or blocks console output, you need to find a way to change that behavior. Or configure Boost.Log so that it writes logs into a file e.g. by using a [text file](https://www.boost.org/doc/libs/1_72_0/libs/log/doc/html/log/detailed/sink_backends.html#log.detailed.sink_backends.text_file) sink backend.  
  
Can u answer on this too pls?  
https://stackoverflow.com/questions/42792727/boost-log-output-can-not-be-redirected  
  
Seems to be same problem.

---

## Comment 3

> Username: Lastique  
> Created at: 2020-04-20 15:11:41 UTC  
> Url: https://github.com/boostorg/log/issues/117#issuecomment-616617175  

I've already commented on that question, and I don't know what that problem is.
