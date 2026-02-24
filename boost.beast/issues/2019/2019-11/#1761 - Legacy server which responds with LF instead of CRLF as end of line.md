# #1761 - Legacy server which responds with LF instead of CRLF as end of line [Closed]

> Username: enricodetoma  
> Created at: 2019-11-09 13:40:46 UTC  
> Updated at: 2019-11-09 13:51:56 UTC  
> Closed at: 2019-11-09 13:51:56 UTC  
> Url: https://github.com/boostorg/beast/issues/1761  

### Version of Beast  
  
266  
  
### Steps necessary to reproduce the problem  
  
Need a legacy local server which responds with LF instead of CRLF terminator.  
  
### All relevant compiler information  
  
Microsoft Visual Studio 2017 v 15.9.17  
  
### Issue details  
  
Unfortunately I have a legacy local http server (a Java application) which responds with LF instead of CRLF as end of line.  
  
Rfc7230, section 3.5 (Message Parsing Robustness) says:  
  
> Although the line terminator for the start-line and header fields is the sequence CRLF, a recipient MAY recognize a single LF as a line terminator and ignore any preceding CR.  
  
In `detail/basic_parser.ipp`, line 277 I found this code:  
```  
#if 0  
    // VFALCO This allows `\n` by itself  
    //        to terminate a line  
    else if(*p == '\n')  
    {  
        token_last = p;  
        ++p;  
    }  
#endif  
  
```  
I tried to uncomment it, but it still can't get beast to parse the response (when the legacy local http server closes the connection, I found that `state_ == state::start_line` while debugging).  
  
This is the trace of the response I get from Wireshark:  
```  
HTTP/1.1 200 OK\n  
Cache-Control: no-cache\n  
Pragma: no-cache\n  
Content-Type: text/plain\n  
\n  
1  
```  
  
Is there a way to manage the LF as a line terminator in a "tolerant" way with beast?

---

## Comment 1

> Username: enricodetoma  
> Created at: 2019-11-09 13:43:03 UTC  
> Url: https://github.com/boostorg/beast/issues/1761#issuecomment-552101589  

For completeness, this is the http request:  
  
```  
GET /?methodName=checkValidation HTTP/1.1  
Host: 127.0.0.1  
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/70.0.3538.77 Safari/537.36  
Connection: close  
```

---

## Comment 2

> Username: vinniefalco  
> Created at: 2019-11-09 13:44:04 UTC  
> Url: https://github.com/boostorg/beast/issues/1761#issuecomment-552101660  

If you want to do that, you have to preprocess the incoming data and make it the message compliant. Beast does not support non-compliant HTTP.

---

## Comment 3

> Username: enricodetoma  
> Created at: 2019-11-09 13:51:56 UTC  
> Url: https://github.com/boostorg/beast/issues/1761#issuecomment-552102328  

Ok, thank you, I'll try to use the `parser` (https://www.boost.org/doc/libs/develop/libs/beast/doc/html/beast/ref/boost__beast__http__parser.html) on preprocessed data.
