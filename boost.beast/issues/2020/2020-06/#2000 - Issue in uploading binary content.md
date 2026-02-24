# #2000 - Issue in uploading binary content [Closed]

> Username: GaneshTambat1  
> Created at: 2020-06-29 13:57:07 UTC  
> Updated at: 2020-08-08 09:37:48 UTC  
> Closed at: 2020-08-08 09:37:48 UTC  
> Url: https://github.com/boostorg/beast/issues/2000  

I am trying to upload an image file to Microsoft OneDrive. I am using the Boost Beast library. My code is as follows:  
  
        restoreReq_.method(http::verb::put);  
        restoreReq_.version(version);  
        restoreReq_.target(target);  
        restoreReq_.set(http::field::host, host);  
        restoreReq_.set(http::field::user_agent, BOOST_BEAST_VERSION_STRING);          
          
        restoreReq_.set(http::field::authorization, strToken_.c_str());  
        restoreReq_.set(beast::http::field::content_type, "application/octet-stream");  
                                       
        std::ifstream sourceFile("E:\\Home.gif", std::ifstream::binary | std::ifstream::in);  
        std::stringstream buffer;  
        buffer << sourceFile.rdbuf();  
        std::string fileContent = buffer.str();  
        long len = fileContent.length();  
        restoreReq_.set(http::field::content_length, len);  
        restoreReq_.body() = fileContent.c_str();  
        restoreReq_.prepare_payload();  
  
I can see that "restoreReq_.body() = fileContent.c_str();" is not assigning the binary content correctly. It is assigning till it finds the first '\0' and after that, it stops. What is the right way to assign binary content to body()?   
  
Regards,  
GT

---

## Comment 1

> Username: Eelis  
> Created at: 2020-06-29 15:00:07 UTC  
> Updated at: 2020-06-29 15:01:32 UTC  
> Url: https://github.com/boostorg/beast/issues/2000#issuecomment-651177493  

Since the body type returned by body() is determined by a template parameter of http::request, I think it depends on the actual type of your restoreReq_ variable.

---

## Comment 2

> Username: GaneshTambat1  
> Created at: 2020-06-29 16:50:00 UTC  
> Url: https://github.com/boostorg/beast/issues/2000#issuecomment-651238716  

Thanks for the quick reply. I am not able to figure out which body type my http::request should have. Currently, it is set to http::string_body and as you correctly pointed out, this causes the issue with binary data. What is the correct body type for Binary data?

---

## Comment 3

> Username: Eelis  
> Created at: 2020-06-29 17:06:49 UTC  
> Url: https://github.com/boostorg/beast/issues/2000#issuecomment-651247194  

I expect that if you use http::string_body, then you can most likely do `restoreReq_.body() = fileContent;` without the `.c_str()`.

---

## Comment 4

> Username: vinniefalco  
> Created at: 2020-06-29 17:21:06 UTC  
> Url: https://github.com/boostorg/beast/issues/2000#issuecomment-651254251  

or maybe use `http::file_body` since you want to upload a file...

---

## Comment 5

> Username: GaneshTambat1  
> Created at: 2020-06-30 05:36:38 UTC  
> Url: https://github.com/boostorg/beast/issues/2000#issuecomment-651550950  

Thanks guys for quick replies. Suggestion to use "restoreReq_.body() = fileContent; without the .c_str()." solved the issue. I can now upload  different image types e.g. pdf, jpg etc.

---

## Comment 6

> Username: stale[bot]  
> Created at: 2020-08-01 09:00:22 UTC  
> Url: https://github.com/boostorg/beast/issues/2000#issuecomment-667498876  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 7

> Username: stale[bot]  
> Created at: 2020-08-08 09:37:46 UTC  
> Url: https://github.com/boostorg/beast/issues/2000#issuecomment-670851318  

It looks like this issue has either been abandoned or resolved so I will go ahead and close it. Feel free to re-open this issue if you feel it needs attention, or open new issues as needed. Thanks!
