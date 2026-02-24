# #1458 - creation of new client for each http async request unstable [Closed]

> Username: nj2901  
> Created at: 2019-02-17 18:52:55 UTC  
> Updated at: 2019-07-03 06:39:31 UTC  
> Closed at: 2019-05-26 04:35:56 UTC  
> Url: https://github.com/boostorg/beast/issues/1458  

I am trying to create a new Http client for each request and then delete it once response for it, is recieved.  
Reason for not having only one client is, that when multiple requests are to be sent, it gives unwanted / wierd output.   
Now with my approach i thought everything will work smoothly but it is failing as soon as any (the first) response is recieved. It doesn't wait for responses of other requests and end up with segmentation fault.  
  
if am not deleting my object, then i am getting the perfect behaviour. But this scenario will lead to memory leak as i may have to send many requests and creating those many clients is just a big concern.  
  
Now, caller can send any number of requests using http client, hence to ensure that each request recieves its response correctly i am thinking (please suggest if i am wrong) that i have to create new AsioHttpClient because earlier when i had same client some weird response was recieved. And if a new client needs to be created then as per C++ standards we should also delete it to avoid memory leaks.  
  
i am putting delete clause in my callback and errback function in jsonRequest function (Currently code is commented kindly check for reference). As soon as, first response (it can be in any order may be response for request 1 or request2 because it is async i guess) is recieved the execution fails giving segmentation fault.   
  
Please guide where am i wrong in my thinking process.  
boost: stable 1.68.0  
BOOST_BEAST_VERSION 181  
  
clang -v  
clang version 6.0.0-1ubuntu2 (tags/RELEASE_600/final)  
Target: x86_64-pc-linux-gnu  
Thread model: posix

---

## Comment 1

> Username: vinniefalco  
> Created at: 2019-02-17 18:54:07 UTC  
> Url: https://github.com/boostorg/beast/issues/1458#issuecomment-464494564  

You have not provided sufficient information in the issue, please see the guidelines for issue submission:  https://github.com/boostorg/beast/blob/develop/.github/ISSUE_TEMPLATE.md

---

## Comment 2

> Username: nj2901  
> Created at: 2019-03-14 10:06:50 UTC  
> Url: https://github.com/boostorg/beast/issues/1458#issuecomment-472784527  

Hi,   
Is it possible that someone can help me out on this ?   
Thanks.

---

## Comment 3

> Username: vinniefalco  
> Created at: 2019-03-14 12:11:23 UTC  
> Url: https://github.com/boostorg/beast/issues/1458#issuecomment-472826614  

You have to wait until the first request is finished sending before you try to write the second request.

---

## Comment 4

> Username: nj2901  
> Created at: 2019-03-14 12:17:05 UTC  
> Updated at: 2019-03-14 14:19:24 UTC  
> Url: https://github.com/boostorg/beast/issues/1458#issuecomment-472828541  

