# #2858 - Session communication without Mutex? [Closed]

> Username: vtharmalingam  
> Created at: 2024-04-24 16:58:01 UTC  
> Updated at: 2024-07-24 06:18:24 UTC  
> Closed at: 2024-07-24 06:18:24 UTC  
> Url: https://github.com/boostorg/beast/issues/2858  

1.84  
  
## Looking for advice on migrating the session communication  
  
### Older  version   
Concerning session communication (with client), I had developed an app using beast example a few years ago.  
Wherein i used "mutex" for guarding the write as well as "vector" for queuing the messages, etc. Here is a snippet of how it is:  
  
```cpp  
  
// a global instance of std::mutex to protect  
global variable std::mutex myQueueMutex;  
  
  
std::vector<std::shared_ptr<const std::string>> queue_;  
  
```  
And my write function is like this:  
  
```cpp  
  
void ssl_websocket_session::on_write(beast::error_code ec, std::size_t)   
{   
    std::lock_guard<std::mutex> guard(myQueueMutex);   
    {   
        //... <you write to your session>   
    }   
      
}  
  
  
```  
  
### In the latest example,  
When I recently studying the latest examples, I found that this part of the code has been migrated, made bit simpler and perhaps sounds more efficient too—without mutex and vector. All managed through "buffer". I am looking for your guidance on whether I can migrate to this one without any drawbacks.  
  
```cpp  
  
// we use the buffer  
beast::flat_buffer buffer_;  
  
```  
  
And we use this write function  
  
```cpp  
  
    void  
    on_write(  
        beast::error_code ec,  
        std::size_t bytes_transferred)  
    {  
        boost::ignore_unused(bytes_transferred);  
  
        if(ec)  
            return fail(ec, "write");  
  
        // Clear the buffer  
        buffer_.consume(buffer_.size());  
  
        // Do another read  
        do_read();  
    }  
  
```  
  
Thanks in advance!  
  
Regards,  
Tharma

---

## Comment 1

> Username: vinniefalco  
> Created at: 2024-04-24 17:04:12 UTC  
> Url: https://github.com/boostorg/beast/issues/2858#issuecomment-2075432966  

The point of the mutex is that foreign threads can concurrently call a "send" function to add the string to a queue. If you want that feature then you have to have the mutex. Alternatively you can call `asio::post` to submit a function object which, when invoked, adds the string to the `vector` without the need for a mutex. But note that `asio::post` uses an internal mutex.  
  
If you are worried about performance, don't be. There is little to no contention for the lock, as the mutex is held for the shortest possible time, and you don't have such a large number of threads trying to queue a string at the same time that the performance would matter.

---

## Comment 2

> Username: vtharmalingam  
> Created at: 2024-04-25 02:36:08 UTC  
> Url: https://github.com/boostorg/beast/issues/2858#issuecomment-2076248550  

Very helpful, thank you, @vinniefalco.

---

## Comment 3

> Username: vtharmalingam  
> Created at: 2024-04-25 16:37:49 UTC  
> Url: https://github.com/boostorg/beast/issues/2858#issuecomment-2077712069  

Quick follow-up question, @vinniefalco:  Is `std::vector<std::shared_ptr<const std::string>>` the recommended container for the message `queue_` ? Maybe I am missing, in the recent example, `vector` was not found used, but instead some sort of buffer was used?

---

## Comment 4

> Username: vinniefalco  
> Created at: 2024-04-25 19:06:54 UTC  
> Url: https://github.com/boostorg/beast/issues/2858#issuecomment-2077993789  

I like the vector because it is very clear and understandable. I don't know what's going on with this other buffer. Who changed it? Maybe the buffer is more efficient. But the vector is more readable, as evidenced by your question.

---

## Comment 5

> Username: ashtum  
> Created at: 2024-07-04 16:32:08 UTC  
> Url: https://github.com/boostorg/beast/issues/2858#issuecomment-2209323583  

@vtharmalingam, is this issue still open or has the problem been resolved?
