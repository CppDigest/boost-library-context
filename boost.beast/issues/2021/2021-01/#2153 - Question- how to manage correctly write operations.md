# #2153 - Question: how to manage correctly write operations [Closed]

> Username: emabiz  
> Created at: 2021-01-27 08:43:11 UTC  
> Updated at: 2022-03-22 09:40:44 UTC  
> Closed at: 2022-03-18 07:01:01 UTC  
> Url: https://github.com/boostorg/beast/issues/2153  

Hi,  
I need to understand if I have to manage the transmission queue externally or the sending rate is managed automatically by beast/asio in a multithreaded environment.  
  
Let's say I have a wss client:  
  
```  
boost::beast::websocket::stream<boost::asio::ssl::stream<boost::asio::ip::tcp::socket>> _ws(boost::asio::make_strand(ioc), ctx)  
```  
  
I am using a strand so all async operations on the websocket should be managed correctly internally.  
  
Now I want to send data, but in my code it is possible that multiple threads want to transmit data concurrently on the same websocket.   
  
If I have to manage an external queue, the code for sending data could be something like that:  
  
```  
void send(shared_io_buffer buffer)  
{		  
    auto self = shared_from_this();  
    _tx_strand.post([self, this, buffer]() {//here I am using an external strand for the management of the queue  
	    _tx_queue.push(buffer);  
										        
	    if (_tx_queue.size() == 1) {  
		    next_write(buffer);  
	    }  
    });  
}  
  
void next_write(shared_io_buffer buffer)  
{  
    auto self = shared_from_this();  
    _ws.async_write(boost::asio::buffer(buffer->data(), buffer->size()),[this, self](boost::system::error_code ec, std::size_t bytes_transferred) {  
	    if (ec) {  
			triggerSessionDestroy(ec, "write");  
			return;  
		}  
		  
	    _tx_strand.post([self, this](){  
		    _tx_queue.queue.pop();  
  
			if (!_tx_queue.queue.empty()) {  
				shared_io_buffer buffer = _tx_queue.queue.front();  
				next_write(buffer);  
			}  
		});  
    });  
}  
```  
In this way, if the send function is called multiple times, buffers are accumulated externally in the tx queue and the next async_write is called only at the end of the previous one.  
  
Another option could be the following.  
If I don't have to manage the sending queue the code could be simply:  
  
```  
void send(shared_io_buffer buffer)  
{		  
    auto self = shared_from_this();  
    _ws.async_write(boost::asio::buffer(buffer->data(), buffer->size()),[this, self](boost::system::error_code ec, std::size_t bytes_transferred) {  
	    if (ec) {  
			triggerSessionDestroy(ec, "write");  
			return;  
		}			     
    });  
}  
```  
  
But in this second case, what happen if the sending rate is bigger that the network capacity?  
The async_write handlers should start to be accumulated internally and managed at the rate of the network.  
  
And what happen to the accumulated handlers, if I decide to close the websocket in this situation?  
  
Are all the async_write handlers after the close triggered with an error or skipped?  
  
Based on the previous answers, do I have a similar behavior on basic asio sockets or it is specific of beast websockets?  
  
Which is the correct choice?  
  
Thank you in advance,  
  
Emanuele

---

## Comment 1

> Username: madmongo1  
> Created at: 2021-01-27 10:35:33 UTC  
> Url: https://github.com/boostorg/beast/issues/2153#issuecomment-768193832  

> If I have to manage an external queue, ...  
  
Yes you are correct, you must queue the writes so that they only happen one at a time.  
  
> in [any] case, what happen if the sending rate is bigger that the network capacity?  
  
If the sending rate is bigger than network capacity, your async_write operation will stay in progress until sufficient tcp send buffer space has been made available by the OS.  
  
You will experience this as a longer delay between async_write and the execution of the completion handler.  
This will mean that your queue (see above) might drain more slowly than it is filling.  
  
If you were to add (for example) timestamps to the items in your send queue, you would be able to measure latency.  
  
In reality, a queue size of more than a few items will be unlikely unless you have an extremely congested network.

---

## Comment 2

