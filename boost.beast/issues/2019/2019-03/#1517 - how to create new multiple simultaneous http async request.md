# #1517 - how to create new multiple simultaneous http async request [Closed]

> Username: nj2901  
> Created at: 2019-03-14 10:08:44 UTC  
> Updated at: 2019-05-20 14:37:01 UTC  
> Closed at: 2019-05-20 14:37:01 UTC  
> Url: https://github.com/boostorg/beast/issues/1517  

In my code, I am using boost library which gets asynchronous messages. It is a single threaded system.   
My use case is , i want to create a new http async request for each message and if response is recieved then want to do some processing with the response.  
  
Fun fact here is : that when i am testing my module by sending simultaneous messages say 50 messages with a gap of 300 milliseconds then my code pauses after some requests' responses are recieved. Pause here means it doen't crash no error message is shown but the screen goes idle and no output is shown.   
No further requests are received by the code though they are being sent by my test_driver code. It appears as if its gone in infinite loop.  
  
Please help me with this abnormal behaviour of http async client.  
  
boost: stable 1.68.0  
BOOST_BEAST_VERSION 181  
  
clang -v  
clang version 6.0.0-1ubuntu2 (tags/RELEASE_600/final)  
Target: x86_64-pc-linux-gnu  
Thread model: posix  
  
Thanks in advance.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2019-03-14 12:09:44 UTC  
> Url: https://github.com/boostorg/beast/issues/1517#issuecomment-472826035  

Can you please post a small test program that reproduces the problem?

---

## Comment 2

> Username: nj2901  
> Created at: 2019-03-14 12:52:38 UTC  
> Url: https://github.com/boostorg/beast/issues/1517#issuecomment-472839695  

```c  
void sendHttpRequest(){  
    HttpClient *client = new HttpClient();  
  
    deleteClient = [this,client]{  
            int timeout = 1;  
        boost::asio::deadline_timer *clientDeleteTimer = new boost::asio::deadline_timer( *this->context);  
          
        clientDeleteTimer->expires_from_now(boost::posix_time::seconds(timeout));  
        clientDeleteTimer->async_wait([client,this,clientDeleteTimer](const boost::system::error_code &ec){  
        if(ec == boost::asio::error::operation_aborted){  
             std::cout<<" Operation aborted\n"<<std::flush;             
            return;  
        }  
        else{  
            delete client;  
        }  
        delete clientDeleteTimer;  
   };  
  
   callback = [] {  
            std::cout<<"Response recieved successfully\n"<<std::flush;  
            deleteClient();  
   };  
  
  errback = [] {  
           std::cout<<"Response not recieved \n"<<std::flush;  
            deleteClient();  
   };  
   client.sendPostRequest(request, callback , errback);  
}  
```

---

## Comment 3

> Username: stale[bot]  
> Created at: 2019-04-13 13:44:19 UTC  
> Url: https://github.com/boostorg/beast/issues/1517#issuecomment-482810495  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 4

> Username: vinniefalco  
> Created at: 2019-04-13 13:48:33 UTC  
> Url: https://github.com/boostorg/beast/issues/1517#issuecomment-482810822  

I don't understand the question

---

## Comment 5

> Username: stale[bot]  
> Created at: 2019-05-13 14:08:05 UTC  
> Url: https://github.com/boostorg/beast/issues/1517#issuecomment-491837662  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 6

> Username: stale[bot]  
> Created at: 2019-05-20 14:37:00 UTC  
> Url: https://github.com/boostorg/beast/issues/1517#issuecomment-494014521  

It looks like this issue has either been abandoned or resolved so I will go ahead and close it. Feel free to re-open this issue if you feel it needs attention, or open new issues as needed. Thanks!
