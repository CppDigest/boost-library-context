# #2845 - Chat-Multi Code of Boost Beast is crashing when made some changes. [Closed]

> Username: shivank1111  
> Created at: 2024-03-28 10:27:02 UTC  
> Updated at: 2024-05-06 10:14:25 UTC  
> Closed at: 2024-05-06 08:03:36 UTC  
> Url: https://github.com/boostorg/beast/issues/2845  

Boost version: 1.83.0  
  
In the github chat-multi code :   
https://github.com/boostorg/beast/tree/develop/example/websocket/server/chat-multi  
  
Here in this function "void shared_state:: send(std::string message)" of file "shared_state.cpp" we are using   
weak_ptr, and at some places in the chat-multi .cpp and .hpp files we used raw pointers so I changed that to smart   
pointers to maintain uniformity because that would solve my memory management issues, but after my changes   
regarding URL access and smart pointers, my code is crashing by throwing this error:  
  
terminate called after throwing an instance of 'std::bad_weak_ptr'  
what():  bad_weak_ptr  
Aborted (core dumped)  
  
The changes I have made regarding websocket channel accessing by a URL is that if we want to open the channel again   
with the same URL then I close the before one and then only open the new one after passing the message to the   
older channel that "Old Closed, New Opened". For sending the message before closing the channel I have used   
lambda function.   
  
If I am opening new channels then it is working fine but I am trying to open the channel with the same URL again  
then it is crashing.   
  
Kindly help me with the same.

---

## Comment 1

> Username: shivank1111  
> Created at: 2024-03-28 13:03:58 UTC  
> Updated at: 2024-03-29 04:29:04 UTC  
> Url: https://github.com/boostorg/beast/issues/2845#issuecomment-2025136055  

Hi, I made many changes like adding the callback function, URL checking etc., in the chat-multi code. I have attached the files that you can run and check and help me with the issue.  
  
You can compile using:  
```g++ -std=c++17 boost_beast_server.cpp -o beast -lpthread -lboost_system -lboost_filesystem```  
  
To access the websocket channel you can use :   
"ws://I.P:Port/ws/xyz/github/boost/?"  
  
Just we need to open two channel one after other with the exact same URL then only that crash will happen.  
  
  
NOTE: I have the extensions you can change it after downloading from .txt to   
boost_beast_server.cpp  
[boost_beast_server.txt](https://github.com/boostorg/beast/files/14789730/boost_beast_server.txt)  
  
shared_state.hpp  
[shared_state.txt](https://github.com/boostorg/beast/files/14789731/shared_state.txt)  
  
websocket_session.hpp  
[websocket_session.txt](https://github.com/boostorg/beast/files/14789732/websocket_session.txt)  
  
  
Kindly message me if need anything apart from these.

---

## Comment 2

> Username: ashtum  
> Created at: 2024-04-04 07:58:51 UTC  
> Url: https://github.com/boostorg/beast/issues/2845#issuecomment-2036453550  

@shivank1111, a bad_weak_ptr exception indicates you likely have lifetime issues in your code. Try running the code in your debugger to pinpoint where the exception is thrown; this will provide clues as to why it occurs.

---

## Comment 3

> Username: shivank1111  
> Created at: 2024-05-06 08:52:44 UTC  
> Url: https://github.com/boostorg/beast/issues/2845#issuecomment-2095488416  

Hi @ashtum, I tried by gdb(bt full command) also but it was not giving any particular error. I tried several times still was not able to find the real reason.   
  
If possible, Kindly help me with the same.   
  
  
NOTE: I was out with some other work so was not able to do it.

---

## Comment 4

> Username: ashtum  
> Created at: 2024-05-06 10:10:12 UTC  
> Url: https://github.com/boostorg/beast/issues/2845#issuecomment-2095628993  

There are multiple life time issues in your code:  
  
These functions are taking reference to a shared pointer while the shared pointer itself is not stored anywhere:  
```CPP  
void send(std::shared_ptr<std::string const> const& ss, std::function<void()> on_sent = nullptr);  
void on_send(std::shared_ptr<std::string const> const& ss, std::function<void()> on_sent);  
```  
  
Your are calling `shared_from_this()` in the destructor:  
```CPP  
	websocket_session:: ~websocket_session()  
	{  
		// Remove this session from the list of active sessions  
		//state_->leave(this);  
		state_->leave(shared_from_this());  
	}  
```

---

## Comment 5

> Username: shivank1111  
> Created at: 2024-05-06 10:14:23 UTC  
> Url: https://github.com/boostorg/beast/issues/2845#issuecomment-2095636500  

Thanks @ashtum , will check the code thoroughly for these issues.
