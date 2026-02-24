# #712 - Async http/web client examples [Closed]

> Username: ghost  
> Created at: 2017-08-02 12:54:20 UTC  
> Updated at: 2017-08-16 10:30:37 UTC  
> Closed at: 2017-08-07 06:17:37 UTC  
> Url: https://github.com/boostorg/beast/issues/712  

Hi, I have used the blocking resolve, read, write etc. functions so far, but need to switch to async communication now. I have tried to figure out the async usage on my own ... but no chance. I've never seen such unintuitive code as of boost before. So could you please provide an async http client example or at least give some hints to get me in the right direction. I think I need to call the corresponding async_resolve, async_read, async_write functions instead, but have no idea how to use them together with beast.  
An async client websocket example would be nice too :).

---

## Comment 1

> Username: vinniefalco  
> Created at: 2017-08-02 13:19:44 UTC  
> Url: https://github.com/boostorg/beast/issues/712#issuecomment-319669562  

You could simply adapt the asynchronous websocket server. Call `async_resolve` and use `std::bind` to define your completion handler as in the server example.

---

## Comment 2

> Username: ghost  
> Created at: 2017-08-02 14:58:46 UTC  
> Url: https://github.com/boostorg/beast/issues/712#issuecomment-319698853  

Yes, I tried this, but without success. And the server example seems not to use the async_resolve function at all.  
  
The synchronous call was:  
  
> tcp::resolver::iterator itLookup = resolver.resolve(query, errorCode);  
  
So i tried the asynchronous:  
  
-----------------------------------------------------------------------------------------------  
  
> void OnResolveHandler(const boost::system::error_code& error, tcp::resolver::iterator iterator)  
> {  
> }  
> resolver.async_resolve(query, OnResolveHandler);  
  
Error	C3867	'OnResolveHandler': non-standard syntax; use '&' to create a pointer to member  
Error	C2672	'boost::asio::ip::basic_resolver<boost::asio::ip::tcp,boost::asio::ip::resolver_service<InternetProtocol>>::async_resolve': no matching overloaded function found  
  
-----------------------------------------------------------------------------------------------  
  
> void OnResolveHandler(const boost::system::error_code& error, tcp::resolver::iterator iterator)  
> {  
> }  
> resolver.async_resolve(query, &OnResolveHandler);  
  
Severity	Code	Description	Project	File	Line	Suppression State  
Error	C2780	'::boost::asio::async_result<::boost::asio::handler_type<ResolveHandler,void(boost::system::error_code,boost::asio::ip::basic_resolver_iterator<InternetProtocol>)>::type>::type boost::asio::ip::basic_resolver<InternetProtocol,boost::asio::ip::resolver_service<InternetProtocol>>::async_resolve(const boost::asio::ip::basic_endpoint<InternetProtocol> &,ResolveHandler &&)': expects 2 arguments - 1 provided  
Error	C2276	'&': illegal operation on bound member function expression  
Error	C2672	'boost::asio::ip::basic_resolver<boost::asio::ip::tcp,boost::asio::ip::resolver_service<InternetProtocol>>::async_resolve': no matching overloaded function found  
Error	C2780	'::boost::asio::async_result<::boost::asio::handler_type<ResolveHandler,void(boost::system::error_code,boost::asio::ip::basic_resolver_iterator<InternetProtocol>)>::type>::type boost::asio::ip::basic_resolver<InternetProtocol,boost::asio::ip::resolver_service<InternetProtocol>>::async_resolve(const boost::asio::ip::basic_resolver_query<InternetProtocol> &,ResolveHandler &&)': expects 2 arguments - 1 provided  
  
-----------------------------------------------------------------------------------------------  
  
> void OnResolveHandler(const boost::system::error_code& error, tcp::resolver::iterator iterator)  
> {  
> }  
> resolver.async_resolve(query, std::bind(&OnResolveHandler, this, std::placeholders::_1));  
  
