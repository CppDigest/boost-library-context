# #1162 - std::distance called with a circular buffer, causing infinite loop [Closed]

> Username: ghost  
> Created at: 2018-06-14 20:24:49 UTC  
> Updated at: 2022-01-01 22:36:50 UTC  
> Closed at: 2018-06-15 16:01:02 UTC  
> Url: https://github.com/boostorg/beast/issues/1162  

Hello !  
  
I am writing a game server using websockets with the help of Beast. I encountered a weird bug recently after I tested my server with a test client that launches 10 threads, each simulating 10 players that write random but valid stuff every 250ms, then reads anything the server is sending, then writes again, etc... during 40 seconds. That's for the client.  
   
The server has one thread to catch user input, one thread for the game's loop, and one or more threads for the network part, which run io_context::run(). I made sure only the network thread calls async operations on the client's websockets. Each connection has its own strand. Plus I use a steady_timer to call periodically all async_write on all sockets if there is something to write.  
  
So the problem was that almost everytime, after I launched my test client, after about 18 seconds, my io_context ceased to execute any async operation. I am sure there were at least one pending async operation (if not an async_read, at least the async_wait on my timer). The io_context.run() call did not return so it has to be either an infinite loop in run(), or a deadlock somewhere. I investigated a bit with Visual Studio and found an infinite loop in a call to std::distance related to buffers I'm using to write :  
  
As you can see in the red frame, _First->next->next is _First, so std::distance will never reach _Last.  
  
I am pretty sure I am misusing my multi_buffer objects and it could be the cause of this bug. So here is how I use them:  
  
- I have one multi_buffer per connection  
- For the write part I concat every pending JSON object into a JSON array to be received by the client. I use prepare() with the size of my newly built message, and use buffer_copy to copy it into a buffer object that I can pass to async_write.  
```  
...  
auto full_msg = new std::string;  
*full_msg = "[";  
for (auto & msg : to_write_)  
    *full_msg += std::move(msg) + ",";  
*full_msg->rbegin() = ']';  
  
to_write_.clear();  
  
auto bufs = buffer_.prepare(full_msg->size());  
asio::buffer_copy(bufs, asio::buffer(*full_msg));  
  
socket_.async_write(bufs, asio::bind_executor(strand_,  
   boost::bind(&ws_conn::on_write, shared_from_this(), _1, _2, full_msg)));  
```  
- For the read part :  
```  
...  
std::string str;  
for (auto const& buf : buffer_.data())  
    str += std::move(std::string(static_cast<char const *>(buf.data()), buf.size()));  
buffer_.consume(buffer_.size());  
...  
```  
  
Am I using this class correctly ?  
  
I hope the details I gave you are relevant and clear enough and that my english is understandable.  
  
Thank you !

---

## Comment 1

> Username: vinniefalco  
> Created at: 2018-06-14 20:38:03 UTC  
> Updated at: 2018-06-14 20:41:44 UTC  
> Url: https://github.com/boostorg/beast/issues/1162#issuecomment-397430523  

> Am I using this class correctly ?  
  
Nope! Calling `prepare`, `commit`, or `consume` **invalidates** any buffers previously obtained by calling `prepare` or `data`! You need to write a real queue and append each message to it separately. When you pull from the queue you can either send each item as its own message, or you can coalesce everything there into one buffer. This chat server shows how to implement a write queue:  
  
https://github.com/vinniefalco/CppCon2018/blob/3067248d5488d130fd3caaee2648774e677cea0d/websocket_session.cpp#L95  
  
tl;dr: A **DynamicBuffer** is not a proper queue.

---

## Comment 2

> Username: vinniefalco  
> Created at: 2018-06-14 20:50:22 UTC  
> Url: https://github.com/boostorg/beast/issues/1162#issuecomment-397433869  

```  
std::string str;  
for (auto const& buf : buffer_.data())  
    str += std::move(std::string(static_cast<char const *>(buf.data()), buf.size()));  
```  
  
You can replace all this code with:  
  
```  
auto str = boost::beast::buffers_to_string(buffer_.data());  
```  
  
It will also be more efficient since it uses `reserve` and only allocates once.

---

## Comment 3

> Username: ghost  
> Created at: 2018-06-15 13:35:28 UTC  
> Url: https://github.com/boostorg/beast/issues/1162#issuecomment-397621984  

to_write_ is my write queue, similar to the one in the example. I'll try to shortly describe my code :  
  
Here are the most important members of my ws_conn class :  
```  
class ws_conn : public std::enable_shared_from_this<ws_conn>  
{  
    enum state  
    {  
        ...  
        reading,  
        writing,  
        ...  
    };  
    ...  
    socket_t socket_;  
    asio::strand<asio::io_context::executor_type>   strand_;  
    state state_;  
    beast::multi_buffer buffer_;  
    std::list<std::string> to_write_;  
    std::recursive_mutex  conn_mutex_;  
    ...  
};  
```  
I have a ws_conn::write() method similar to the websocket_session::send() but it just push_back a message in the queue and returns immediately :  
```  
void ws_conn::write(std::string const& msg)  
{  
    std::lock_guard<decltype(conn_mutex_)> l(conn_mutex_);  
  
    to_write_.push_back(msg);  
}  
```  
  
