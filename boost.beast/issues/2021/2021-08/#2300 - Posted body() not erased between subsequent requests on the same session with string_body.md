# #2300 - Posted body() not erased between subsequent requests on the same session with string_body [Closed]

> Username: rerdavies  
> Created at: 2021-08-22 23:48:25 UTC  
> Updated at: 2021-08-30 20:44:50 UTC  
> Closed at: 2021-08-30 20:44:50 UTC  
> Url: https://github.com/boostorg/beast/issues/2300  

### Version of Beast  
  
167  
  
### Steps necessary to reproduce the problem  
  
Using code based off advanced async server, (with modifications to allow routing of requests).  
  
POST two consecutive requests to the same session, with non-empty body in the request (application/json data, in this particular case).  (requests are string_body type, as in the sample code).  
  
Log the contents of req.body(). The body() of the 2nd request consists of the body() of the first and second requests concatenated. This continues for subsequent requests. The contents of the body() are not erased between subsequent posts on the same session.   
  
Posts are arriving as consecutive requests. I'm not sure if intervening non-post operations make the problem go away; but setting res.keep_alive(false) makes the problem go away.   
  
Is there something I need to do to the requests to clear the body()? The code is fairly heavily modified (CORS support, access to the requester's address for sessions, a scheme for routing requests, multi-writer wrappers and write-queues for websockets only, &c), but I have reviewed against the sample code pretty carefully.  
  
The following code added to handle_request should repro the problem:  
  
```  
            if (req.method() == http::verb::post)  
            {  
  
                http::response<http::string_body> res{http::status::ok, req.version()};  
                // res.set(http::field::server, BOOST_BEAST_VERSION_STRING);   
               res.set(http::field::mime_type,"text/plain";  
  
               // echo the posted data.  
                std::string response = req.body(); // just echo back the posted data.  
                res.body() = response;  
  
               res.set(http::field::content_length, response.length());   
               res.set(http::field::content_type, "application/json");  
  
                requestHandler->post_response(request_uri, req, res, ec);  
                res.keep_alive(req.keep_alive());   
  
                res.prepare_payload();  
                return send(std::move(res));  
       }  
  
```  
Embed the following javascript code in a browser page to trigger the issue. (Redacted from typescript code, so please excuse if there are trivial syntax errors).  
  
```  
                let url = "http://MYSERVER.ADDRESS/test'  
  
                // Make first POST request.  
                fetch(  
                    url,  
                    {  
                        method: "POST",  
                        body: ' "FIRST_REQUEST_DATA" ',  
                        headers: {  
                            'Content-Type': 'application/json',  
                        },  
                    }  
                )  
                    .then((response) => {  
                        if (!response.ok) {  
                            console.log("Upload failed. " + response.statusText);  
                            return;  
                           }                      
                           return response.json(); // read the response asynchronously  
                    })  
                    .then((json) => {  
                        console.log("First response received.");  
                        // Make 2nd POST request, which will be on the same http session as long as it hasn't timed out.  
                        return fetch(url,  
                            { method: "POST",  
                             body: ' "SECOND_REQUEST_DATA" ',  
                             headers: {  
                                 "Content-Type': 'application/json'  
                             }  
                       );  
                   )  
                   .then((response) => {   
                            if (!response.ok) {  
                                console.log("Upload failed. " + response.statusText);  
                                return;  
                              }                      
                             // WILL FAIL HERE, BECAUSE THE RESPONSE WILL BE  ' "FIRST_REQUEST_DATA" "SECOND_REQUEST_DATA" '.  
                             // which is not well-formed json.  
                              return response.json();  
                       }  
                    )  
                   .then((json) => {  
                       console.log("Second response received.");  
                   })  
                    .catch((error) => {  
                        console.log("Upload failed. " + error);  
                    })  
  
  
```  
  
If I *had* to guess, I would guess that string_body::reader::init should call body_.resize(0) at string_body.hpp:93, since a call to std::string::reserve does not reset the length of the string. But the limited debugging I'm able to do doesn't really bear that out. I can't figure out where the extra data comes from. I think my copy of gdb isn't fast enough to debug through the code without causing my browser (Chrome) to time-out the http session, in which case the issue doesn't manifest on the 2nd request.  
.   
### All relevant compiler information  
g++ (Raspbian 8.3.0-6+rpi1) 8.3.0  (latest version on Raspbian)  
Compiled with C++ 17 options.  
Linux patchbox 5.10.52-v7l+ #1441 SMP Tue Aug 3 18:11:56 BST 2021 armv7l GNU/Linux  
ARM 32-bit.  
  
It compiles fine.

---

## Comment 1

> Username: madmongo1  
> Created at: 2021-08-23 03:55:27 UTC  
> Url: https://github.com/boostorg/beast/issues/2300#issuecomment-903421380  

Request objects are not designed to be re-used between requests.  
  
either:  
  
req = http::request<http::string_body>();  
  
or  
  
req.clear();  
req.body().clear()  
  
  
Between requests.

---

## Comment 2

> Username: rerdavies  
> Created at: 2021-08-30 20:44:50 UTC  
> Url: https://github.com/boostorg/beast/issues/2300#issuecomment-908681212  

Problem solved.  
  
Somehow or another, I seem to have lost (or never had) the first three lines of do_read() from the sample servers I started with.  
  
```  
void  
do_read() {  
      // make the request empty before reading  
        // otherwise the operation behavior is undefined.  
        req_ = {};  
```
