# #1731 - Basic Authentication example [Open]

> Username: AntonioL  
> Created at: 2019-10-13 15:27:51 UTC  
> Updated at: 2019-12-30 05:13:06 UTC  
> Url: https://github.com/boostorg/beast/issues/1731  

I understand boost::beast does not offer high-level API to increase likelihood of standardisation in future. And I wonder whether the community produced wrappesr around boost::beast for mundane tasks.  
  
I apologise in advance if this does not belong to a github issue, please close it in the case.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2019-10-13 15:29:04 UTC  
> Url: https://github.com/boostorg/beast/issues/1731#issuecomment-541428533  

What high-level APIs did you have in mind?

---

## Comment 2

> Username: AntonioL  
> Created at: 2019-10-13 15:33:30 UTC  
> Url: https://github.com/boostorg/beast/issues/1731#issuecomment-541428957  

Something along the lines of Python requests for example.  
  
I understand that such a higher-level API is not in the business to belong to boost::beast, this issue is more for double checking whether any other independent developer made an effort in building something similar. On github I cannot seem to find any.

---

## Comment 3

> Username: vinniefalco  
> Created at: 2019-10-13 15:34:39 UTC  
> Url: https://github.com/boostorg/beast/issues/1731#issuecomment-541429060  

> Something along the lines of Python requests for example.  
  
Yeah that would be nice!

---

## Comment 4

> Username: gocarlos  
> Created at: 2019-11-29 17:10:37 UTC  
> Url: https://github.com/boostorg/beast/issues/1731#issuecomment-559847323  

Like this: https://github.com/expresscpp/expresscpp ?

---

## Comment 5

> Username: vinniefalco  
> Created at: 2019-11-29 18:10:21 UTC  
> Url: https://github.com/boostorg/beast/issues/1731#issuecomment-559857950  

Nice!! Consider adding your project to this page?  
  
https://github.com/boostorg/beast/wiki/Companies-and-Individuals-Using-Beast

---

## Comment 6

> Username: AntonioL  
> Created at: 2019-12-09 20:35:58 UTC  
> Url: https://github.com/boostorg/beast/issues/1731#issuecomment-563426466  

Thank you @gocarlos   
  
I hope that this thread will receive some few more entries!

---

## Comment 7

> Username: vinniefalco  
> Created at: 2019-12-09 21:50:41 UTC  
> Url: https://github.com/boostorg/beast/issues/1731#issuecomment-563456240  

Working on it: https://github.com/vinniefalco/url

---

## Comment 8

> Username: AntonioL  
> Created at: 2019-12-10 00:03:14 UTC  
> Url: https://github.com/boostorg/beast/issues/1731#issuecomment-563496794  

>   
>   
> Working on it: https://github.com/vinniefalco/url  
  
What is the plan? First, you design and implement. a facility which can represent an URL,. Second, you plug some high-level API inside beast taking the aformentioned representation?  
  
Is my understanding correct?

---

## Comment 9

> Username: vinniefalco  
> Created at: 2019-12-10 00:07:25 UTC  
> Url: https://github.com/boostorg/beast/issues/1731#issuecomment-563497962  

> What is the plan?   
  
Yes, the general idea is to work in layers. Asio is the bottom layer, the networking. Then we have Beast which implements low level HTTP and WebSocket. If we want to have a high-level HTTP client like Python Requests (which I think is a good goal!) then we need some other components:  
  
* URL Library  
* Library to get proxy configuration from the OS  
* SSL certificate verification lib  
* Refactored ZLib (to handle gzip and deflate Transfer-Encoding)  
* Basic Authentication module  
* Cookies module  
* OAuth2 library  
* "Secure" socket (i.e. a high-level boost::asio::ssl::stream)  
  
And probably we will need a couple of other things along the way. I am figuring out how to get all of this going. If anyone wants to volunteer, I'm more than happy to have their help!

---

## Comment 10

> Username: swornim1  
> Created at: 2019-12-21 03:51:14 UTC  
> Url: https://github.com/boostorg/beast/issues/1731#issuecomment-568149475  

I am willing to contribute to this project. I am new to open source contribution but I am willing to put in time and effort to make this happen. Please provide some guideline on getting started. Thanks!

---

## Comment 11

> Username: vinniefalco  
> Created at: 2019-12-21 04:00:42 UTC  
> Url: https://github.com/boostorg/beast/issues/1731#issuecomment-568149960  

Any idea what Basic Authentication might look like? Or cookies?

---

## Comment 12

> Username: swornim1  
> Created at: 2019-12-22 03:49:22 UTC  
> Url: https://github.com/boostorg/beast/issues/1731#issuecomment-568230442  

I have an understanding of how basic authentication and cookies works, but I am not sure how the design and implementation will work yet? Do I have to turn in a proposal with the design and implementation details to move forward? Thanks!

---

## Comment 13

> Username: vinniefalco  
> Created at: 2019-12-22 04:17:23 UTC  
> Url: https://github.com/boostorg/beast/issues/1731#issuecomment-568231401  