```c  
  
void JsonHTTPClient::jsonRequest(JsonRequest request,std::function<void(JsonResponse &)> jsoncallback, std::function<void(JsonResponse &)> jsonerrback, int timeout,int retries,int queryNum) {  
      
    this->queryNum = queryNum;  
    boost::asio::ssl::context ctx(boost::asio::ssl::context::sslv23);  
    ctx.set_default_verify_paths();  
          
    std::string host = request.getHost();  
    int port = request.getPort();  
    std::string target = request.getTarget();  
    std::map<std::string,std::string> headers = request.getHeaders();  
    std::function<void(int&)> errback;  
    time_t currTime = time(0);  
    AsioHttpClient *client = new AsioHttpClient(*this->context , ctx);  
      
    auto deleteCallback = [this,client] () -> void{  
        int timeout = 1;  
        boost::asio::deadline_timer *clientDeleteTimer = new boost::asio::deadline_timer( *this->context);  
          
        clientDeleteTimer->expires_from_now(boost::posix_time::seconds(timeout));  
        std::cout<<"Msg Id : Query num : " << this->queryNum << "In delete Client callback\n"<<std::flush;  
          
        clientDeleteTimer->async_wait([client,this,clientDeleteTimer](const boost::system::error_code &ec){  
        if(ec == boost::asio::error::operation_aborted){  
               
            return;  
        }  
        else{  
  
            delete client;  
        }  
        delete clientDeleteTimer;  
    });  
        std::cout<<"After timer Client\n"<<std::flush;  
    };  
      
      
    auto callback = [this,jsoncallback,timeout,currTime,client,deleteCallback](std::string &resp, int &respCode) -> void{  
  
        time_t currTimeNow = time(0);  
        if(currTimeNow - currTime > timeout){  
            std::cout<<" Reponse recieved after client times out of https request\n"<<std::flush;  
        }  
            std::cout<<" Reponse recieved within time and body is : of https request" <<  resp <<"\n"<<std::flush;  
              
            JsonResponse jsonresp;  
            jsonresp.setJsonResponse("");  
            jsonresp.setJsonResponse(resp);  
            jsonresp.setResponseCode(respCode);  
            deleteCallback();  
            jsoncallback(jsonresp);  
    };  
  
    errback = [this,request,jsoncallback,jsonerrback,timeout,retries,client,deleteCallback](int &respCode) -> void{  
              std::cout<<"inside errback of jsonget Response is : " << respCode << std::endl<<std::flush;   
                
      if(retries == 0){  
            JsonResponse jsonresp;  
            jsonresp.setJsonResponse("");  
            jsonresp.setResponseCode(respCode);  
            deleteCallback();  
            jsonerrback(jsonresp);  
      }else{  
//            retries -= 1; will give error as retries is in capture and in capture clause are variables are by default const type  
            std::cout<<"of https request Trying to reconnect retry num : " <<retries<<std::endl<<std::flush;      
            jsonRequest(request,jsoncallback,jsonerrback,timeout,retries-1,this->queryNum);  
      }  
        
    };  
      
    switch(request.getMethod()){  
        case GET:{  
            std::cout<<"Msg Id : Query num : " << this->queryNum <<"   Inside GET method of json Request\n"<<std::flush;              
            client->get(this->queryNum,timeout,host,callback,errback,headers,port,target);  
        }  
            break;  
        case POST:{  
            std::cout<<"  Inside post method of json request\n" <<std::flush;  
            std::string body = request.getJsonBody();     
            client->postascii(this->queryNum,timeout,host,body,callback,errback,headers,port,target);  
        }  
            break;  
        default: std::cout<<" Invalid request method\n" << std::flush;  
      
    }  
  
}  
```  
  
I got this point but my caller function itself waits for 1 sec before deleting the client object. And for each request a new client object is created in caller function.  
  
Appears like the system goes in deadlock . Does async http requests, even with different client for each requests use some shared resource in OS ?

---

## Comment 5

> Username: nj2901  
> Created at: 2019-03-27 10:37:03 UTC  
> Url: https://github.com/boostorg/beast/issues/1458#issuecomment-477088121  

Kindly share some insights .

---

## Comment 6

> Username: vinniefalco  
> Created at: 2019-04-19 01:37:12 UTC  
> Url: https://github.com/boostorg/beast/issues/1458#issuecomment-484740824  

I'm not really sure, this is an enormous program, and it isn't even complete (there's no declaration for `AsioHttpClient`). It is already too big for me to sit down and study. I suggest you start from one of the examples that most closely resembles what you are trying to accomplish, and verify that it works without changing it. Then, introduce small modifications to make it do what you want to do, and verify at each step that the program still behaves the way you expect.

---

## Comment 7

> Username: stale[bot]  
> Created at: 2019-05-19 03:42:15 UTC  
> Url: https://github.com/boostorg/beast/issues/1458#issuecomment-493724326  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 8

> Username: stale[bot]  
> Created at: 2019-05-26 04:35:55 UTC  
> Url: https://github.com/boostorg/beast/issues/1458#issuecomment-495968184  

It looks like this issue has either been abandoned or resolved so I will go ahead and close it. Feel free to re-open this issue if you feel it needs attention, or open new issues as needed. Thanks!
