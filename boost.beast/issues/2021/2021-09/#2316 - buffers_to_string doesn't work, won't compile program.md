# #2316 - buffers_to_string doesn't work, won't compile program [Closed]

> Username: michael-brennan2005  
> Created at: 2021-09-22 04:15:41 UTC  
> Updated at: 2022-09-24 05:09:35 UTC  
> Closed at: 2022-09-24 05:09:35 UTC  
> Url: https://github.com/boostorg/beast/issues/2316  

PLEASE DON'T FORGET TO "STAR" THIS REPOSITORY :)  
  
If you rather keep your project secret, feel free  
to email the author at `<vinnie.falco@gmail.com>`; any  
private correspondence is treated as confidential.  
  
When reporting a bug please include the following:  
  
### Version of Beast  
313  
### Steps necessary to reproduce the problem  
`  
std::string CodekataConnection::SendMessageAndListen(std::string message)  
{  
	beast::flat_buffer buffer;  
	ws.write(net::buffer(message));  
	ws.read(buffer);  
    return buffers_to_string(buffer);  
}  
`  
  
ws is a standard websocket, it is a class member of the CodekataConnection  
  
### All relevant compiler information  
MSVC++ on Visual Studio 2019, using Vcpkg to manage all of my dependencies.  
  
These are the errors I am getting:  
![image](https://user-images.githubusercontent.com/76018881/134282574-651d1f95-acfc-4e31-98ad-4cd93dde5c41.png)

---

## Comment 1

> Username: madmongo1  
> Created at: 2021-09-22 05:45:46 UTC  
> Url: https://github.com/boostorg/beast/issues/2316#issuecomment-924605190  

Try:  
  
buffers_to_string(buffer.data())

---

## Comment 2

> Username: stale[bot]  
> Created at: 2022-01-09 03:16:47 UTC  
> Url: https://github.com/boostorg/beast/issues/2316#issuecomment-1008220728  

This issue has been open for a while with no activity, has it been resolved?
