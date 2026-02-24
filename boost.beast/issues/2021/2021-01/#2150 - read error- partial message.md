# #2150 - read error: partial message [Closed]

> Username: abhilashraju  
> Created at: 2021-01-18 11:30:19 UTC  
> Updated at: 2022-01-09 05:17:14 UTC  
> Closed at: 2022-01-09 05:17:14 UTC  
> Url: https://github.com/boostorg/beast/issues/2150  

Hi   
 Thanks for the library it really helped me to develop some great tools to overcome some issues we faced during pandemic time:-)   
 I am getting "partial message read error" in response reading phase after making a post request. I am using dedicated thread for network communication on which I called ioc.run()(only place it is called). The code is the same as "https://www.boost.org/doc/libs/develop/libs/beast/example/http/client/async-ssl/http_client_async_ssl.cpp". Initially, I was using http::response<http::string_body> res_. In order to check the response content, I move to  http::response_parser< http::string_body> . When  I inspected the content I see that the response body contains the full message sent from the server. But still, the "partial message read error" occurs. What might be the reason for this?

---

## Comment 1

> Username: madmongo1  
> Created at: 2021-01-18 11:35:29 UTC  
> Url: https://github.com/boostorg/beast/issues/2150#issuecomment-762191551  

partial_message occurs if put_eof() is called on the parser while the message is incomplete (i.e. headers not fully consumed or there is remaining content-length unaccounted for).  
  
It's difficult to comment further than this without access to a reproducable test.

---

## Comment 2

> Username: abhilashraju  
> Created at: 2021-01-18 11:52:25 UTC  
> Url: https://github.com/boostorg/beast/issues/2150#issuecomment-762200530  

Thanks for the quick response. Yes, I know that my question was incomplete. I am new to network programming and trying to get familiarise with  boost::asio /beast. So needed a starting point to troubleshoot further in order to frame the question properly. From the answer, I think, there is a possibility of mismatch in the content length and actual content from the server-side. Is there any way we can overcome such mismatches without putting parser into an invalid state?. I will get back after doing some troubleshooting.

---

## Comment 3

> Username: madmongo1  
> Created at: 2021-01-19 09:24:49 UTC  
> Updated at: 2021-01-19 09:24:58 UTC  
> Url: https://github.com/boostorg/beast/issues/2150#issuecomment-762714701  

> I think, there is a possibility of mismatch in the content length and actual content from the server-side.   
   
 Ok, so this is a protocol error. The client is not expected to be able to recover from this,  
   
> Is there any way we can overcome such mismatches without putting parser into an invalid state?  
   
 It's not that the parser is in an invalid state, it's that the server is not keeping to the protocol and the client is telling you this.  
There is no valid recovery scenario that would be reliable. You need to fix the server.

---

## Comment 4

> Username: abhilashraju  
> Created at: 2021-02-03 17:42:49 UTC  
> Url: https://github.com/boostorg/beast/issues/2150#issuecomment-772693526  

Unfortunately, I don't have access to server-side implementation. I was trying to use beast as a replacement for curl client. I tried several options including async read. Finally, resort to incremental read as below  
template<typename Cont>  
 void sync_read(auto& stream_,auto& buffer_,Cont&& cont){  
            beast::error_code ec{};       
            http::response_parser<http::buffer_body> res0;  
            http::read_header(stream_, buffer_, res0,ec);  
            if(ec){  
                return call_app_handler(ec,"read: header");   
                  
            }  
            int field_count = 0;  
            for(auto const& field : res0.get())  
                std::cout << "Field#"  
                            << ++field_count << " : " << field.name() << " = " << field.value() << std::endl;  
  
            std::stringstream os;  
            while(! res0.is_done())  
            {  
                char buf[512];  
                res0.get().body().data = buf;  
                res0.get().body().size = sizeof(buf);  
                http::read(stream_, buffer_, res0, ec);  
                if(ec == http::error::need_buffer)  
                    ec = {};  
                if(ec)  
                    return call_app_handler(ec,"read: body");;  
                os.write(buf, sizeof(buf) - res0.get().body().size);  
            }  
            // http::response_parser<http::string_body> res{std::move(res0)};  
            // http::read(stream_, buffer_, res,ec);  
            // if(ec){  
            //     return call_app_handler(ec,"read: body");  
            // }  
            // call_app_handler(ec,res.get().body());  
            call_app_handler(ec,os.str());  
            cont();  
        }  
