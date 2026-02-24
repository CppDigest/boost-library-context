# #2165 - Unable to accept request with specific filetypes in the body [Closed]

> Username: LucAngevare  
> Created at: 2021-02-15 08:25:51 UTC  
> Updated at: 2021-04-21 16:37:19 UTC  
> Closed at: 2021-04-21 16:37:19 UTC  
> Url: https://github.com/boostorg/beast/issues/2165  

So I had a bit of an issue and I'm already fairly sure that this is just something I don't understand rather than a bug in the library.  
<hr>  
For some reason, specific files aren't being accepted by the Boost::Beast server. I can add files in the body and with most files, it just keeps loading until I get a "Connection reset" error (using Postman). Sending images works, but any other filetype doesn't. For clarification:  
  
* Filesize isn't the problem  
   I have tested this with files of 10MB and files of 1 byte, both show the same results consistently  
* It's not a problem of binary vs encoded (utf-8/ASCII/etc) files  
  I have tested whether the same problem occurs on actual binary files (executables) and that didn't change the results whatsoever either  
  
---  
The code:  
  
I'm not using any insane functions or any 3rd-party libraries to help with this. I'm just using the native `boost::beast::buffers_to_string(req.body().data())` function and outputting that back into the response body. With images this gives the expected output, it returns the raw data of the image, with any other filetypes (tested with txt files, zip files and executables) it doesn't hive any response at all and Postman doesn't recognize it accepting the request or giving a response.  
  
libboost-dev-all version: focal 1.71.0.0ubuntu2 amd64  
<pre>  
std::string req_body = boost::beast::buffers_to_string(req.body().data());  
beast::ostream(res.body()) << req_body;</pre>  
  
Thanks in advance for helping and taking the time to read this!

---

## Comment 1

> Username: madmongo1  
> Created at: 2021-02-15 08:29:27 UTC  
> Url: https://github.com/boostorg/beast/issues/2165#issuecomment-779049380  

Hi @LucAngevare ,  
  
Which server do you mean? Are you able to provide me with enough information to perform a reproduceable test my end?

---

## Comment 2

> Username: LucAngevare  
> Created at: 2021-02-15 08:30:46 UTC  
> Url: https://github.com/boostorg/beast/issues/2165#issuecomment-779050312  

Yeah sure, let me setup a repository up with my code, the necessary libraries and some test files

---

## Comment 3

> Username: LucAngevare  
> Created at: 2021-02-15 08:42:07 UTC  
> Url: https://github.com/boostorg/beast/issues/2165#issuecomment-779058492  

