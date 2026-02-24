# #1887 - Benchmarks/test client server architecture using boost.asio / beast webocket [Closed]

> Username: denisbertini  
> Created at: 2020-03-30 09:07:46 UTC  
> Updated at: 2020-05-04 11:16:13 UTC  
> Closed at: 2020-05-04 11:16:13 UTC  
> Url: https://github.com/boostorg/beast/issues/1887  

Hi,   
  
I would like to test/benchmark a client/server program based on boost/asio + beast websocket  library.   
One server and multiple client running on a Linux based cluster using Infiniband ( IPovIB to be precise ) as interconnecting  network.  
The problem we are facing is that we have low clients efficiency, typically for 400 clients we have ~ 40% CPU usage, client working on one cpu only, and we are at this point not able to saturate the CPU usage for the client.  
  
Some Details:  
  
- The server fires up one or more  listener thread per core. By increasing the number of listener thread, one notice  a slight increase of client CPU usage up to 50 % but still far from stauration  
  
- Requests are received, parsed, processed, and responses are written out  
  
So, the question i have is what should I look at to improve this result?   
  
My idea was to use some test/benchmark program available from the beast github repository,  
like for example wsload to do some profiling and investigate some crucial aspects  in order to better understand some of the bottlenecks i.e:  
  
- If the requests are too small, and often fulfilled in a few ms, one could modify the client to actually send  not so often and much bigger requests ?  
- modify server to use a different design pattern, is this appropriate here?  
  
So my question : is the wsload example ii found in the repository /bench suitable for such a study?  
If yes, since wsload need a server to run, is there a already written server part that i can used together with wsload example for this kind of study ?  
If not what can i use then ?  
  
Thanks in advance.

---

## Comment 1

> Username: madmongo1  
> Created at: 2020-03-30 09:24:52 UTC  
> Url: https://github.com/boostorg/beast/issues/1887#issuecomment-605886062  

Hi @denisbertini ,  
  
I'd like to help you as I have seen extremely high throughput from a beast server in a previous project.  
  
Are you able to share the benchmark programs you already have? Perhaps I can take a look and see if there is any room for improvement in approach.

---

## Comment 2

> Username: denisbertini  
> Created at: 2020-03-30 09:30:58 UTC  
> Url: https://github.com/boostorg/beast/issues/1887#issuecomment-605889200  

Well i am not the author of the client/server program i want to test, but the code  it is available on github at:  
https://github.com/gemfony/geneva.  
I first wanted to use plain beast-program to better understand the situation we have, that is why i wanted to use for example wsload benchmark.  
Do you have an example of such high throughput beast server  that i can use ?

---

## Comment 3

> Username: madmongo1  
> Created at: 2020-03-30 09:45:22 UTC  
> Url: https://github.com/boostorg/beast/issues/1887#issuecomment-605896935  

I can't think of one that is general purpose. Would you like to join the slack channel to discuss further?  
  
https://cppalliance.org/slack/  
  
channel: `#beast`

---

## Comment 4

> Username: denisbertini  
> Created at: 2020-03-30 09:52:30 UTC  
> Url: https://github.com/boostorg/beast/issues/1887#issuecomment-605900836  

Well i"d rather keep using the issues here.  
I would like  investigate further the problem in order to give more hints for suport.  
Thanks

---

## Comment 5

> Username: vinniefalco  
> Created at: 2020-03-30 15:12:04 UTC  
> Url: https://github.com/boostorg/beast/issues/1887#issuecomment-606059674  

wsload is a tool I used to help me load up a websocket server so that I could try out different optimizations. It probably won't help you, because you care more about the specifics of how you handle requests.  
  
In any event, in order to know if you have unnecessary bottlenecks, we need to know some things, Are you using one `io_context` per thread? Or one `io_context` with many threads? Or just one thread? Are you using strands? Are you using asynchronous APIs?

---

## Comment 6

> Username: denisbertini  
> Created at: 2020-03-31 05:33:55 UTC  
> Url: https://github.com/boostorg/beast/issues/1887#issuecomment-606409952  

-  Asynchronous API is used  
- Multiple listener threads can be used, one io_context per threads ( io_context array)  , it actually push up a little the client CPU usage, but not enough tough ...   
- Here a Link to consumer websocket code if you could give expert comments:  
  
https://github.com/gemfony/geneva/blob/master/include/courtier/GWebsocketConsumerT.hpp

---

## Comment 7

> Username: madmongo1  
> Created at: 2020-04-09 10:27:05 UTC  
> Url: https://github.com/boostorg/beast/issues/1887#issuecomment-611454098  

You asked for comments:  
  
1. Possible performance hit here if strings are long. Is it possible to deserialise the object from   
text in the buffer?  
  
```  
		 // Extract the string from the buffer  
		 auto message = boost::beast::buffers_to_string(m_incoming_buffer.data());  
  
		 // Clear the buffer, so we may later fill it with data to be sent  
		 m_incoming_buffer.consume(m_incoming_buffer.size());  
  
		 // De-serialize the object  
		 Gem::Courtier::container_from_string(  
			 message  
			 , m_command_container  
			 , m_serialization_mode  
		 ); // may throw  
```  
  
2. This smells bad. You're mixing async with sync code. If you call async_close() on the websocket, all subsequent reads and writes will fail with `asio::error::operation_aborted`.  
  
You can use this to clean up and automatically drop out of the io_context run loop.  
  
```  
		 // This call will block until no more work remains in the ASIO work queue  
		 m_io_context.run();  
  
		 // Finally close all outstanding connections  
		 do_close(m_close_code);  
```  
  
3. ...Which would make this un-necessary:  
  
```  
	 void async_start_write(const std::string& message) {  
		 // Do nothing if we have been asked to stop  
		 if(this->halt()) return;  
```

---

## Comment 8

> Username: vinniefalco  
> Created at: 2020-04-26 16:25:08 UTC  
> Url: https://github.com/boostorg/beast/issues/1887#issuecomment-619579696  

Is this resolved?

---

## Comment 9

> Username: denisbertini  
> Created at: 2020-04-27 08:12:26 UTC  
> Url: https://github.com/boostorg/beast/issues/1887#issuecomment-619811739  

First of al : @madmongo1 l thanks a lot for scanning the code and giving interessant comments !  
I will try this out and tell you if i get some boost ....   
Thanks again and sorry for late answer.

---

## Comment 10

> Username: madmongo1  
> Created at: 2020-05-04 11:16:13 UTC  
> Url: https://github.com/boostorg/beast/issues/1887#issuecomment-623403762  

I'll close this issue. If you have any more questions about Beast, please don't hesitate to either file an issue or join us on the #beast channel in the Cpplang slack workspace:  
  
https://cppalliance.org/#links