Well, what I like to do is make a prototype of what the library would look like if it was used. Where I have some interfaces, but no implementatation, and then make a list of what use-cases I want to support. Then for each use-case I make a proposed syntax with sample code that shows what the syntax is like. Here's an example of that design process:  
https://github.com/boostorg/beast/issues/154

---

## Comment 14

> Username: swornim1  
> Created at: 2019-12-25 03:56:23 UTC  
> Updated at: 2019-12-25 04:00:25 UTC  
> Url: https://github.com/boostorg/beast/issues/1731#issuecomment-568832876  

basic_authentication_header can be used for constructing the basic authentication header from the provided username and password. encoded_header() returns the header after base64 encoding the username and password  
```  
    class basic_authentication_header  
    {  
	private:  
		        string header_before_encoding;  
		        string header_after_encoding;  
        public:  
		        basic_authentication_header(string username, string password);  
		        void encode_header();  
		        string encoded_header();  
    };  
  
```  
basic_authentication handles the authentication on server side. The path of the file containing the username and password is initalized from the constructor, and later the authenticate function compares the username and password from the header file with the username and password in the files, and determine the validity of the requestor.  
  
```  
class basic_authentication  
{  
	private:  
			string username_passwd_file_path;  
			  
	public:  
			 basic_authentication(string username_passwd_file_path);  
			 bool authenticate(string header);  
};  
  
```  
Above is the prototype and a brief description of the functions. Please recommend required modifications. Is this the right way to approach the design process? Thank you!

---

## Comment 15

> Username: vinniefalco  
> Created at: 2019-12-25 15:40:58 UTC  
> Updated at: 2019-12-25 15:41:52 UTC  
> Url: https://github.com/boostorg/beast/issues/1731#issuecomment-568910071  

There's a file? (Yes, this is a good design approach)

---

## Comment 16

> Username: swornim1  
> Created at: 2019-12-26 22:35:45 UTC  
> Url: https://github.com/boostorg/beast/issues/1731#issuecomment-569144005  

I was thinking of a file which stores the username and password list like a .htpasswd file, which can be refered to while validating the basic authentication request.

---

## Comment 17

> Username: vinniefalco  
> Created at: 2019-12-27 02:48:00 UTC  
> Url: https://github.com/boostorg/beast/issues/1731#issuecomment-569172129  

I think having the file is a bit _too_ high level, we need something in between first. Maybe the API can have the caller supply an invocable object that performs the check for the credentials? And the example can have something that uses .htaccess (but this example code won't be an official, public API)

---

## Comment 18

> Username: swornim1  
> Created at: 2019-12-28 03:15:09 UTC  
> Url: https://github.com/boostorg/beast/issues/1731#issuecomment-569380083  

![basic_auth](https://user-images.githubusercontent.com/21980646/71537279-5a897f80-28df-11ea-8045-3d0390b08296.PNG)  
  
I took the above image from https://developer.mozilla.org/en-US/docs/Web/HTTP/Authentication website  
  
As I was reading more about the basic authentication and through boost::beast::message and boost::beast:field library, I got more confused about implementing the basic authentication module. Initially I was thinking of a library that would help the users create the basic authentication header with username and password on the client side, and a function which would allow users to validate the  basic authentication request on server side. Since the beast::message library allows the functionality to develop the basic authentication header for the user, I felt like I was going in the wrong direction. Could you please give some suggestion about what we are trying to accomplish with the basic authentication module? Thanks in advance!

---

## Comment 19

> Username: vinniefalco  
> Created at: 2019-12-28 16:03:28 UTC  
> Url: https://github.com/boostorg/beast/issues/1731#issuecomment-569428462  

Maybe first just write a simple example client program that does basic authentication against some well known server?

---

## Comment 20

> Username: swornim1  
> Created at: 2019-12-30 03:41:40 UTC  
> Updated at: 2019-12-30 03:43:11 UTC  
> Url: https://github.com/boostorg/beast/issues/1731#issuecomment-569574062  

For the server, I did an apache2 server setup on SUSE Linux Enterprise server running on AWS ec2 instance. I have setup two targets one with basic authentication enabled, and the other without the basic authentication.   
  
Link for the target without basic authentication: 52.15.155.121/index.html  
  
Link for the target with the basic authentication:  52.15.155.121/basic_auth_test/index.html  
  
The credential for logging into the link with basic_auth_test are provided below:username: test  
Password: test  
  
I verified from a web browser that the basic authentication mechanism is setup and working properly on the server.  
  
For the client, I wrote the basic_auth_client by referring to the   
libs/beast/example/http/client/sync/http_client_sync.cpp example provided in boost::beast documentation. The client is able to fetch the request and recognize whether the response has basic authentication or not. After recognizing that the server requires authorization, the client updates the authorization field of the request header with the required value which in our case is "Basic test:test", after base64 encoding turns out to be "Basic  dGVzdDp0ZXN0". The server checks the header and verifies the request and responds with the required document.   
  
The link for the project is listed below:  
https://github.com/swornim1/test_server.git