> Username: emabiz  
> Created at: 2021-01-27 10:44:44 UTC  
> Updated at: 2021-01-27 10:50:09 UTC  
> Url: https://github.com/boostorg/beast/issues/2153#issuecomment-768198784  

Hi,  
thank for your response.  
  
Then what is the purpose of the internal strand I am creating on the socket?  
```  
boost::beast::websocket::stream<boost::asio::ssl::stream<boost::asio::ip::tcp::socket>> _ws(boost::asio::make_strand(ioc), ctx)  
```  
The only reason I see could be to avoid concurrently writes and reads.  
Should be concurrently writes and reads avoided?  
Or are there other reasons?

---

## Comment 3

> Username: madmongo1  
> Created at: 2021-01-27 10:51:47 UTC  
> Url: https://github.com/boostorg/beast/issues/2153#issuecomment-768202447  

The strand in the socket is the executor that will be used to execute any completion handler you supply to an async_ initiation function if that handler does not have a different executor associated with it.  
  
In the case where your io_context is being run by more than one thread, it will ensure that completion handlers will execute on the strand in sequence, and not in parallel. So it protects your connection object in that respect.  
  
It protects you from simultaneous completions (for example if the internal websocket timer times out while receiving data, you want these events to be processed sequentually)  
  
Much like a mutex, you only get protection from races if you actually lock the mutex before accessing data. Similarly, you must ensure that the strand is used, by post() or dispatch() if you want protection from calling code that is potentially running in a different strand.

---

## Comment 4

> Username: madmongo1  
> Created at: 2021-01-27 10:52:04 UTC  
> Url: https://github.com/boostorg/beast/issues/2153#issuecomment-768202592  

I have a couple of example programs here:  
  
https://github.com/test-scenarios/boost_beast_websocket_echo/blob/1d17ebe265381b5967f0a8139ced631c402afedf/pre-cxx20/chatterbox/connection.cpp#L125

---

## Comment 5

> Username: madmongo1  
> Created at: 2021-01-27 10:53:24 UTC  
> Url: https://github.com/boostorg/beast/issues/2153#issuecomment-768203275  

here I am ensuring that the send request (which can come from any thread or executor) is being marshalled onto my objects' strand:  
  
```  
    void connection_impl::send(std::string msg)  
    {  
        net::dispatch(  
            net::bind_executor(get_executor(), [self = shared_from_this(), msg = std::move(msg)]() mutable {  
                self->tx_queue_.push(std::move(msg));  
                self->maybe_send_next();  
            }));  
    }  
```

---

## Comment 6

> Username: emabiz  
> Created at: 2021-01-27 11:04:42 UTC  
> Url: https://github.com/boostorg/beast/issues/2153#issuecomment-768209438  

ah so you are using the same executor passed in the constructor,also for the queue management (I assume it is a strand), right?  
I used a different strand for the queue management, but maybe this is an error.  
  
Another question:  
dispatch for the strand is the same of doing a post on it, right?

---

## Comment 7

> Username: madmongo1  
> Created at: 2021-01-27 17:15:30 UTC  
> Url: https://github.com/boostorg/beast/issues/2153#issuecomment-768437720  

> ah so you are using the same executor passed in the constructor,also for the queue management (I assume it is a strand), right?  
> I used a different strand for the queue management, but maybe this is an error.  
  
Conceptually, the strand is doing the same job as a mutex. So you would have one strand per independent component of a program.  
  
A connection class might have a socket, a timer, a signal_set. They would all share the same strand because you want the completion handlers of all three to be executed on the same strand (otherwise you will have data races).  
  
In the same way, they should share the strand of the object itself. This way all completion handlers share the same strand and will never race.  
  
Note that the function passed to `post` or `dispatch` is also a completion handler. So posting to a strand is conceptually like locking a mutex and doing doing with the lock held. (although the mechanics are a little different).  
  
> dispatch for the strand is the same of doing a post on it, right?  
  
It's almost the same. It's more like this:  
```  
dispatch(handler):  
  if I am already running in the strand,  
      invoke handler() immediately  
  else,  
    post the handler to the strand for later sequential execution.  
```

---

## Comment 8

