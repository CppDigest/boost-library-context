# #2275 - Facing issue with http request with non English characters [Closed]

> Username: jigarthacker24  
> Created at: 2021-07-01 21:42:25 UTC  
> Updated at: 2021-07-03 04:39:19 UTC  
> Closed at: 2021-07-03 04:39:19 UTC  
> Url: https://github.com/boostorg/beast/issues/2275  

Hi,  
  
I am facing issue with non-English characters as a part of POST request URL target.  
URL: host/target  
  
  
std::string target(URL_TARGET);  
  
Steps:  
1. resolver : host, "443"  
2. connect :  
3. ssl handshake  
  
  
boost::beast::http::request<boost::beast::http::string_body> req{ POST, target.c_str(), HTTP_VERSION };  
req.set(beast::http::field::content_type, "application/json; charset=utf-8");  
  
4. write  
5. read  
  
It returns 400 invalid request error.  
  
It works fine if requst url target is English characters only.

---

## Comment 1

> Username: madmongo1  
> Created at: 2021-07-01 22:09:37 UTC  
> Url: https://github.com/boostorg/beast/issues/2275#issuecomment-872580669  

As far as I am aware, HTTP URIs only permit the letters `a-zA-Z`, numbers `0-9`, `$-_.+` and `!*'(),`  
  
https://datatracker.ietf.org/doc/html/rfc1738#section-5

---

## Comment 2

> Username: jigarthacker24  
> Created at: 2021-07-02 06:32:55 UTC  
> Updated at: 2021-07-02 06:33:14 UTC  
> Url: https://github.com/boostorg/beast/issues/2275#issuecomment-872754322  

For more info.  
I am trying to create an upload session using MS graph api: POST /drives/{driveId}/items/{itemId or path}/createUploadSession  
  
where path is file path and contains non English characters as well.  
  
https://docs.microsoft.com/en-us/graph/api/driveitem-createuploadsession?view=graph-rest-1.0#create-an-upload-session  
  
Note: It works with the manual test tool like Postman with non English characters.

---

## Comment 3

> Username: madmongo1  
> Created at: 2021-07-02 06:35:47 UTC  
> Url: https://github.com/boostorg/beast/issues/2275#issuecomment-872755761  

It might be worth using wire shark to see exactly what postman is sending.  
There may well be some escaping you need to do when creating the request.

---

## Comment 4

> Username: jigarthacker24  
> Created at: 2021-07-02 14:43:29 UTC  
> Url: https://github.com/boostorg/beast/issues/2275#issuecomment-873051283  

Hi @madmongo1   
Thank you for your inputs.. Checked the URL details that were being sent via postman on postman console output.  
The encoding was the issue my side. I had to encode the target part of my URL to make it work.  
  
e.g. Had to convert target "/drives/{driveId}/items/root:/再見.zip:/createUploadSession" to "/drives/{driveId}/items/root:/%E5%86%8D%E8%A6%8B.zip:/createUploadSession"  
  
This solved the issue.  
  
Thank you.  
  
ref: https://datatracker.ietf.org/doc/html/rfc1738#section-2.2

---

## Comment 5

> Username: madmongo1  
> Created at: 2021-07-02 15:27:14 UTC  
> Url: https://github.com/boostorg/beast/issues/2275#issuecomment-873081561  

Great news! May I close this issue?
