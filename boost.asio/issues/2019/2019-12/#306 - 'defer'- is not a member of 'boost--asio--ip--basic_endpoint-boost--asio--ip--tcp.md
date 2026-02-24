# #306 - 'defer': is not a member of 'boost::asio::ip::basic_endpoint<boost::asio::ip::tcp> [Closed]

> Username: vtharmalingam  
> Created at: 2019-12-10 18:09:07 UTC  
> Updated at: 2020-08-23 08:13:25 UTC  
> Closed at: 2019-12-10 18:27:34 UTC  
> Url: https://github.com/boostorg/asio/issues/306  

Hi,  
  
I am not knowledgeable enough to identify where this error is originally rooting from from my code. But this is has made me crazy and I have spent 2-3 days just on this - install/install boost multiple times. All this happened only after i upgraded to boost - 1.71.0. I am using beast and wanted to try out Websocket SSL and that's where i landed in this.   
  
What help I needed is to identify the piece of code that I use that goes down to "executor" and leads to this specific error.  
  
```  
Error	C2039	'defer': is not a member of 'boost::asio::ip::basic_endpoint<boost::asio::ip::tcp>'		C:\vcpkg\installed\x86-windows\include\boost\asio\impl\executor.hpp	194	  
```  
  
Any quick help on this will be greatly appreciated...  
  
Thanks,  
Tharma

---

## Comment 1

> Username: vtharmalingam  
> Created at: 2019-12-10 18:27:34 UTC  
> Url: https://github.com/boostorg/asio/issues/306#issuecomment-564168286  

I made a stupid mistake, sorry

---

## Comment 2

> Username: vtharmalingam  
> Created at: 2019-12-10 18:27:45 UTC  
> Url: https://github.com/boostorg/asio/issues/306#issuecomment-564168371  

closing

---

## Comment 3

> Username: foragerDev  
> Created at: 2020-08-23 08:13:25 UTC  
> Url: https://github.com/boostorg/asio/issues/306#issuecomment-678744289  

@vtharmalingam what is that mistake can you please let me know?