> Username: emabiz  
> Created at: 2021-01-27 17:47:21 UTC  
> Url: https://github.com/boostorg/beast/issues/2153#issuecomment-768458709  

hi,  
now it seems clear to me.  
From what I understand, managing the tx queue with a separated strand is an error, because I will call async_write from a different executor.  
  
Lets see how it works  
  
Thank you (for now) :)

---

## Comment 9

> Username: emabiz  
> Created at: 2021-01-28 08:20:58 UTC  
> Url: https://github.com/boostorg/beast/issues/2153#issuecomment-768882689  

Thinking more about strand usage, the thing that is not clear is the following.  
If I pass a strand to the constructor of the socket, the socket implementation assures you that the handler (callback) runs wrapped in the strand, but why the socket doesn't manage the dispatch/post internally when you call its methods like async_write?  
If it needs that the operations are not overlapped it should manage the strand completely internally and you should pass to its constructor simply the ioc, shouldn't it?

---

## Comment 10

> Username: stale[bot]  
> Created at: 2021-05-29 16:11:07 UTC  
> Url: https://github.com/boostorg/beast/issues/2153#issuecomment-850857893  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 11

> Username: stale[bot]  
> Created at: 2022-01-09 05:16:46 UTC  
> Url: https://github.com/boostorg/beast/issues/2153#issuecomment-1008232368  

It looks like this issue has either been abandoned or resolved so I will go ahead and close it. Feel free to re-open this issue if you feel it needs attention, or open new issues as needed. Thanks!

---

## Comment 12

> Username: emabiz  
> Created at: 2022-03-17 15:43:53 UTC  
> Url: https://github.com/boostorg/beast/issues/2153#issuecomment-1070995651  

Using this way to manage write operations, I can generate a big memory leak inside asio on linux (on win the behaviour is correct).  
To do this I overload the system, with multiple async operations.   
During the overload it is normal to see the cpu at 100% and the memory that increases, but if it stops to generate async events, the memory doesn't come back to a normal value on linux while it does correctly on win.  
The only way to clean the memory is to destroy the io_context.  
To see this in action you can use the attached example that simply uses a timer and a strand to simulate sockets, but it doesn't use sockets at all.  
Do you have experience on this issue?  
  
