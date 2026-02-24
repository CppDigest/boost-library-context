# #2742 - help with streaming very large http body [Closed]

> Username: iofo  
> Created at: 2023-09-19 16:43:31 UTC  
> Updated at: 2023-09-20 14:38:46 UTC  
> Closed at: 2023-09-20 14:38:46 UTC  
> Url: https://github.com/boostorg/beast/issues/2742  

Dear Beast Experts,  
I am trying to stream multiple very large https bodies from a GET request and decompress the gz files on the fly and get chunks of data and merge in time order the time-series from all the files.  I issue http::async_read's for every URL until I have enough data to decompress at least the first line from every file. I will then parse the line to pull out a timestamp and then I will process the data in timestamp order effectively merging the HTTP get data.  Once I have run out of data for a file I will issue another http::async_read to pull in the next chunks and relying on tcp windows to pause the transmissions whilst I process (processing time is fast so it shouldn't be an issue).  
The initial reads seem to work fine but when I go and  issue the next http:async_read the completion handler is never called and I get into an infinite loop calling io.run() over and over.  
A simplified code is here   
[https://github.com/iofo/testdata.git ](https://github.com/iofo/testdata.git )  
  
doing just the IO portion with test data.  
Any suggestions greatly appreciated.  
Thanks

---

## Comment 1

> Username: iofo  
> Created at: 2023-09-20 14:38:46 UTC  
> Url: https://github.com/boostorg/beast/issues/2742#issuecomment-1727865621  

So, it turns out this is nothing to do with beast and I need to call io_service::reset after the call to io_service::run completes for the first batch of data.  
Sorry to disturb everyone.  
Carry on.
