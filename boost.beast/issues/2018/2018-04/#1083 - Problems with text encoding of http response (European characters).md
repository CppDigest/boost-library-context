# #1083 - Problems with text encoding of http response (European characters) [Closed]

> Username: runesig  
> Created at: 2018-04-01 09:02:35 UTC  
> Updated at: 2018-04-04 14:40:19 UTC  
> Closed at: 2018-04-03 14:56:18 UTC  
> Url: https://github.com/boostorg/beast/issues/1083  

I am trying out the Beast library but I have a couple of issues. I am struggeling to get the response from the server in correct format under Windows. I have tried the examples that follows with the library with a test server, where:  
  
http::request<http::string_body> req_;  
http::response<http::string_body> res_;  
  
I am using JSON and a stringstream like this:  
  
std::stringstream message_string;  
write_json(message_string, message);  
  
and:  
  
req_.body() = message_string.str();  
  
If I try to post a name like "René Andersson" the request is correct and the server gets the characters in the correct format.   
  
BUT, the response (which is the exact same text) is not correct. I pick out the body from the response like this:  
  
std::stringstream message_string(response.body());  
  
The name gets changed to René = Renï¿½   
  
Can you please help tell me what I am doing wrong?

---

## Comment 1

> Username: carolinebeltran  
> Created at: 2018-04-01 17:06:05 UTC  
> Url: https://github.com/boostorg/beast/issues/1083#issuecomment-377800963  

A quick suggestion, I would suggest setting your charset to utf-8.  For example:  
  
`res.set(http::field::content_type, "application/json; charset=utf-8");`  
  
I do this and can display latin, Chinese, Russian, etc. characters just fine for my web application, reading from and writing to text or JSON files.  Although UTF-8 has no problems with Windows, trying to print multi-byte UTF-8 characters (such as Russian or Chinese for example) to a Windows command prompt will result in output similar to what you printed in your post.

---

## Comment 2

> Username: carolinebeltran  
> Created at: 2018-04-01 17:07:07 UTC  
> Updated at: 2018-04-01 17:09:27 UTC  
> Url: https://github.com/boostorg/beast/issues/1083#issuecomment-377801039  

Of course, if you are going to display as HTML, you would use:  
  
`res.set(http::field::content_type, "text/html; charset=utf-8");`

---

## Comment 3

> Username: runesig  
> Created at: 2018-04-01 17:28:11 UTC  
> Url: https://github.com/boostorg/beast/issues/1083#issuecomment-377802557  

Thanks, for your suggestion. I had not tried to set it on the response until now, but I just did and unfortunately the result is the same. I am not printing the value to a console but using it for a search.  And, of course the search is failing because of this. Also, the debugger shows me the same result. I am using Visual Studio, changing character set from unicode to multi byte does not do any difference either.

---

## Comment 4

> Username: vinniefalco  
> Created at: 2018-04-01 18:01:52 UTC  
> Url: https://github.com/boostorg/beast/issues/1083#issuecomment-377804963  

>Can you please help tell me what I am doing wrong?  
  
Can you please post a complete program?

---

## Comment 5

> Username: runesig  
> Created at: 2018-04-01 19:04:13 UTC  
> Url: https://github.com/boostorg/beast/issues/1083#issuecomment-377808980  

Btw, this same project is what I used to test the async issue.

---

## Comment 6

> Username: vinniefalco  
> Created at: 2018-04-01 19:46:58 UTC  
> Url: https://github.com/boostorg/beast/issues/1083#issuecomment-377811870  

The program looks okay, although it could have been trimmed to just the parts needed to reproduce the defect. Is the server sending back an incorrect result?

---

## Comment 7

> Username: runesig  
> Created at: 2018-04-01 19:51:10 UTC  
> Url: https://github.com/boostorg/beast/issues/1083#issuecomment-377812115  

Yes, I am sorry, I just sent you my test as was. The server is responding correctly according to Postman. The string in the response object is the problem.

---

## Comment 8

> Username: vinniefalco  
> Created at: 2018-04-01 20:24:46 UTC  
> Url: https://github.com/boostorg/beast/issues/1083#issuecomment-377814160  

Beast doesn't perform any translation of the message body, so I don't see how it is possible that there is a bug in the library causing this.

---

## Comment 9

> Username: runesig  
> Created at: 2018-04-02 08:06:37 UTC  
> Url: https://github.com/boostorg/beast/issues/1083#issuecomment-377880398  

So that means that it’s either me who have done something wrong, or an issue with Asio?

---

## Comment 10

> Username: vinniefalco  
> Created at: 2018-04-02 11:14:52 UTC  
> Url: https://github.com/boostorg/beast/issues/1083#issuecomment-377909277  

There's no way that Asio is causing this, it has to be either in your program or the server.

---

## Comment 11

> Username: runesig  
> Created at: 2018-04-02 14:27:17 UTC  
> Url: https://github.com/boostorg/beast/issues/1083#issuecomment-377941012  

Yes you are right. I have done some more research. I tried another library based on libcurl (Cpr) and got the exact same issue. I also made a C# program where I used HttpClient and PostAsync where everything was correct. So it is not the server (I already knew that), it is some kind of a c++ windows character set/ encoding issue.

---

## Comment 12

> Username: vinniefalco  
> Created at: 2018-04-02 15:36:07 UTC  
> Url: https://github.com/boostorg/beast/issues/1083#issuecomment-377957378  

Maybe you need to `imbue` the `std::stringstream` with the "C" locale?

---

## Comment 13

> Username: runesig  
> Created at: 2018-04-03 14:56:18 UTC  
> Url: https://github.com/boostorg/beast/issues/1083#issuecomment-378279751  

Thanks for your suggestion, but I could not get it to work. But since this is not directly related Beast I close this issue and I'll ask the question on Stackoverflow too. Thanks!

---

## Comment 14

> Username: runesig  
> Created at: 2018-04-04 05:16:02 UTC  
> Url: https://github.com/boostorg/beast/issues/1083#issuecomment-378482724  

Sometimes, its so obvious when you finally figure out what the problem really was... I have been looking at the wrong place all the time. But thats how things work from time to time. The string with the European characters I was sending was in iso-8859-1 format, not utf-8. I had to convert it first, then post it, and then convert the response back. I used the boost library like this   
  
std::string output = boost::locale::conv::to_utf<char>(someString, loc);  
  
By setting "content_type" to "charset=utf-8" does of course not magically convert the string, it just tells the server which format the string you are sending is in.   
  
In C# we can set :  
var content = new StringContent(jsonObject.ToString(), Encoding.UTF8, "application/json");  
The Encoding.UTF8 parameter does convert the text and I guess that is what fooled me when I compared the results.  
  
Thanks @carolinebeltran and @vinniefalco for looking into this. Appreciate it!

---

## Comment 15

> Username: vinniefalco  
> Created at: 2018-04-04 14:40:19 UTC  
> Url: https://github.com/boostorg/beast/issues/1083#issuecomment-378623820  

Very glad to see that you got it resolved! Thanks for letting us know.
