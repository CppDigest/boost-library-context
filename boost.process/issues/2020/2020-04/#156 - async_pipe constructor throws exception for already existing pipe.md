# #156 - async_pipe constructor throws exception for already existing pipe [Closed]

> Username: ankitMeta1  
> Created at: 2020-04-17 18:44:26 UTC  
> Updated at: 2020-05-20 14:04:43 UTC  
> Closed at: 2020-05-20 14:04:43 UTC  
> Url: https://github.com/boostorg/process/issues/156  

if the pipe name already exists the constructor for async_pipe throws exception, which makes it impossible to use it for inter-process communication between two separate processes.

---

## Comment 1

> Username: ankitMeta1  
> Created at: 2020-04-17 18:46:09 UTC  
> Url: https://github.com/boostorg/process/issues/156#issuecomment-615405277  

`  
async_pipe::async_pipe(boost::asio::io_context & ios_source,  
  
                       boost::asio::io_context & ios_sink,  
  
                       const std::string & name) : _source(ios_source), _sink(ios_sink)  
  
{  
  
    auto fifo = mkfifo(name.c_str(), 0666 );  
  
  
  
    if (fifo != 0)  
  
        boost::process::detail::throw_last_error("mkfifo() failed");`

---

## Comment 2

> Username: klemens-morgenstern  
> Created at: 2020-05-20 14:04:43 UTC  
> Url: https://github.com/boostorg/process/issues/156#issuecomment-631494564  

Well aware, the pipes are not meant to be used that way. Using them for IPC by pipe name will be a lot more work, and should probably be a different library.
