# #1985 - Shutdown operation takes around 3 mins [Closed]

> Username: GaneshTambat1  
> Created at: 2020-06-12 15:35:53 UTC  
> Updated at: 2020-06-12 15:57:40 UTC  
> Closed at: 2020-06-12 15:41:47 UTC  
> Url: https://github.com/boostorg/beast/issues/1985  

I am seeing an issue where the shutdown operation takes around 3 mins and eventually it fails with error "Error: An existing connection was forcibly closed by the remote host".   
  
I referred code sample available at https://github.com/boostorg/beast/blob/develop/example/http/client/sync-ssl/http_client_sync_ssl.cpp.  
  
I have modified above code sample a little to support OAuth.  
  
My env details:  
openssl version: vc142  
Visual Studio version: 2019  
OS: Windows Server 2016  
BOOST_BEAST_VERSION: 290   
  
Is this something that can be fixed / known issue? Please let me know.  
  
Relevant code area:  
*****  
beast::error_code ec;  
stream.shutdown(ec);   
*****  
  
Here stream object is of type: beast::ssl_stream<beast::tcp_stream>   
  
Thanks,  
GT  
  
  
  
PLEASE DON'T FORGET TO "STAR" THIS REPOSITORY :)  
  
If you rather keep your project secret, feel free  
to email the author at `<vinnie.falco@gmail.com>`; any  
private correspondence is treated as confidential.  
  
When reporting a bug please include the following:  
  
### Version of Beast  
  
You can find the version number in the file `<boost/beast/version.hpp>`,  
or by using the command "git log -1" in the local Beast repository  
directory.  
  
### Steps necessary to reproduce the problem  
  
A small compiling program is the best. If your code is  
public, you can provide a link to the repository.  
  
### All relevant compiler information  
  
If you are unable to compile please include the type and  
version of compiler you are using as well as all compiler  
output including the error message, file, and line numbers  
involved.  
  
The more information you provide the sooner your issue  
can get resolved!

---

## Comment 1

> Username: madmongo1  
> Created at: 2020-06-12 15:41:47 UTC  
> Url: https://github.com/boostorg/beast/issues/1985#issuecomment-643341845  

This is because the remote server closed the underlying TCP socket connection without bothering to wait for your client to initiate an SSL shutdown.   
  
It's quite normal.  
  
The workaround for you is to simply skip the ssl shutdown in your code and close the underlying tcp socket as the server is doing.  
  
The longer term solution is to use async code in which case you can use the beast::basic_stream to add timeout support to your connection.

---

## Comment 2

> Username: GaneshTambat1  
> Created at: 2020-06-12 15:45:56 UTC  
> Url: https://github.com/boostorg/beast/issues/1985#issuecomment-643343807  

Yes. Eventually we will use Async calls. This experiment was just to evaluate Beast library.   
  
I did not get the part "close the underlying tcp socket as the server is doing." Is there any method provided by the library to do this?

---

## Comment 3

> Username: madmongo1  
> Created at: 2020-06-12 15:57:40 UTC  
> Url: https://github.com/boostorg/beast/issues/1985#issuecomment-643349606  

Yes, you are using an `ssl_stream<tcp::socket>` as your transport. The member function `next_layer()` returns a reference to the next layer, which is the tcp socket.  
  
https://www.boost.org/doc/libs/1_73_0/libs/beast/doc/html/beast/ref/boost__beast__ssl_stream/next_layer.html
