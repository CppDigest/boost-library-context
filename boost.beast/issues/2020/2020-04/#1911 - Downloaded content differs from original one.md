# #1911 - Downloaded content differs from original one [Closed]

> Username: FabrizioDys  
> Created at: 2020-04-22 10:11:40 UTC  
> Updated at: 2021-08-20 15:48:20 UTC  
> Closed at: 2020-05-02 19:21:31 UTC  
> Url: https://github.com/boostorg/beast/issues/1911  

using Boost 1.71  
  
Hi again!   
I am using beast to retrieve an object I uploaded on a server a while a go for testing purposes. This object is a bitstream representing a compressed 3D mesh. Once the download is over I save my data into a file using this code after the standard example code of beast http_client_sync:  
  
   
         // Declare a container to hold the response  
        http::response<http::dynamic_body> resp;  
  
        // Receive the HTTP response  
        http::read(stream, buffer, resp);  
  
        // Write the message to standard out  
        std::ofstream outpoot("C:/downloaded_content.p3d");  
        outpoot << resp;  
        outpoot.close();  
  
  
The problem is that the downloaded content differs in some lines with respect to the original uploaded file, more specifically there are around 10/12 empty lines that aren't present in the original file, example:  
  
File downloaded with boost, lines 267, 268, 269  
  
> ºq óùYjØ/÷‹%GÊJ?†qóÿn7j~³í:õê)7âZ¨;z¨Â¥Uq@ÊíCSV"<)  
>   
> Zv{âÜÎI9†Rûñéå¤ö’¥Ì&ë Uš=•ôcpíŸ½…ñè­ª\2¯×¾b5­O  
  
Original file I uploaded on the server, lines 267,268,269  
  
> ºq óùYjØ/÷‹%GÊJ?†qóÿn7j~³í:õê)7âZ¨;z¨Â¥Uq@ÊíCSV"<)  
> Zv{âÜÎI9†Rûñéå¤ö’¥Ì&ë Uš=•ôcpíŸ½…ñè­ª\2¯×¾b5­O  
> »õ C·¼ùXe.C‚.×W/‡’|Ät«y4Þe\þ{J¤c”àÖ:>Ñ¶:èÚ_p:b˜ÕåÏÌ9ø|¢  
  
I assumed the problem could be cause by one of these two factors ( but maybe I am going off-the-rails ):  
1- The assignment of the type http::response<http::dynamic_body> as a right operand of " << " even though is not considered an error, causes some bad interpretation that leads to the differences mentioned;  
2- Maybe those lines are related to packetization of the content on the internet?   
  
First I tried addressing my first hypothesis by looking inside the member functions of the template class which my variable "resp" belongs to, and I located the member "body" which apparently should be the body of the HTTP response, but if I try to do:  
  
`outpoot << resp.body;`  
  
it results in an error because the type of the right operand is not accepted by "<<"  
  
  
If someone has any idea I would appreciate some help :-) Thank you in advance.

---

## Comment 1

> Username: FabrizioDys  
> Created at: 2020-04-27 07:43:26 UTC  
> Updated at: 2020-04-27 07:44:11 UTC  
> Url: https://github.com/boostorg/beast/issues/1911#issuecomment-619795974  

I add some update, maybe the difference is due to the fact that I want to have the result as bytes but I get a stream:  
  
          
  
> // These objects perform our I/O  
>         tcp::resolver resolver(ioc);  
>         beast::ssl_stream<beast::tcp_stream> stream(ioc, ctx);  
>           
>   
>         // Set SNI Hostname (many hosts need this to handshake successfully)  
>         if(! SSL_set_tlsext_host_name(stream.native_handle(), host))  
>         {  
>             beast::error_code ec{static_cast<int>(::ERR_get_error()), net::error::get_ssl_category()};  
>             throw beast::system_error{ec};  
>         }  
>   
>         // Look up the domain name  
>         auto const results = resolver.resolve(host, port);  
  
maybe I do have to change something here?

---

## Comment 2

> Username: madmongo1  
> Created at: 2020-04-30 14:58:26 UTC  
> Url: https://github.com/boostorg/beast/issues/1911#issuecomment-621908293  

Could it be that the file stream needs to be opened in `binary` mode? Windows will add `\r` characters before `\n` characters in a `text` stream.

---

## Comment 3

> Username: madmongo1  
> Created at: 2020-05-01 15:56:31 UTC  
> Url: https://github.com/boostorg/beast/issues/1911#issuecomment-622445698  

Hi @FabrizioDys ,  
  
Did you get to the bottom of this? Is there something more you'd like me to investigate or may I close the issue?

---

## Comment 4

> Username: FabrizioDys  
> Created at: 2020-05-02 19:21:31 UTC  
> Url: https://github.com/boostorg/beast/issues/1911#issuecomment-623001523  

Hi! I managed to solve it, thank you :)

---

## Comment 5

> Username: vinniefalco  
> Created at: 2020-05-02 19:21:47 UTC  
> Url: https://github.com/boostorg/beast/issues/1911#issuecomment-623001553  

What was the problem?

---

## Comment 6

> Username: FabrizioDys  
> Created at: 2020-05-02 19:30:52 UTC  
> Url: https://github.com/boostorg/beast/issues/1911#issuecomment-623002599  

I declared the ofstream object without specifying that the second parameter of the constructor should be "std::ofstream::binary". By doing that everything was solved :)  
  
` std::ofstream outpoot("C:/prova.p3d", std::ofstream::binary ); `

---

## Comment 7

> Username: foreverneverer  
> Created at: 2021-08-20 11:39:47 UTC  
> Url: https://github.com/boostorg/beast/issues/1911#issuecomment-902632211  

> I declared the ofstream object without specifying that the second parameter of the constructor should be "std::ofstream::binary". By doing that everything was solved :)  
>   
> `std::ofstream outpoot("C:/prova.p3d", std::ofstream::binary );`  
  
Hi, `http::response<http::dynamic_body> resp;` include `header content`, for example:  
```  
HTTP/1.1 200 OK^M  
Accept-Ranges: bytes^M  
Content-Length: 61^M  
Content-Type: text/x-sh; charset=utf-8^M  
Last-Modified: Wed, 18 Aug 2021 03:45:56 GMT^M  
Date: Fri, 20 Aug 2021 11:36:54 GMT^M  
^M  
sudo docker run -it duruo850/ubuntu18.04-python3.6 /bin/bash  
```  
but actually , I only need save body content:  
```  
sudo docker run -it duruo850/ubuntu18.04-python3.6 /bin/bash  
```  
so how can I get the body and save as file?  
  
I try use res.body().data(), but it can't accept the ` outpoot << res.body().data() << std::endl;`  
  
thank you!

---

## Comment 8

> Username: vinniefalco  
> Created at: 2021-08-20 15:48:20 UTC  
> Url: https://github.com/boostorg/beast/issues/1911#issuecomment-902787290  

use `beast::http::file_body`