This it the response header content   
Field#1 : Date = Wed, 03 Feb 2021 17:23:21 GMT  
Field#2 : Content-Type = application/json;charset=UTF-8  
Field#3 : Connection = close  
  
The content-length is missing.   
Using curl command I could get the response. I was hoping that the beast will do the same. Before talking to the server folks I want to make sure that it is really a protocol error from the server and there is no workaround we can do in client-side to overcome it.

---

## Comment 5

> Username: madmongo1  
> Created at: 2021-02-03 17:59:56 UTC  
> Url: https://github.com/boostorg/beast/issues/2150#issuecomment-772704888  

any chance you can post the complete response you get through curl? (headers and body)  
  
I'd like to build a test case here to check all is correct.

---

## Comment 6

> Username: madmongo1  
> Created at: 2021-02-03 18:00:42 UTC  
> Url: https://github.com/boostorg/beast/issues/2150#issuecomment-772705349  

If you'd prefer to send it privately you can email me: hodges.r@gmail.com  
I won't publish it in our tests before reducing it to the smallest example that replicates the problem (if any)

---

## Comment 7

> Username: abhilashraju  
> Created at: 2021-02-04 04:50:58 UTC  
> Url: https://github.com/boostorg/beast/issues/2150#issuecomment-773026485  

I have sent you a mail containing the request.  
This is the response with the header after using the curl request.  
HTTP/1.1 500  
Date: Thu, 04 Feb 2021 04:41:45 GMT  
Content-Type: application/json;charset=UTF-8  
Transfer-Encoding: chunked  
Connection: keep-alive  
  
{"timestamp":"2021-02-04T04:41:45.301+0000","status":500,"error":"Internal Server Error","message":"App has reached an unexpected condition","path":"/shortcut/v1/cui"}  
  
Even though The server is in an unexpected condition right now, I wanted to show this as an error message in the client-side.  So forget about the body content right now.

---

## Comment 8

> Username: abhilashraju  
> Created at: 2021-02-05 07:09:00 UTC  
> Url: https://github.com/boostorg/beast/issues/2150#issuecomment-773841365  

