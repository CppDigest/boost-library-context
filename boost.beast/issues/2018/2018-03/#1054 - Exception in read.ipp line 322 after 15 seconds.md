# #1054 - Exception in read.ipp line 322 after 15 seconds [Closed]

> Username: solosTec  
> Created at: 2018-03-02 01:40:15 UTC  
> Updated at: 2018-03-02 10:22:44 UTC  
> Closed at: 2018-03-02 03:23:15 UTC  
> Url: https://github.com/boostorg/beast/issues/1054  

### Version  
  
Version 162  
  
### Steps necessary to reproduce the problem  
  
When running example [advanced_server.cpp](https://github.com/boostorg/beast/blob/develop/example/advanced/server/advanced_server.cpp) I got an exception in read.ipp line 322 after 15 seconds.  
  
It seems the websocket is invalid when the on_control_callback() is called. I made a small modification:  
  
  
 ```  
       ws_.control_callback(  
            std::bind(  
                &websocket_session::on_control_callback,  
//             this,  
                this->shared_from_this(),  
                std::placeholders::_1,  
                std::placeholders::_2));   
```  
  
But behavior doesn't change.  
  
### All relevant compiler information  
  
- Compiled with newest Visual Studio Version.  
- Run on Windows 10

---

## Comment 1

> Username: carolinebeltran  
> Created at: 2018-03-02 02:30:45 UTC  
> Url: https://github.com/boostorg/beast/issues/1054#issuecomment-369800802  

Hello, I do believe that you will find the solution here:  
https://github.com/boostorg/beast/issues/1047

---

## Comment 2

> Username: vinniefalco  
> Created at: 2018-03-02 02:39:53 UTC  
> Url: https://github.com/boostorg/beast/issues/1054#issuecomment-369802252  

Are you using Beast from the repository or from Boost 1.66.0?

---

## Comment 3

> Username: solosTec  
> Created at: 2018-03-02 03:15:21 UTC  
> Url: https://github.com/boostorg/beast/issues/1054#issuecomment-369807555  

The Boost version is 1.66.0. The example [advanced_server.cpp](https://github.com/boostorg/beast/blob/develop/example/advanced/server/advanced_server.cpp) is taken from the github repository.  
  
I've found a workaround by using a new member variable in the websocket class of type:  
  
`std::function<void(boost::beast::websocket::frame_type, boost::beast::string_view)> cb_;  
`  
  
It is used in the following way:  
  
```  
cb_ = std::bind(&websocket_session::on_control_callback,  
	//this->shared_from_this(),  
	this,  
	std::placeholders::_1,  
	std::placeholders::_2);  
  
	ws_.control_callback(cb_);  
```  
  
This works fine for me. The idea came up after reading your comment in stream.cpp:  
  
>         // Callback may not be constant, caller is responsible for  
>         // managing the lifetime of the callback. Copies are not made.  
  
So I have advanced the lifetime of the callback. But I'm not sure I'm on the right track.

---

## Comment 4

> Username: vinniefalco  
> Created at: 2018-03-02 03:19:35 UTC  
> Url: https://github.com/boostorg/beast/issues/1054#issuecomment-369808128  

You are definitely on the right track :) In Boost 1.66.0, the implementation merely stored a reference to the control callback. But in Boost 1.67.0 (what the repository will become in a few weeks) the implementation makes a copy of the callback by storing it in a `std::function`. My original intent by storing a reference was to avoid allocating memory, but the user can still avoid memory allocation in the version that makes a copy of the callback simply by putting it in a `std::reference_wrapper`. Thus, I changed the interface. The advanced_server.cpp source file was also changed to reflect this. But using the example source file from the repository with Boost 1.66.0 will of course result in undefined behavior since the lifetime of the callback is too short, as you have discovered.

---

## Comment 5

> Username: solosTec  
> Created at: 2018-03-02 03:23:15 UTC  
> Url: https://github.com/boostorg/beast/issues/1054#issuecomment-369808626  

Okay, I was too early. So I learned it the hard way. Thanx for helping!

---

## Comment 6

> Username: zlojvavan  
> Created at: 2018-03-02 06:25:38 UTC  
> Url: https://github.com/boostorg/beast/issues/1054#issuecomment-369833717  

did similar modifications few months ago:  
  
template<class Derived>  
class websocket_session: public websocket_session_base  
{  
...  
	typedef std::function<void(boost::beast::websocket::frame_type, string_view)> CtrlCbkType;  
	CtrlCbkType ctrlcbk;  
...  
  
	template<class Body, class Allocator>  
	void  
		do_accept(http::request<Body, http::basic_fields<Allocator>> req)  
	{  
		// Set the control callback. This will be called  
		// on every incoming ping, pong, and close frame.  
		derived().ws().control_callback(ctrlcbk  
			/*std::bind(  
				&websocket_session::on_control_callback,  
				this,//derived().shared_from_this(),  
				std::placeholders::_1,  
				std::placeholders::_2)*/);

---

## Comment 7

> Username: solosTec  
> Created at: 2018-03-02 10:22:44 UTC  
> Url: https://github.com/boostorg/beast/issues/1054#issuecomment-369883081  

Good for you. I wasn't aware of this.   
Does it make sense to cancel the session timer, when upgrading to websocket? I haven't quite understood the lifetime of session and websocket objects.
