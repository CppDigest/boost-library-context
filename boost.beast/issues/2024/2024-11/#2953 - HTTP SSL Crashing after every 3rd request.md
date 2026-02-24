# #2953 - HTTP SSL Crashing after every 3rd request [Closed]

> Username: shivank1111  
> Created at: 2024-11-19 12:47:56 UTC  
> Updated at: 2024-11-28 12:10:44 UTC  
> Closed at: 2024-11-28 12:10:43 UTC  
> Url: https://github.com/boostorg/beast/issues/2953  

Boost-Version = 1.83  
  
  
I am using advanced-server-flex example(https://github.com/boostorg/beast/blob/develop/example/advanced/server-flex/advanced_server_flex.cpp) and in that only I have added my changes but the issue is that I am facing crash whenever I am using SSL server but it is fine when I am using plain connection(without ssl).  
  
It is going to the below function and giving this output:  
  
terminate called after throwing an instance of 'boost::wrapexcept<boost::asio::execution::bad_executor>'  
  what():  bad executor  
make: *** [Makefile:26: run] Aborted (core dumped)  
  
  
It is coming inside the if(! response_queue_.empty()) condition and after every 3rd request it is giving the bad_executor error.  
  
Kindly help me with the same.   
  
NOTE: I have made some changes in the constructor parameters but I believe that won't have create a issue. Do let me know if any extra information is required.   
  
The below function is same as advanced_server_flex example:  
  
```  
void do_write()  
    {  
        try  
		{  
			std::cout<<"http_session do_write"<<"\n";  
			if(! response_queue_.empty())  
			{  
				std::cout<<"TEST CRASH 1.0.1"<<"\n";  
				bool keep_alive = response_queue_.front().keep_alive();  
				std::cout<<"keep alive value:"<<keep_alive<<"\n";  
				//Below line is crashing  
				beast::async_write( derived().stream(), std::move(response_queue_.front()),   
					beast::bind_front_handler( &http_session::on_write,	derived().shared_from_this(), keep_alive));  
				std::cout<<"TEST CRASH 1.0.2"<<"\n";  
			}  
		}  
		catch (const std::exception& e)   
		{  
			log_error("Exception in http_session:: do_write: %s", e.what());  
		}  
    }

---

## Comment 1

> Username: ashtum  
> Created at: 2024-11-20 08:12:17 UTC  
> Url: https://github.com/boostorg/beast/issues/2953#issuecomment-2487838023  

I can't reproduce this. Could you please test if you can reproduce it using the original code in the example?

---

## Comment 2

> Username: shivank1111  
> Created at: 2024-11-21 06:11:36 UTC  
> Url: https://github.com/boostorg/beast/issues/2953#issuecomment-2490158589  

I tried in the original code, it is not crashing but in my code it is crashing.  
I am trying to add one by one my changes in the code, and check where exactly the issue is there. Give me 1-2 days and if required I will ask for help. Till then please don't close this issue.  
  
Thanks for the help!

---

## Comment 3

> Username: vinniefalco  
> Created at: 2024-11-22 05:25:56 UTC  
> Url: https://github.com/boostorg/beast/issues/2953#issuecomment-2492901822  

An obvious solution presents itself, skip every 3rd request.

---

## Comment 4

> Username: shivank1111  
> Created at: 2024-11-28 05:48:31 UTC  
> Url: https://github.com/boostorg/beast/issues/2953#issuecomment-2505309584  

I tried using the suggested solution above, but the issue persisted, and the application continued to crash. However, I identified a timeout-related statement, made changes to it, and tested with approximately 1000 connections without encountering any crashes.   
  
In the function `void do_read()`, I modified the timeout statement to never expire. Previously, it was sending a timeout close signal, and if any request was sent during that period, it would cause the crash.  
  
  
Changed the below statement  
----------------------------------  
// Set the timeout.  
//beast::get_lowest_layer( derived().stream()).expires_after(std::chrono::seconds(45));  
  
Changed it to  
---------------  
//Added for crash related issue  
beast::get_lowest_layer(derived().stream()).expires_never();  
  
  
In the advanced_server_flex.cpp code, the application crashes exactly after the duration specified in the expires_after parameter.  
  
Please check from your side as well, and if you encounter the same issue, kindly let me know. As of now, my crash issue is resolved, but I want to ensure that my solution and approach are correct.   
  
Thank you!

---

## Comment 5

> Username: ashtum  
> Created at: 2024-11-28 06:05:35 UTC  
> Url: https://github.com/boostorg/beast/issues/2953#issuecomment-2505327279  

You mentioned you're using Boost version 1.83; is that correct? Your situation seems similar to this bug report, which was introduced in version 1.86: https://github.com/boostorg/beast/issues/2925. This issue has been fixed and will be included in the upcoming Boost 1.87 release.

---

## Comment 6

> Username: shivank1111  
> Created at: 2024-11-28 06:12:03 UTC  
> Url: https://github.com/boostorg/beast/issues/2953#issuecomment-2505334204  

Yes I am using Boost version 1.83.   
  
I am also attaching the code that I used, I just added one function:  
Just change the extension to .cpp. #/[advanced_server_flex_github.txt](https://github.com/user-attachments/files/17943474/advanced_server_flex_github.txt)  
  
  
Thanks for the help @ashtum.

---

## Comment 7

> Username: ashtum  
> Created at: 2024-11-28 07:26:59 UTC  
> Updated at: 2024-11-28 07:27:32 UTC  
> Url: https://github.com/boostorg/beast/issues/2953#issuecomment-2505425247  

I couldn't reproduce the issue with the provided code. Could you add the following line to your `main` function and confirm the library version?    
```C++  
std::cout << BOOST_BEAST_VERSION << std::endl;  
// prints 347 for boost 1.83.0  
```  
You might have two versions of Boost installed on your machine. To confirm, Please run the compiled executable to check the version. Inspecting `BOOST_BEAST_VERSION` in the IDE might give a different result.

---

## Comment 8

> Username: shivank1111  
> Created at: 2024-11-28 11:10:49 UTC  
> Updated at: 2024-11-28 11:13:03 UTC  
> Url: https://github.com/boostorg/beast/issues/2953#issuecomment-2505861996  

Sorry I had 2 Boost beast downloaded.  
  
It's showing "354" so which version does that imply?

---

## Comment 9

> Username: ashtum  
> Created at: 2024-11-28 11:33:27 UTC  
> Url: https://github.com/boostorg/beast/issues/2953#issuecomment-2505905191  

> Sorry I had 2 Boost beast downloaded.  
>   
> It's showing "354" so which version does that imply?  
  
You are experiencing the issue described here: https://github.com/boostorg/beast/issues/2925.    
I recommend using version [1.87.0.beta](https://www.boost.io/releases/1.87.0.beta1/) for now and upgrading to 1.87.0 when it is released in December.

---

## Comment 10

> Username: shivank1111  
> Created at: 2024-11-28 12:10:43 UTC  
> Url: https://github.com/boostorg/beast/issues/2953#issuecomment-2505973738  

Hi @ashtum,   
  
Now I will use expires_never in the timeout because for the time being it is solving my issue, and when released in december will update to boost_beast 1.87 version.   
  
I needed help regarding one more thing, related to ping pong so will raise another git issue.   
  
Thanks for the help.