[memory_leak.zip](https://github.com/boostorg/beast/files/8287706/memory_leak.zip)

---

## Comment 13

> Username: madmongo1  
> Created at: 2022-03-17 15:59:23 UTC  
> Url: https://github.com/boostorg/beast/issues/2153#issuecomment-1071014289  

Surely if the memory is recovered when the io context is destroyed, there is no memory leak?  
Am I missing something?

---

## Comment 14

> Username: madmongo1  
> Created at: 2022-03-17 16:00:20 UTC  
> Url: https://github.com/boostorg/beast/issues/2153#issuecomment-1071015431  

Ok I see you have posted an example. I’ll look at it when I get in.

---

## Comment 15

> Username: emabiz  
> Created at: 2022-03-17 16:01:31 UTC  
> Url: https://github.com/boostorg/beast/issues/2153#issuecomment-1071016931  

I don't want to destroy the io_context, while the application is running.  
I destroy the io_context to demonstrate that the memory stay allocated inside of it.  
Thank you for your help

---

## Comment 16

> Username: emabiz  
> Created at: 2022-03-17 16:41:27 UTC  
> Updated at: 2022-03-17 16:42:04 UTC  
> Url: https://github.com/boostorg/beast/issues/2153#issuecomment-1071066595  

I changed a bit the steps, maybe this is better, but also the one contained in the zip should be ok.  
  
```  
int main(int argc, char** argv)  
{  
	uint16_t num = 70;  
	size_t threads = 4;  
	int interval = 1;  
  
	std::cout << "You can monitor the memory of the process with htop" << std::endl;  
  
  
	IOEngine* engine = new IOEngine(threads);  
  
	std::shared_ptr<DataManager> data_manager = std::make_shared<DataManager>();  
	data_manager->start(engine->ioc(), num, interval);  
  
	engine->run();  
  
	std::cout << "When you see that the memory starts growing a lot, then press ENTER" << std::endl;  
	std::cin.ignore(std::numeric_limits<std::streamsize>::max(), '\n');  
  
	data_manager->stop();  
	std::cout << "Wait until cpu comes back to 0%" << std::endl;  
	std::cout << "When the cpu reaches 0%, then press ENTER" << std::endl;  
	std::cin.ignore(std::numeric_limits<std::streamsize>::max(), '\n');  
	data_manager = nullptr;  
  
	std::cout << "data_manager has been destroyed." << std::endl;	  
	std::cout << "If the bug has been reproduced, you should see that the memory of the process is still high" << std::endl;  
	std::cout << "Now press ENTER again" << std::endl;  
	std::cin.ignore(std::numeric_limits<std::streamsize>::max(), '\n');  
  
	engine->stop();  
	engine = nullptr;  
  
	std::cout << "Now ioc has been destroyed and the memory should be released." << std::endl;  
	std::cout << "Press ENTER to terminate" << std::endl;  
	std::cin.ignore(std::numeric_limits<std::streamsize>::max(), '\n');  
  
	return 0;  
  
}  
```

---

## Comment 17

> Username: madmongo1  
> Created at: 2022-03-17 17:52:40 UTC  
> Url: https://github.com/boostorg/beast/issues/2153#issuecomment-1071148320  

Not sure whether it's relevant, but this is a data race:  
```  
  void stop() {  
    _running = false;  
    std::error_code ec;  
    _timer.cancel(ec);  
  }  
```  
  
The timer is being cancelled on the wrong thread (the main thread).  
  
I think this would be preferable:  
```  
  void stop() {  
    _running = false;  
    asio::post(_timer.get_executor(), [self = shared_from_this()]  
    {  
      std::error_code ec;  
      self->_timer.cancel(ec);  
    });  
  }  
```  
  
Strictly, according to the asio docs, calling `_timer.get_executor()` on thread 1 is also a data race, but we can live with it as we know it's harmless.

---

## Comment 18

> Username: madmongo1  
> Created at: 2022-03-17 18:21:00 UTC  
> Url: https://github.com/boostorg/beast/issues/2153#issuecomment-1071182212  

Is it possible that what we are seeing is malloc's thread-local caches being freed up when the `ioc::run` threads exit?

---

## Comment 19

> Username: madmongo1  
> Created at: 2022-03-17 19:47:43 UTC  
> Updated at: 2022-03-17 19:48:09 UTC  
> Url: https://github.com/boostorg/beast/issues/2153#issuecomment-1071296540  

Confirmed.  
  
The memory actually gets freed in the function `__malloc_arena_thread_freeres` during the destruction of the `std::thread`.  
This is nothing to do with Asio, it's an implementation detail of the linux system libraries.

---

## Comment 20

> Username: madmongo1  
> Created at: 2022-03-17 19:48:23 UTC  
> Url: https://github.com/boostorg/beast/issues/2153#issuecomment-1071298098  

May I close the issue?

---

## Comment 21

> Username: emabiz  
> Created at: 2022-03-17 20:06:29 UTC  
> Url: https://github.com/boostorg/beast/issues/2153#issuecomment-1071337644  

Can I ask you how you detect that the memory belongs to std::thread?  
If the bug is not related to asio you can close.  
For me it is a bit difficult to debug asio on Linux, so if you have any suggestion on how to do that, which tools is convenient to use, in order to see these low level parts, I will appreciate a lot.  
Anyway I have to resolve this, because in the real world it can happen that in some moments the machine is overloaded and the process starts eating memory.

---

## Comment 22

> Username: emabiz  
> Created at: 2022-03-17 20:38:58 UTC  
> Url: https://github.com/boostorg/beast/issues/2153#issuecomment-1071411973  

Also in the real application, where I use real buffers passed to  the real async write of the socket I have a that the memory increases faster and a lot. I don't know if it is related to thread cache, or lambda management, but it is a big issue for me.

---

## Comment 23

> Username: madmongo1  
> Created at: 2022-03-18 07:01:01 UTC  
> Url: https://github.com/boostorg/beast/issues/2153#issuecomment-1072084499  

I can only comment on the test application you have supplied.   
I debugged the code by running it in a debugger while simultaneously running `htop` as you suggested. I set set breakpoints at various strategic places in the code.  
I found that the memory release happened during the thread destruction, so I then ran again and single-stepped through the code, into the library calls themselves, while watching `htop`.  
  
It is entirely possible that you have a separate memory leak in the real program of course.

---

## Comment 24

> Username: emabiz  
> Created at: 2022-03-18 08:10:11 UTC  
> Updated at: 2022-03-18 08:10:29 UTC  
> Url: https://github.com/boostorg/beast/issues/2153#issuecomment-1072124620  

I modified the example using a real buffer in the queue.  
Now the memory increases very fast and it seems not released when io_context is destroyed.  
  
(I also fixed the cancel of timer as you suggested, but you also said that _timer.get_executor() should not be called on thread 1, then with less priority I'd like to know which should be the correct way of using it)  
  
[memory_leak.zip](https://github.com/boostorg/beast/files/8302663/memory_leak.zip)

---

## Comment 25

> Username: madmongo1  
> Created at: 2022-03-18 10:07:13 UTC  
> Updated at: 2022-03-18 10:08:11 UTC  
> Url: https://github.com/boostorg/beast/issues/2153#issuecomment-1072260183  

>with less priority I'd like to know which should be the correct way of using it  
  
Take a copy of the strand and store it as a member variable separately from the timer.

---

## Comment 26

> Username: madmongo1  
> Created at: 2022-03-18 10:15:05 UTC  
> Url: https://github.com/boostorg/beast/issues/2153#issuecomment-1072266155  

> and it seems not released when io_context is destroyed.  
  
It's important to make the distinction between:  
  
1 - The memory is not added back to the process-wide free list maintained by malloc/free, or  
2 - Memory is not being deallocated from the process-wide free list and returned to the kernel as free memory.  
  
(1) would be a memory leak as a result of a logic error  
(2) would be absolutely expected, because the way that malloc/free works is that feed memory is kept in the process for later re-allocation.

---

## Comment 27

> Username: emabiz  
> Created at: 2022-03-18 14:08:04 UTC  
> Url: https://github.com/boostorg/beast/issues/2153#issuecomment-1072446597  

I made another modification to my example.  
Now it generates data for 15 seconds and then it stops. After each generation cycle, when the cpu comes back to zero, you can manually restart the generation or exit .  
  
The results I get are the following (on my machine 4 cores 16GB of RAM):  
  
1. Running only one instance of the application does exactly what you said in point number (2). After the first cycle no more memory is used. if you restart a generation cycle, the memory doesn't increase. Then it seems that memory is allocated by the process but not currently used, ready to be used again.  
  
2. Running 3 instances of the application sequentially (waiting for the previous one stops to consume cpu/memory before to run the next one. I also disabled the swap) I have that the first consumes about half of memory, the second almost the second half, when I launch the third then one of the others 2 dies (the first 2 are idle, not working). This means to  me that the allocated memory by the first 2 processes is in use and not available, so it seems a real memory leak.  
  
  
I also read this:  
https://www.celonis.com/blog/c-memory-arenas-and-their-implications/  
  
but trying to use malloc_stats() and malloc_trim(0) is not giving to me any information I can understand for now.  
  
I attach my new example  
[memory_leak.zip](https://github.com/boostorg/beast/files/8305050/memory_leak.zip)

---

## Comment 28

> Username: emabiz  
> Created at: 2022-03-22 09:40:44 UTC  
> Url: https://github.com/boostorg/beast/issues/2153#issuecomment-1074940788  

Point number 2 is understandable on linux because the os chooses the application to kill using select_bad_process().  
Then because the memory is not released, also if it is not in use, it can happen that the os decides to kill not the application that is requesting the memory in that moment but another one that requested more memory in the past.  
  
About the effect of memory usage, I arrived to the conclusion that it is a side effect on asio performances on linux, that seem worse than on windows, at least under heavy conditions like the ones of the example, using the same hardware.  
Do you have experience on tuning asio on linux to have the best performances that the library can handle?  
Or are we already at the maximum it can do?
