# #6 - typo in readme example [Closed]

> Username: mayur-IG  
> Created at: 2024-02-15 09:16:56 UTC  
> Updated at: 2024-02-15 09:34:01 UTC  
> Closed at: 2024-02-15 09:34:01 UTC  
> Url: https://github.com/boostorg/mqtt5/issues/6  

Please update the readme.md example   
	  
	c.credentials(......)  
		.....)  
		**.async_run(asio::detached);**  
		  
to:  
		**.async_run(boost::asio::detached);**