@madmongo1   
The repository is visible [here](https://github.com/LucAngevare/test-boost-file-retrieval), the test files are test.txt, test.zip, test.png and real-world-test.zip. I used Postman to add any number of those files to the body and send those to the code, the webserver/API (whatever you want to call it) made in `Boost::Beast`. Thanks!

---

## Comment 4

> Username: madmongo1  
> Created at: 2021-02-15 08:45:03 UTC  
> Url: https://github.com/boostorg/beast/issues/2165#issuecomment-779060442  

Great, I’ll take a look today

---

## Comment 5

> Username: madmongo1  
> Created at: 2021-02-15 10:01:53 UTC  
> Url: https://github.com/boostorg/beast/issues/2165#issuecomment-779108301  

Could this be relevant?  
```  
/home/rhodges/github/LucAngevare/test-boost-file-retrieval/main.cpp:90:60: warning: character constant too long for its type  
   90 |                         res.set(http::field::content_type, 'application/json');  
      |                                                            ^~~~~~~~~~~~~~~~~~  
In file included from /usr/include/boost/bind.hpp:30,  
```

---

## Comment 6

> Username: LucAngevare  
> Created at: 2021-02-15 10:11:26 UTC  
> Url: https://github.com/boostorg/beast/issues/2165#issuecomment-779114183  

I wouldn't think so, that's just a warning and changing that back to `text/plain` gives the same result, I just added that because I still need to test how different browsers respond to JSON values shoved through plain text or other content types and see what's the best one for recognizing JSON.

---

## Comment 7

> Username: madmongo1  
> Created at: 2021-02-15 10:15:10 UTC  
> Url: https://github.com/boostorg/beast/issues/2165#issuecomment-779116408  

Note that this fails to compile with latest beast because of the invalid type conversion between char and string_view.  
  
Which of the files should I send to the server, and with which headers set?

---

## Comment 8

> Username: madmongo1  
> Created at: 2021-02-15 10:19:42 UTC  
> Url: https://github.com/boostorg/beast/issues/2165#issuecomment-779119104  

ok, so posting a file to the server results in bad_request.  
  
This is not surprising. Anything other than a GET is rejected.  
  
```  
            void process_request() {  
                res.version(req.version());  
                res.keep_alive(false);  
  
                switch(req.method()) {  
                    case http::verb::get:  
                    res.result(http::status::ok);  
                    res.set(http::field::server, "Beast");  
                    create_response();  
                    break;  
  
                    default:  
                    res.result(http::status::bad_request);  
                    res.set(http::field::content_type, "text/plain");  
                    beast::ostream(res.body())  
                    << "Invalid request-method '"  
                    << std::string(req.method_string())  
                    << "'";  
                    break;  
                }  
  
                write_response();  
            }  
```  
  
What am I supposed to be testing here?

---

## Comment 9

> Username: LucAngevare  
> Created at: 2021-02-15 10:23:01 UTC  
> Updated at: 2021-02-15 10:25:21 UTC  
> Url: https://github.com/boostorg/beast/issues/2165#issuecomment-779121107  

> Note that this fails to compile with latest beast because of the invalid type conversion between char and string_view.  
  
Oh I didn't notice that, I haven't really tested it on any other versions though to be honest. I wonder if that's what's causing this issue  
  
> Which of the files should I send to the server, and with which headers set?  
  
The eventual necessity is to send a .zip file like real-world-test.zip, without any changes to the headers but with a Name, Description and File key/value in the form-data body. Screenshots of my setup are included [here](https://imgur.com/a/eg60iZF)  
  
> This is not surprising. Anything other than a GET is rejected.  
  
So would that mean that theoretically this issue should be resolved if I could change the server to work with a POST method?  
  
> What am I supposed to be testing here?  
  
I'm not sure, I don't understand what I would need to change in order for it to accept files and why there's a difference between images and any other filetype here.

---

## Comment 10

> Username: madmongo1  
> Created at: 2021-02-15 10:31:55 UTC  
> Updated at: 2021-02-15 10:32:07 UTC  
> Url: https://github.com/boostorg/beast/issues/2165#issuecomment-779126370  

```  
$ curl -v  --data-binary real-world-test.zip  --get http://127.0.0.1:8080/give/bongo   
*   Trying 127.0.0.1:8080...  
* Connected to 127.0.0.1 (127.0.0.1) port 8080 (#0)  
> GET /give/bongo?real-world-test.zip HTTP/1.1  
> Host: 127.0.0.1:8080  
> User-Agent: curl/7.71.1  
> Accept: */*  
>   
* Mark bundle as not supporting multiuse  
< HTTP/1.1 200 OK  
< Connection: close  
< Server: Beast  
< Content-Length: 77  
<   
Update done! Your package is available at: [IP/hostname]:[Port*?]/get/[name]  
* Closing connection 0  
```

---

## Comment 11

> Username: LucAngevare  
> Created at: 2021-02-15 10:36:56 UTC  
> Url: https://github.com/boostorg/beast/issues/2165#issuecomment-779129382  

Wait... was this whole issue just because of Postman? If so, my sincere apologies for wasting your time 😓

---

## Comment 12

> Username: madmongo1  
> Created at: 2021-02-15 10:39:18 UTC  
> Url: https://github.com/boostorg/beast/issues/2165#issuecomment-779130904  

> > Which of the files should I send to the server, and with which headers set?  
>   
> The eventual necessity is to send a .zip file like real-world-test.zip, without any changes to the headers but with a Name, Description and File key/value in the form-data body. Screenshots of my setup are included [here](https://imgur.com/a/eg60iZF)  
  
This is an application-level concern and not something on which I can comment meaningfully.  
  
> So would that mean that theoretically this issue should be resolved if I could change the server to work with a POST method?  
  
Accepting files is normally done via POST. It would be (extremely) unusual to use a GET request for this.  
  
> I'm not sure, I don't understand what I would need to change in order for it to accept files and why there's a difference between images and any other filetype here.  
  
I am guessing that your podman project is injecting headers based on file type?  
This is very much an application level concern.  
As far as HTTP is concerned the body of a request is just a bunch of bytes. Applications infer the meaning of those bytes on the basis of the URL, headers etc.

---

## Comment 13

> Username: madmongo1  
> Created at: 2021-02-15 10:40:03 UTC  
> Url: https://github.com/boostorg/beast/issues/2165#issuecomment-779131370  

> Wait... was this whole issue just because of Postman? If so, my sincere apologies for wasting your time  
  
LOL! I never use these stupid GUI-based tools. They try to do clever stuff behind your back.

---

## Comment 14

> Username: LucAngevare  
> Created at: 2021-02-15 10:45:07 UTC  
> Url: https://github.com/boostorg/beast/issues/2165#issuecomment-779134294  

Damn that was one of the only things I trusted that _couldn't_ have been the issue haha, thanks so much for all the help and advice and sorry for not troubleshooting the issue further to check whether the issue was actually with the server or if it was with the client hahaha, what a stupid issue this was.  
  
Thanks again for the help!

---

## Comment 15

> Username: LucAngevare  
> Created at: 2021-02-15 12:10:32 UTC  
> Updated at: 2021-02-15 12:13:41 UTC  
> Url: https://github.com/boostorg/beast/issues/2165#issuecomment-779181641  

@madmongo1   
Right so I believe I jumped the gun with closing this issue before really testing it. I'm not going to reopen it again however as I believe this is just me being kinda dumb again haha  
  
I tested out the curl command you sent and it occurred to me that it didn't actually send back any of the body form-data (which it did before but it could be that I changed some stuff which made it stop working, don't remember) as it should with the line `beast::ostream(res.body()) << "[...]" << req_body;` (it did send back the hardcoded part of the response, just not the value of req_body). When I noticed this I tried writing the request body to a file server-side, as to check whether it was or was not receiving the data and just failing with sending back 1992294.4 bits of data (see code below, git repo is also updated). This resulted in the error `Error: basic_string::substr: __pos (which is 30) > this->size() (which is 0)`, meaning that req_body is completely empty, this is backed up by the completely empty file of file.zip. Any ideas on why this stopped working all of a sudden? Could it still be with it just not being able to handle the body data as GET method? How would I turn this into a POST method (I can't find it in the docs)?  
  
```cpp  
std::string req_body = boost::beast::buffers_to_string(req.body().data());  
std::string rawZIPdata = req_body.substr((int)(req_body.find("Content-Type: application/zip\n") + 31), (int)(req_body.length()));  
auto zipFile = std::fstream("file.zip", std::ios::out | std::ios::binary);  
zipFile << rawZIPdata << std::endl;  
zipFile.close();

---

## Comment 16

> Username: madmongo1  
> Created at: 2021-02-16 08:40:11 UTC  
> Url: https://github.com/boostorg/beast/issues/2165#issuecomment-779677934  

shouldn't `"Content-Type: application/zip\n"` be `"Content-Type: application/zip\r\n"` ?  
  
Also, why are you looking for headers in the body? That doesn't make sense to me.  
  
shouldn't it be:  
  
```  
if(request["Content-Type"] == "application/zip") {  
  ...  
}  
```  
?

---

## Comment 17

> Username: LucAngevare  
> Created at: 2021-02-16 12:44:55 UTC  
> Updated at: 2021-02-17 10:11:20 UTC  
> Url: https://github.com/boostorg/beast/issues/2165#issuecomment-779813688  

> shouldn't `"Content-Type: application/zip\n"` be `"Content-Type: application/zip\r\n"`?  
  
That's true. In the end `\r\n` would've been correct.  
  
> Also, why are you looking for headers in the body? That doesn't make sense to me.  
> shouldn't it be:  
> <code>if(request["Content-Type"] == "application/zip") {  
  ...  
}</code>  
> ?  
  
Well as I brought up in passing, in the end, more elements inside the body will be used, which means that the content type would be multipart/form-data. Only 1 of the 3 (or more later on) body elements out of the form data will actually contain a zip file. If I interpreted your response correctly I believe I didn't bring that point across entirely clearly. Furthermore I'm not necessarily checking whether the content-type of a (or the multiple of the) body element(s) is a zip, rather splitting the string and attempting to write the actual data of the sent ZIP out of one of the elements inside the form data to a physical file in the directory of the webserver.  
  
One more thing, I did some research and apparently the `res.set(http::field::content_type, 'application/json');` line, was in fact incorrect. C++ as well as C use double quotes for strings and single quotes for characters or character array. You were correct that that was a syntax error, it just didn't have any consequences.  
  
Thanks again!

---

## Comment 18

> Username: LucAngevare  
> Created at: 2021-02-17 09:58:13 UTC  
> Url: https://github.com/boostorg/beast/issues/2165#issuecomment-780441321  

As a bit of an update, I removed the part to check if the request method was a GET or POST method and sending a POST request using curl with the file as form-data (command I used was `$ curl -v -F "ZIP=@/home/user/path/real-world-test.zip" http://127.0.0.1:3538/give`) does the same thing as Postman did. It is able to get past the 100-continue status code but after that it just does nothing until the connection is reset by the peer for some reason. To be completely honest I have no idea what it is trying to do or how to fix it.  
  
```  
$ curl -v -F "ZIP=@/home/user/path/real-world-test.zip" http://127.0.0.1:3538/give  
  
*   Trying 127.0.0.1:3538...  
* TCP_NODELAY set  
* Connected to 127.0.0.1 (127.0.0.1) port 3538 (#0)  
> POST /give HTTP/1.1  
> Host: 127.0.0.1:3538  
> User-Agent: curl/7.68.0  
> Accept: */*  
> Content-Length: 1892462  
> Content-Type: multipart/form-data; boundary=------------------------883f56506a97de52  
> Expect: 100-continue  
>   
* Done waiting for 100-continue  
* Recv failure: Connection reset by peer  
* Closing connection 0  
curl: (56) Recv failure: Connection reset by peer

---

## Comment 19

> Username: madmongo1  
> Created at: 2021-02-18 17:59:24 UTC  
> Url: https://github.com/boostorg/beast/issues/2165#issuecomment-781529571  

You're wondering why the peer reset the connection.  
  
```  
                http::async_write(  
                    sock,  
                    res,  
                    [self](beast::error_code ec, std::size_t) {  
                        self->sock.shutdown(tcp::socket::shutdown_send, ec);  
                        self->deadline_.cancel();  
                    });  
                }  
```  
  
This is exactly what `self->sock.shutdown()` does.  
  
You have destroyed the connection rather than waiting for the next request.

---

## Comment 20

> Username: LucAngevare  
> Created at: 2021-02-24 09:27:57 UTC  
> Updated at: 2021-02-24 09:28:27 UTC  
> Url: https://github.com/boostorg/beast/issues/2165#issuecomment-784937870  

Just so it doesn't seem as if I'm ignoring this issue, I've been working on rewriting this code basically entirely. I've got it to work for the most part but I've been having some issues with it not giving a reply when getting any type of form-data which I still need to work out.

---

## Comment 21

> Username: madmongo1  
> Created at: 2021-02-24 11:08:15 UTC  
> Url: https://github.com/boostorg/beast/issues/2165#issuecomment-784999475  

OK, thanks for letting me know.  
  
Note that using the test::stream can be really useful for tracking down application errors - it lets you focus on program logic without worrying about the tcp connection complexities.

---

## Comment 22

> Username: LucAngevare  
> Created at: 2021-03-17 07:49:42 UTC  
> Url: https://github.com/boostorg/beast/issues/2165#issuecomment-800873487  

I'm fairly sure you've already seen my Reddit post but I thought it would be smart to also post an update here, the reason I started with the Reddit post was because I didn't want to be too much of a burden toward you but that hasn't really worked :smile:  
  
I have been able to take out all the lines that say the socket should shutdown, but the problem persists. I've been able to send and save PNGs under the ~1MB limit, but anything above that and it still gives an empty reply. This happens with both the file sent as `--form` and as `--binary-data`. I'm fairly certain the problem is not with the `body_limit` because obviously I'm not getting any sort of error (runtime or compile). I look forward to hearing back from you and thanks again so much for all the help and time you've given me!

---

## Comment 23

> Username: madmongo1  
> Created at: 2021-03-17 08:23:56 UTC  
> Url: https://github.com/boostorg/beast/issues/2165#issuecomment-800892600  

Do you have this code on a github repo somewhere so I can build it and see what's going wrong?

---

## Comment 24

> Username: LucAngevare  
> Created at: 2021-03-17 08:25:03 UTC  
> Url: https://github.com/boostorg/beast/issues/2165#issuecomment-800893191  

Yeah the repo I sent in one of the first comments should be up to date.

---

## Comment 25

> Username: LucAngevare  
> Created at: 2021-04-10 09:16:17 UTC  
> Url: https://github.com/boostorg/beast/issues/2165#issuecomment-817105929  

@madmongo1  
Are there any bugs you have found in the code yet that could point to the issue? I'd really love to know.  
Thanks in advance!

---

## Comment 26

> Username: madmongo1  
> Created at: 2021-04-10 10:53:17 UTC  
> Url: https://github.com/boostorg/beast/issues/2165#issuecomment-817117213  

What have you tried so far with debugging? Single step, sanitizers, asserts?

---

## Comment 27

> Username: LucAngevare  
> Created at: 2021-04-10 16:40:41 UTC  
> Url: https://github.com/boostorg/beast/issues/2165#issuecomment-817168299  

Thus far only single step, the Visual Studio Code default I believe. This did not show anything else, it just returned a warning on `i` being an int instead of a long unsigned int, which I was able to fix. I'll do some research on those other methods you mentioned but I'm not too familiar with debugging, I usually just run any code I've written and if I run into any errors, I just rewrite that part until it works. I think that's also why I'm fairly useless if it doesn't give an error but doesn't work.  
  
I'll do some more research on debugging, to try and see if that gives a different output.

---

## Comment 28

> Username: LucAngevare  
> Created at: 2021-04-21 16:37:05 UTC  
> Url: https://github.com/boostorg/beast/issues/2165#issuecomment-824200892  

Well, thank you so much for all the time and effort you put into helping me, but I've decided to switch to uWS. Especially coming from Node.js and Python I find that the documentation of uWS is just a tad more extensive and the library itself is fairly intuitive for me, as it's comparable to Express (a module in Node.js) which I have experience in.  
  
I hope you don't mind, and thanks again for your time!