Error	C2672	'boost::asio::ip::basic_resolver<boost::asio::ip::tcp,boost::asio::ip::resolver_service<InternetProtocol>>::async_resolve': no matching overloaded function found  
Error	C2276	'&': illegal operation on bound member function expression  
Error	C2672	'std::bind': no matching overloaded function found  
Error	C2780	'::boost::asio::async_result<::boost::asio::handler_type<ResolveHandler,void(boost::system::error_code,boost::asio::ip::basic_resolver_iterator<InternetProtocol>)>::type>::type boost::asio::ip::basic_resolver<InternetProtocol,boost::asio::ip::resolver_service<InternetProtocol>>::async_resolve(const boost::asio::ip::basic_endpoint<InternetProtocol> &,ResolveHandler &&)': expects 2 arguments - 1 provided  
Error	C2780	'::boost::asio::async_result<::boost::asio::handler_type<ResolveHandler,void(boost::system::error_code,boost::asio::ip::basic_resolver_iterator<InternetProtocol>)>::type>::type boost::asio::ip::basic_resolver<InternetProtocol,boost::asio::ip::resolver_service<InternetProtocol>>::async_resolve(const boost::asio::ip::basic_resolver_query<InternetProtocol> &,ResolveHandler &&)': expects 2 arguments - 1 provided  
  
-----------------------------------------------------------------------------------------------  
  
Sorry to say that, but the whole boost lib looks totally ill desined for me. It's desinged from template fetishists. Why the hell is the resolver class part of the tcp class and not staying for itself. Nothing could be forward declared, dozens of error messages reaching over multiple lines full of template rubbish. Use of auto together with implizit conversions so that no one can ever say again what data type is used under the hood. You see I am really annoyed of boost. Sadly it seems C++ becomes the new Visual Basic. History repeats... All I want is something like Connect, Disconnect, Send and Receive. Why must this be so complicated? I am no web developer and without some examples it is not clear to me how it should be used.

---

## Comment 3

> Username: vinniefalco  
> Created at: 2017-08-02 15:27:04 UTC  
> Updated at: 2017-08-02 15:29:47 UTC  
> Url: https://github.com/boostorg/beast/issues/712#issuecomment-319707324  

```  
resolver.async_resolve(query, OnResolveHandler);  
```  
This is not correct C++ syntax, you need to take the address of your function pointer, or use `std::bind` as in your later attempts.  
  
```  
resolver.async_resolve(query, std::bind(&OnResolveHandler, this, std::placeholders::_1));  
```  
The resolve handler takes two arguments not one. Try:  
```  
resolver.async_resolve(query, std::bind(&OnResolveHandler,  
    this, std::placeholders::_1, std::placeholders::_2));  
```  
  
Note that this requirement is documented:  
http://www.boost.org/doc/libs/1_64_0/doc/html/boost_asio/reference/ResolveHandler.html  
  
  
>the whole boost lib looks totally ill desined for me. It's desinged from template fetishists.  
  
Most sophisticated libraries, including Boost, presume that the reader has a firm grasp of the C++ language. I don't think this is necessarily a problem with the library. If you want to get better results, consider brushing up on your C++ language skills. There are many free tutorials and answers on StackOverflow.com that should be able to help you. As a last resort feel free to post your questions here.  
  
Good luck!

---

## Comment 4

> Username: ghost  
> Created at: 2017-08-07 06:17:37 UTC  
> Url: https://github.com/boostorg/beast/issues/712#issuecomment-320578131  

As I told you I tried several combinations with the bind function but nothing succeded. Also with two params as suggested from you. At the end the bind function could not handle the first argument correctly. Instead of   
  
> resolver.async_resolve(query, std::bind(&OnResolveHandler, this, std::placeholders::_1, std::placeholders::_2));  
  
I had to write  
  
> resolver.async_resolve(query, std::bind(&MyClass::OnResolveHandler, this, std::placeholders::_1, std::placeholders::_2));  
  
I tried to use beast based on this example  
  
http://www.boost.org/doc/libs/1_36_0/doc/html/boost_asio/example/http/client/async_client.cpp  
  
but still couldn't figure out how to use it. So I decided against using beast for now doing the parsing/protocol stuff with boost.asio only. Maybe you will provide some examples in future for async usage. At least I don't get it.

---

## Comment 5

> Username: vinniefalco  
> Created at: 2017-08-07 06:46:47 UTC  
> Url: https://github.com/boostorg/beast/issues/712#issuecomment-320582492  

I'll have **many** new examples available in just a day or two if you can hang on.

---

## Comment 6

> Username: ghost  
> Created at: 2017-08-16 10:30:37 UTC  
> Url: https://github.com/boostorg/beast/issues/712#issuecomment-322730264  

Thanks for the examples. That helped me a lot :+1:.