see the full source in case you wanted to review   
[https://github.com/abhilashraju/http-client-based-on-beast/blob/main/http_client.hpp](https://github.com/abhilashraju/http-client-based-on-beast/blob/main/http_client.hpp)

---

## Comment 9

> Username: madmongo1  
> Created at: 2021-02-05 07:10:23 UTC  
> Url: https://github.com/boostorg/beast/issues/2150#issuecomment-773842035  

Ok thanks. Helpful.  
  
On Fri, 5 Feb 2021 at 08:09, abhilashraju <notifications@github.com> wrote:  
  
> see the full source in case you wanted to review  
>  
> https://github.com/abhilashraju/http-client-based-on-beast/blob/main/http_client.hpp  
>  
> —  
> You are receiving this because you commented.  
>  
>  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/beast/issues/2150#issuecomment-773841365>,  
> or unsubscribe  
> <https://github.com/notifications/unsubscribe-auth/ABHOZSO4FA52KW7UOMKFVZTS5OKRTANCNFSM4WHFEFAA>  
> .  
>  
--   
Richard Hodges  
hodges.r@gmail.com  
office: +442032898513  
home: +376841522  
mobile: +376380212

---

## Comment 10

> Username: madmongo1  
> Created at: 2021-02-05 14:00:55 UTC  
> Url: https://github.com/boostorg/beast/issues/2150#issuecomment-774050024  

I have reduced your code to a minimal test:  
https://github.com/madmongo1/boost-issue-2150  
  
The output I get is:  
```  
call_app_handler: ec=bad status, msg=read: header  
```  
  
Please confirm that this is what you would expect to see?

---

## Comment 11

> Username: abhilashraju  
> Created at: 2021-02-05 16:47:51 UTC  
> Url: https://github.com/boostorg/beast/issues/2150#issuecomment-774150500  

Though the response was an error at that point in time, the actual response was a valid XML body. In both case, server failure and success, the partial read error is what I am getting. I was suspicious about the response body reading phase after a post request. I tried to debug the code. It seems, the read function in read.hpp is failing. [See](https://github.com/abhilashraju/http-client-based-on-beast/blob/main/partail_read.PNG) the pic for details.

---

## Comment 12

> Username: abhilashraju  
> Created at: 2021-02-05 17:11:03 UTC  
> Url: https://github.com/boostorg/beast/issues/2150#issuecomment-774164669  

It seems already complete data has been read as part of road header call. After the call, I could see that the buffer_ contains the body content too. So maybe that is why next stream reading is failing when we tried to read further for the body.

---

## Comment 13

> Username: madmongo1  
> Created at: 2021-02-05 17:13:38 UTC  
> Url: https://github.com/boostorg/beast/issues/2150#issuecomment-774166016  

Did you `consume()` the indicated number of bytes from the buffer, or re-create the parser?  
The parser is not re-usable.

---

## Comment 14

> Username: abhilashraju  
> Created at: 2021-02-05 17:22:40 UTC  
> Url: https://github.com/boostorg/beast/issues/2150#issuecomment-774170968  

I did not make any specific call for "consume".  If you look at the sync-read code, All I am doing is that read header -> print header details -> try to read the body. I thought beast would internally handle the header and body parsing from the buffer_. Is there anything wrong with the code I shared?

---

## Comment 15

> Username: madmongo1  
> Created at: 2021-02-05 18:30:12 UTC  
> Url: https://github.com/boostorg/beast/issues/2150#issuecomment-774208672  

It's difficult for me to comment intelligently at the moment, because the result of the test program I linked above does not produce the error you are seeing.  
  
Would you be able to push a commit to my repo to demonstrate the problem?

---

## Comment 16

> Username: abhilashraju  
> Created at: 2021-02-08 05:26:47 UTC  
> Url: https://github.com/boostorg/beast/issues/2150#issuecomment-774876338  

I got some authentication error while pushing the code.  Try below . It is the problematic response .   
  
s.append("HTTP/1.1 500 \r\nDate: Sun, 07 Feb 2021 05:56:28 GMT\r\nContent-Type: application/json;charset=UTF-8\r\nConnection: close\r\n\r\n");  
    s.append(chunkify("{\"timestamp\":\"2021-02-07T05:22:53.463+0000\",\"status\":500,\"error\":\"Internal Server Error\",\"message\":\"App has reached an unexpected condition\",\"path\":\"/shortcut/v1/cui\"}"));  
    s.append(chunkify(""));

---

## Comment 17

> Username: madmongo1  
> Created at: 2021-02-08 07:08:03 UTC  
> Updated at: 2021-02-08 07:08:28 UTC  
> Url: https://github.com/boostorg/beast/issues/2150#issuecomment-774924781  

you can create a pull request from a forked repo

---

## Comment 18

> Username: madmongo1  
> Created at: 2021-02-08 08:07:26 UTC  
> Url: https://github.com/boostorg/beast/issues/2150#issuecomment-774955077  

The header you have posted does not specify content-encoding or content-length, so the test fails to complete the read (since I have not posted an EOF to the stream).  
As far as I can see it's working as intended.

---

## Comment 19

> Username: abhilashraju  
> Created at: 2021-02-08 11:14:58 UTC  
> Url: https://github.com/boostorg/beast/issues/2150#issuecomment-775070256  

Does that mean the server fails to send that mandatory fields? I will check with the server team then.

---

## Comment 20

> Username: madmongo1  
> Created at: 2021-02-08 16:32:17 UTC  
> Url: https://github.com/boostorg/beast/issues/2150#issuecomment-775272241  

The fields are not mandatory, but tif they are not present, then the http reponse ends when the stream reaches an EOF state.

---

## Comment 21

> Username: abhilashraju  
> Created at: 2021-02-09 06:10:34 UTC  
> Url: https://github.com/boostorg/beast/issues/2150#issuecomment-775695904  

Can't beast handle above response gracefully( like how curl handles)? The above response I copy-pasted from buffer_ during the read_header API call. I am not sure why the body- read call again trying read from the stream if all data is already read as part of read_header.

---

## Comment 22

> Username: madmongo1  
> Created at: 2021-02-09 11:56:56 UTC  
> Url: https://github.com/boostorg/beast/issues/2150#issuecomment-775885714  

Yes, beast can recover gracefully.  
  
I'm not yet sure what bug you are seeing because they test I built from your code functions as expected.  
  
Would you be able to construct a minimal, self contained, test that demonstrates the problem you are seeing?  
  
I think there might be a misunderstanding in how to drive beast. I would be very surprised if there is a bug here. We have close to 100% coverage on this code, and beast is running in production on a large number of servers.

---

## Comment 23

> Username: abhilashraju  
> Created at: 2021-02-09 14:57:03 UTC  
> Url: https://github.com/boostorg/beast/issues/2150#issuecomment-776000628  

I have made a stand-alone version of my library [here](https://github.com/abhilashraju/http-client-based-on-beast). The main.cpp contains the full version of the code with URL to fetch data. I am expecting no "partial read error" in the HTTP response because  I could use curl to get the data without any error. You may get authentication failure because of the wrong auth token usage. I will give you a new token through a mail.  
Meanwhile, you can try main2.cpp instead, which uses your version of the code to demonstrate the issue. In this case, the program gets hung in the read call. I have added the build command I used in the readme file.

---

## Comment 24

> Username: madmongo1  
> Created at: 2021-02-09 15:17:01 UTC  
> Url: https://github.com/boostorg/beast/issues/2150#issuecomment-776015252  

You can un-hang my test code by sending an eof() to the test stream to simulate the remote peer hanging up the connection, as the web server will do.

---

## Comment 25

> Username: abhilashraju  
> Created at: 2021-02-09 16:37:53 UTC  
> Url: https://github.com/boostorg/beast/issues/2150#issuecomment-776071423  

I see. I thought that was an error. Can I conclude that in order to avoid the partial read error, the server should send back a proper response that contains either content-encoding or content-length? Otherwise, the basic beast parser will always end with a partial read error, right?

---

## Comment 26

> Username: madmongo1  
> Created at: 2021-02-10 05:54:55 UTC  
> Url: https://github.com/boostorg/beast/issues/2150#issuecomment-776463380  

The 500 response you posted is a valid HTTP 1.0 response. Beast handles this properly, terminating the body read upon detecting the EOF.  
  
I don't see the partial read error you are reporting happening in the test.  
  
I can't comment until you share a test that fails with partial read that I can reproduce.  
  
Are you sure this test represents what you are seeing?

---

## Comment 27

> Username: abhilashraju  
> Created at: 2021-02-11 05:25:13 UTC  
> Url: https://github.com/boostorg/beast/issues/2150#issuecomment-777210250  

True, the test code in main2.cpp seems to be working fine in success use case too. It could parse the body successfully. However, in my actual use case (main.cpp) I get a partial read error in both failure(response 500) and success(Response 200) case. I will update the new token in main.cpp once the server is up and running. Then you can see the problem yourself by running the main.cpp.

---

## Comment 28

> Username: madmongo1  
> Created at: 2021-02-11 08:14:49 UTC  
> Url: https://github.com/boostorg/beast/issues/2150#issuecomment-777268474  

Ok I’ll stop dropping hints and just come out with it.  
  
The beast code is correct. Your code is doing something wrong.  
  
The way to find what you have done wrong is to WRITE A MINIMAL TEST TO REPLICATE THE ISSUE IN ISOLATION, using the exact same data and approach that you are using in your program.  
In ISOLATION means a separate executable that does exactly one thing with exactly one block of data, using none of the assumptions in the program you are having trouble with.  
  
You will find that you can’t replicate the issue. That’s because beast is correct.  
  
The next step is go back to your code and see what you are doing wrong outside the read section.  
I.e race conditions, managing buffers incorrectly, timing issues, undefined behaviour and logic errors.  
  
Then expand your test one tiny feature at a time until you see the problem. It will be the last change you made.

---

## Comment 29

> Username: abhilashraju  
> Created at: 2021-02-14 06:14:06 UTC  
> Url: https://github.com/boostorg/beast/issues/2150#issuecomment-778733678  

I was debugging this further. The problem may not be with the basic parser. But it has something to do with the handling of the response after a post request through SSL connection. The following is the place where the error code is set inside the parse_until function in read.hpp.  
// Upgrade the error if we have a partial message.  
        // This causes SSL short reads (and every other error)  
        // to be converted into something else, allowing the  
        // caller to distinguish an SSL short read which  
        // represents a safe connection closure, versus  
        // a closure with data loss.  
        if( ec != net::error::operation_aborted &&  
            parser.got_some() && ! parser.is_done())  
        {  
            ec = error::partial_message;  
        }  
  
What does this mean? Can I ignore the error in my client code and proceed with data read from the response?

---

## Comment 30

> Username: madmongo1  
> Created at: 2021-02-14 09:27:28 UTC  
> Url: https://github.com/boostorg/beast/issues/2150#issuecomment-778751579  

It means that it is legal for an HTTPS response to terminate with an SSL "short read" error. Essentally it means that the HTTP layer understands that SSL connections are often terminated with a TCP shutdown before the SSL goodbye frame has been received.  
  
The HTTP standard specifically calls out this scenario and notes that it is not an error - provided the connection is HTTP/1.1 and the content-length is either known (by being present in the header) or properly deduced (by a chunked stream being terminated correctly).  
  
In HTTP/1.0 it is illegal as it could be the result of an intermediate proxy using a "truncated message" attack.  
  
I am pretty confident that the problem is not in our code. I would strongly enourage you, once again, to write a minimal test that proves just the handling of the message. I am certain that this will work, indicating that your problem is somewhere else in your code.

---

## Comment 31

> Username: madmongo1  
> Created at: 2021-02-14 09:48:44 UTC  
> Url: https://github.com/boostorg/beast/issues/2150#issuecomment-778753747  

for example: https://bugs.chromium.org/p/chromium/issues/detail?id=244260

---

## Comment 32

> Username: abhilashraju  
> Created at: 2021-02-14 17:14:04 UTC  
> Url: https://github.com/boostorg/beast/issues/2150#issuecomment-778807880  

Thanks for the example. Let me go through it.   
I was figuring out what test case to write. The real problem with me is that  I don't have access to server code and have no idea about what might the response be. So I tried to copy the data read from the server response and used the same in the test case that sends it via test::stream. The test passes and I could read the body successfully. Then I debugged both scenarios and could find out that the only difference is in the  "eof" reading stage. The parser could successfully finish the parsing after reading eof form test::stream. But in actual server response, there is an error at this stage(boost::asio::ssl::error::stream_truncated). Is there any way I can replicate the error using test::stream?

---

## Comment 33

> Username: madmongo1  
> Created at: 2021-02-14 17:20:17 UTC  
> Url: https://github.com/boostorg/beast/issues/2150#issuecomment-778808803  

Hmm. Sounds like the server is calling shutdown() on the socket without doing an SSL goodbye.   
Unfortunately the test stream doesn't let you inject any error.  
It probably should.  
I'll look into it.

---

## Comment 34

> Username: stale[bot]  
> Created at: 2021-05-29 16:11:04 UTC  
> Url: https://github.com/boostorg/beast/issues/2150#issuecomment-850857881  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 35

> Username: stale[bot]  
> Created at: 2022-01-09 05:16:49 UTC  
> Url: https://github.com/boostorg/beast/issues/2150#issuecomment-1008232376  

It looks like this issue has either been abandoned or resolved so I will go ahead and close it. Feel free to re-open this issue if you feel it needs attention, or open new issues as needed. Thanks!