ws_conn::write() cannot do an async_write as it will be called by a thread not running io_context::run() : the thread running the gameloop :  
```  
void server::loop(unsigned int nb_ticks)  
{  
    std::lock_guard<decltype(server_mutex_)> l(server_mutex_);  
    ...  
    std::string jsonentities = entities_to_json();  
  
    for (auto &c : conns_)  
        c.second->write(jsonentities);  
}  
```  
ws_conn::write_next() is the method doing the async_write. Just after that, it sets state_ to "writing". It does nothing if the state_ is not "reading" i.e. if the socket is opening or closing or more importantly if the write handler has not been called yet :  
```  
void ws_conn::write_next()  
{  
    std::lock_guard<decltype(conn_mutex_)> l(conn_mutex_);  
  
    if (state_ != reading)  
        return;  
  
    if (to_write_.empty())  
        return;  
  
    auto full_msg = new std::string;  
    *full_msg = "[";  
    for (auto & msg : to_write_)  
        *full_msg += std::move(msg) + ",";  
    *full_msg->rbegin() = ']';  
  
    to_write_.clear();  
  
    auto bufs = buffer_.prepare(full_msg->size());  
    asio::buffer_copy(bufs, asio::buffer(*full_msg));  
  
    socket_.async_write(bufs, asio::bind_executor(strand_,  
        boost::bind(&ws_conn::on_write, shared_from_this(), _1, _2, full_msg)));  
  
    state_ = writing;  
}  
```  
ws_conn::write_next() is called by my steady_timer handler :  
```  
void server::trigger_writes(boost::system::error_code const& ec) noexcept  
{  
    std::lock_guard<decltype(server_mutex_)> l(server_mutex_);  
  
    for (auto &c : conns_)  
        c.second->write_next();  
  
     t_->expires_after(500ms);         
     t_->async_wait(boost::bind(&server::trigger_writes, this, _1));  
}  
```  
My write handler is the only part of the code that set the state_ back to "reading" :  
```  
void ws_conn::on_write(beast::error_code const& ec,  
    std::size_t bytes_transferred, std::string *msg) noexcept  
{  
    std::lock_guard<decltype(conn_mutex_)> l(conn_mutex_);  
  
    if (state_ != writing)  
        return;  
  
    state_ = reading;  
}  
```  
As you can see, with the help of my state_ member, the next call to prepare() cannot be made until the write handler has been called. So I really believe that the buffers I passed to async_write were not corrupted by a second illegal call.  
  
That being said, I tried not using prepare() at all and passing directly boost::asio::buffer(*full_msg) to async_write as in the example you gave me and everything works fine now ! I will continue with that solution but I really don't understand how I could have invalidate my buffers.  
  
Thank you very much for your help and for beast :)

---

## Comment 4

> Username: vinniefalco  
> Created at: 2018-06-15 14:05:34 UTC  
> Updated at: 2018-06-15 14:07:35 UTC  
> Url: https://github.com/boostorg/beast/issues/1162#issuecomment-397631826  

Instead of periodically sending everything in the write queues, you could send on demand. If `write` is called while the queue is empty, then post a call to write from the proper context:  
  
```  
void  
ws_conn::write(std::string msg)  
{  
    std::lock_guard<decltype(conn_mutex_)> l(conn_mutex_);  
    to_write_.emplace_back(std::move(msg));  
    if(to_write_.size() == 1)  
        asio::post(  
            socket_.get_executor(),  
            asio::bind_executor(  
                strand_,  
                std::bind(&ws_conn::write_next, shared_from_this())));  
}  
```  
  
This code is incorrect:  
  
```  
    auto bufs = buffer_.prepare(full_msg->size());  
    asio::buffer_copy(bufs, asio::buffer(*full_msg));  
  
    socket_.async_write(bufs, asio::bind_executor(strand_,  
        boost::bind(&ws_conn::on_write, shared_from_this(), _1, _2, full_msg)));  
```  
  
There's a lot going on here. I guess `full_msg` is a `shared_ptr` and you are keeping it alive? I don't understand why you are doing that. You could just send `full_msg` directly using `boost::asio::buffer` as you noted. However, you are not using `prepare` correctly, since you never call `commit`. You can write this instead:  
  
```  
buffer_.commit(asio::buffer_copy(  
    buffer_.prepare(full_msg->size()),  
    boost::asio::buffer(*full_msg)));  
```  
  
Then:  
```  
    socket_.async_write(  
    buffer_.data(),  
    asio::bind_executor(  
        strand_,  
        std::bind(&ws_conn::on_write, shared_from_this(), _1, _2)));  
```  
  
Of course it is better to just skip `buffer_` entirely and send from the `shared_ptr`. I bring this up so that if you use a dynamic buffer in the future, you will know how to use it correctly. Note that your abuse of `prepare` should have still worked.

---

## Comment 5

> Username: vinniefalco  
> Created at: 2018-06-15 14:15:21 UTC  
> Url: https://github.com/boostorg/beast/issues/1162#issuecomment-397634710  

It looks like `full_msg` is being leaked here:  
  
```  
    auto full_msg = new std::string;  
    *full_msg = "[";  
    for (auto & msg : to_write_)  
        *full_msg += std::move(msg) + ",";  
    *full_msg->rbegin() = ']';  
```  
  
I see a lot of odd implementation choices...is your code in a GitHub repository? I think it would be best if I could see the whole file and comment on it through the GitHub interface.

---

## Comment 6

> Username: ghost  
> Created at: 2018-06-15 16:01:02 UTC  
> Url: https://github.com/boostorg/beast/issues/1162#issuecomment-397666686  

I badly misunderstood the purpose of multi_buffer by reading way too fast the documentation and examples. I am lucky it did not crash sooner !  
  
Thanks again for your time and your advice ! I'm going to try what you suggested with asio::post() right away.

---

## Comment 7

> Username: vinniefalco  
> Created at: 2018-06-15 16:07:14 UTC  
> Url: https://github.com/boostorg/beast/issues/1162#issuecomment-397668284  

Glad to hear that you are resolving these issues, but I should also point out that the way you are building your JSON strings is not optimal. As written, your code is performing many more dynamic allocations than necessary, resulting in reduced performance.
