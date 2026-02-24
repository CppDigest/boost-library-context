# #2901 - File name too long [Closed]

> Username: forresttales  
> Created at: 2024-07-09 16:41:04 UTC  
> Updated at: 2024-07-24 06:17:40 UTC  
> Closed at: 2024-07-24 06:17:40 UTC  
> Url: https://github.com/boostorg/beast/issues/2901  

Boost 85. Basically, I have this, which works fine for files,  
  
```  
beast::error_code ec;  
http::file_body::value_type body;  
body.open(path.c_str(), beast::file_mode::scan, ec);  
  
if(ec) {  
    return server_error(ec.message());  
}  
  
http::response<http::file_body> res{  
	std::piecewise_construct,  
	std::make_tuple(std::move(body)),  
	std::make_tuple(http::status::ok, req.version())};  
res.set(http::field::server, BOOST_BEAST_VERSION_STRING);  
res.set(http::field::content_type, mime_type(path));  
res.content_length(size);  
res.keep_alive(req.keep_alive());  
return res;  
  
```  
  
The GET request is a very long string (thanks, Microsoft).  
?code=0.AQ0AWolQvVRepkqirmjKlkadwHnH_mfxi7hKjtwHUMNt8L8 ... another half page  
  
The server_error is 'File name too long'.  
  
There is no file to retrieve. The frontend should simply grab the string above and POST it to the backend in JSON.  
  
I have tried,  
  
```  
boost::beast::http::request_parser<http::file_body> parser;  
parser.body_limit(boost::none);  
  
```  
  
As expected, body_limit is not the problem. The body size is 0. And if I bypass the error, I get a dialog to save an empty file. Not what I want ))  
  
How can I extend the file name limit, or do I need something other than   
`http::response<http::file_body>`  
?

---

## Comment 1

> Username: ashtum  
> Created at: 2024-07-09 16:48:24 UTC  
> Url: https://github.com/boostorg/beast/issues/2901#issuecomment-2218213951  

Are you getting the `File name too long` error in this line:  
```  
body.open(path.c_str(), beast::file_mode::scan, ec);  
```  
Where is that path coming from?

---

## Comment 2

> Username: forresttales  
> Created at: 2024-07-09 16:53:08 UTC  
> Url: https://github.com/boostorg/beast/issues/2901#issuecomment-2218225960  

Yes, that is the line. The full path looks like this,  
  
/var/www/mysite.com/myendpoint?code=0.AQ...  
  
It works for other file requests.

---

## Comment 3

> Username: ashtum  
> Created at: 2024-07-09 17:34:06 UTC  
> Url: https://github.com/boostorg/beast/issues/2901#issuecomment-2218292650  

Sorry, I don't quite understand what is the question. The error message `File name too long` indicates that the file name you're trying to open exceeds the maximum length allowed by the operating system.

---

## Comment 4

> Username: forresttales  
> Created at: 2024-07-09 17:42:53 UTC  
> Url: https://github.com/boostorg/beast/issues/2901#issuecomment-2218305532  

Right. That's what it should be. The endpoint is 'microsoft-auth-success', which should not be too long. I know it runs in Nginx. I'll have to take this up with my frontend guy. If I find a solution, I'll post it.

---

## Comment 5

> Username: ashtum  
> Created at: 2024-07-09 17:49:10 UTC  
> Url: https://github.com/boostorg/beast/issues/2901#issuecomment-2218314524  

You didn't mention how you construct the `path` variable. You need to inspect the contents of this variable to understand why it sometimes contains a file name that is too long.

---

## Comment 6

> Username: forresttales  
> Created at: 2024-07-10 11:32:36 UTC  
> Url: https://github.com/boostorg/beast/issues/2901#issuecomment-2220280005  

The path is constructed the usual way,  
`std::string path = path_cat(doc_root, req.target());`  
If the path.size() does not exceed exactly 283, it runs. At 284, "File name too long".  
I am on Debian, not Windows. But when subscribing to its webservice, Microsoft Outlook sends a GET with a string over 1000 characters. As mentioned, Nginx processes it with no problem. But something in Beast limits the path length.

---

## Comment 7

> Username: ashtum  
> Created at: 2024-07-10 11:46:51 UTC  
> Url: https://github.com/boostorg/beast/issues/2901#issuecomment-2220306191  

The maximum file name length in most file systems is 255 characters. You can test this by attempting to create a file manually.

---

## Comment 8

> Username: forresttales  
> Created at: 2024-07-10 12:21:01 UTC  
> Url: https://github.com/boostorg/beast/issues/2901#issuecomment-2220370940  

Thanks. I must be thinking about this wrong. Anyway, there is no file named, "microsoft-auth-success". I am converting a site that worked with Nginx to Beast. (Beast works well with the webhook - Nginx does not.) The solution is to change my frontend/backend design.

---

## Comment 9

> Username: forresttales  
> Created at: 2024-07-13 16:23:29 UTC  
> Url: https://github.com/boostorg/beast/issues/2901#issuecomment-2226988106  

The solution was to simply trim the query parameters from the request target. After which, of course, no file would be found. This is a 'redirect uri', NOT a file. After trimming and avoiding the error, Beast simply chooses /index.html as default. This is perfectly normal behavior as in Apache or Nginx.  
  
The context of the problem was this - Beast as a reverse proxy and the Microsoft Oulook webservice api called from a Single Page Application client browser page. As anyone who has dealt with this before could tell you, this means specifying a 'redirect_uri', such as 'https://mywebiste/my-redirect-page'.  
  
The webservice returns information in a GET request with query parameters over 1000 characters long.  
  
When the website is an SPA, the backend does not need to actually do anything, nor return anything. It simply needs to receive the inevitable and unusual GET without throwing an error. In my case, a React frontend parsed the info with no need of backend response.  
  
So this is not a typical GET scenario. There is no static file to retrieve and no upstream endpoint to process anything, as with a normal GET. In fact, trying to give it an endpoint breaks the SPA frontend.  
  
For this case, the backend needs only be passive, an empty response. But since body.open() thinks it's trying to open a real file (as it should), it sees the query params as just a long file name. Thus, 'File name too long'.  
  
Again, the error is correct. There is no issue with Beast. However, my guess is that configurable servers, like Nginx, trim a ridiculously long and misplaced query under-the-hood before defaulting to /index.html.  
  
So, when coding a Beast example into a reverse proxy, THIS ADDED STEP NEEDS TO BE COMPLETED BY THE PROGRAMMER.
