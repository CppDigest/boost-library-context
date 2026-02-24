# #2522 - Head request, got Error: bad method [beast.http:12] [Closed]

> Username: fynv  
> Created at: 2022-09-30 09:02:28 UTC  
> Updated at: 2022-09-30 09:41:23 UTC  
> Closed at: 2022-09-30 09:41:23 UTC  
> Url: https://github.com/boostorg/beast/issues/2522  

Version:  BOOST_BEAST_VERSION 330  
(Windows, MSVC, Boost from NuGet,  version:  1.80.0)  
  
## Steps necessary to reproduce the problem  
Not even sure this is a bug.  
  
Basicaly , trying to make a head request following https://www.boost.org/doc/libs/master/libs/beast/doc/html/beast/more_examples/head_request_client.html  
and got:  
Error: bad method [beast.http:12]   
[HttpClient.zip](https://github.com/boostorg/beast/files/9682555/HttpClient.zip)  
  
Most relevant code:  
  
![image](https://user-images.githubusercontent.com/8346412/193234536-1a75c138-0dd9-4b38-a25d-70d7c721cdea.png)

---

## Comment 1

> Username: fynv  
> Created at: 2022-09-30 09:41:23 UTC  
> Url: https://github.com/boostorg/beast/issues/2522#issuecomment-1263347980  

Sorry, just a typo, request_parser-> response_parser.  
Closing
