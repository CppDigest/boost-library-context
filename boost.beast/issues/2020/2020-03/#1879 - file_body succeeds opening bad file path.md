# #1879 - file_body succeeds opening bad file path [Closed]

> Username: benstadin  
> Created at: 2020-03-18 01:31:37 UTC  
> Updated at: 2024-02-20 11:41:34 UTC  
> Closed at: 2024-02-20 11:33:52 UTC  
> Url: https://github.com/boostorg/beast/issues/1879  

I'm running into an issue when the server receives a bogus file path like ".//v1/mapview/". file_body is able to open that path without error (see below), later on in Async Write an error code is set with message "Is a directory".   
  
Should file_body.open check if the path is a directory?  
  
```  
void codeSnippetSendHandler() {  
// snip  
case RT_FILE: {  
                    if (req.method() != http::verb::get &&  
                        req.method() != http::verb::head ) {  
                        return send(_defaultController.prepareBadRequest(std::move(req), "HTTP-method not accepted"));  
                    }  
                      
                    // Attempt to open the file  
                    beast::error_code ec;  
                    http::file_body::value_type body;  
                    body.open(response->data.c_str(), beast::file_mode::scan, ec);  
                      
                    // Handle the case where the file doesn't exist  
                    if(ec == beast::errc::no_such_file_or_directory || !body.is_open()) {  
                        return send(_defaultController.prepareNotFound(std::move(req), response->data));  
                    }  
                      
                    // Handle an unknown error  
                    if (ec) {  
                        return send(_defaultController.prepareServerError(std::move(req), ec.message()));  
                    }  
                      
                    // Respond to HEAD request  
                    if (std::move(req).method() == http::verb::head) {  
                        return send(_defaultController.prepareHeadResponse(std::move(req), body.size(), mimeType(response->data)));  
                    }  
                      
                    // Respond to GET request  
                    if (std::move(req).method() == http::verb::get) {  
                        return send(_defaultController.prepareFileResponse(std::move(req), body, mimeType(response->data), http::status::ok));  
                    }  
                    break;  
                }  
}  
  
  
        template<class Body, class Allocator>  
        http::response<http::file_body> prepareFileResponse(http::request<Body, http::basic_fields<Allocator>>&& req,  
                                                                      http::file_body::value_type &body,  
                                                                      boost::string_view mimeType,  
                                                                      http::status statusCode) {  
            // Cache the size since we need it after the move  
            auto const size = body.size();  
              
            http::response<http::file_body> res {  
                std::piecewise_construct,  
                std::make_tuple(std::move(body)),  
                std::make_tuple(statusCode, req.version())  
            };  
              
            setFields(res);  
              
            res.set(http::field::server, LOCATION_MASTER_SERVER_VERSION_STRING);  
            res.set(http::field::content_type, mimeType);  
            res.content_length(size);  
            res.keep_alive(std::move(req).keep_alive());  
            return res;  
        }  
```

---

## Comment 1

> Username: vinniefalco  
> Created at: 2020-03-18 02:23:46 UTC  
> Url: https://github.com/boostorg/beast/issues/1879#issuecomment-600391358  

That's perplexing... which implementation is it using for `file`?

---

## Comment 2

> Username: benstadin  
> Created at: 2020-03-18 02:31:02 UTC  
> Url: https://github.com/boostorg/beast/issues/1879#issuecomment-600393041  

I've updated the code snippet with the method preparing the file response.

---

## Comment 3

> Username: benstadin  
> Created at: 2020-03-18 02:42:24 UTC  
> Url: https://github.com/boostorg/beast/issues/1879#issuecomment-600395720  

I should eventually mention that ./v1/mapview is an existing path on the server. A url to a non existing path like /v1/mapview/test correctly handles file not found.

---

## Comment 4

> Username: madmongo1  
> Created at: 2020-03-18 07:16:17 UTC  
> Url: https://github.com/boostorg/beast/issues/1879#issuecomment-600462104  

Hmm, this is an interesting one.  
A file could be a number of things - symlink, Unix device, etc.  
Should the file body interface deal in file names (and therefore have to disambiguate) or file descriptors?

---

## Comment 5

> Username: benstadin  
> Created at: 2020-03-18 12:20:46 UTC  
> Url: https://github.com/boostorg/beast/issues/1879#issuecomment-600590694  

My interpretation is that the file modes described in file_base.hpp need to be considered  in file_body.open(). E.g. if file mode is beast::file_mode::scan or beast::file_mode::read then file_body.open() should fail in case the file is not readable.

---

## Comment 6

> Username: vinniefalco  
> Created at: 2020-03-18 13:21:56 UTC  
> Url: https://github.com/boostorg/beast/issues/1879#issuecomment-600619255  

Beast _File_ is never intended for directories

---

## Comment 7

> Username: benstadin  
> Created at: 2020-03-18 13:33:25 UTC  
> Url: https://github.com/boostorg/beast/issues/1879#issuecomment-600624947  

Should it then fail to open?

---

## Comment 8

> Username: madmongo1  
> Created at: 2020-03-18 15:02:28 UTC  
> Url: https://github.com/boostorg/beast/issues/1879#issuecomment-600674176  

@benstadin I'll have a design discussion with Vinnie about the behaviour of our object. I fear that like all seemingly simple design decisions, this one will end up more nuanced than was originally imagined.  
  
In the meantime, are you comfortable adding a workaround in your code to `stat` the file and behave accordingly?

---

## Comment 9

> Username: benstadin  
> Created at: 2020-03-18 16:45:22 UTC  
> Url: https://github.com/boostorg/beast/issues/1879#issuecomment-600740722  

@madmongo1  Sure, that will work for me. I already have a workaround in place and was raising it as a general topic.

---

## Comment 10

> Username: madmongo1  
> Created at: 2020-06-12 12:42:49 UTC  
> Url: https://github.com/boostorg/beast/issues/1879#issuecomment-643250214  

I've had a think about this.   
  
The #1 concern around sending files is security. Are you sending the file you intend to send? This applies whether you are acting as client or server.  
  
Checking the program's intent is well beyond the scope of Beast. Beast has to assume that if you offer a file-name, then the expectation is that the file exists, is of the correct type, and that you do intend to send it.  
  
It is therefore my view that checking that the file exists (and may be legally transmitted) should already have been checked by the application. I see no reason to change Beast's behaviour here.  
  
I'll leave the issue open for a few more hours in case someone wants to argue, but I am minded to close it.

---

## Comment 11

> Username: benstadin  
> Created at: 2020-06-12 13:53:31 UTC  
> Url: https://github.com/boostorg/beast/issues/1879#issuecomment-643282295  

I can follow your thoughts on this generally. Though from an API user perspective, it seems counter intuitive to what the library seems to offer:  
  
 // Attempt to open the file  
                    beast::error_code ec;  
                    http::file_body::value_type body;  
                    body.open(response->data.c_str(), beast::file_mode::scan, ec);  
                      
                    // Handle the case where the file doesn't exist  
                    if(ec == beast::errc::no_such_file_or_directory || !body.is_open()) {  
                        return send(_defaultController.prepareNotFound(std::move(req), response->data));  
                    }  
  
I mean the sbove code reads like: Open file at path, otherwise handle the case when there is no such file or directory. It doesn‘t seem very intuitive that this check is never done.

---

## Comment 12

> Username: madmongo1  
> Created at: 2020-06-12 14:31:33 UTC  
> Url: https://github.com/boostorg/beast/issues/1879#issuecomment-643302432  

Can you suggest an alternative that we can implement without breaking compatibility?

---

## Comment 13

> Username: benstadin  
> Created at: 2020-06-12 17:01:55 UTC  
> Url: https://github.com/boostorg/beast/issues/1879#issuecomment-643385531  

Putting all pieces together from what I as user would assume, design decisions and what Vinnie said („ Beast File is never intended for directories“), I think after a call to open() body.is_open() should be set to false for „directories“.   
  
Arguably, it changes the result. But in my opinion this is not an API change, but rather a corrective measure due to the expectation that file_body is by definition dealing with files only, and an attempt to open a directory should by this definition never have succeeded.  
  
That said, I can live with any decision.

---

## Comment 14

> Username: vinniefalco  
> Created at: 2020-06-12 17:14:36 UTC  
> Url: https://github.com/boostorg/beast/issues/1879#issuecomment-643390864  

it isn't meant for directories

---

## Comment 15

> Username: madmongo1  
> Created at: 2020-06-12 19:22:35 UTC  
> Updated at: 2020-06-12 19:22:51 UTC  
> Url: https://github.com/boostorg/beast/issues/1879#issuecomment-643444837  

Are you saying that if the call to open() fails, it should be a guarantee that is_open() should return false?  
This would seem reasonable to me.

---

## Comment 16

> Username: benstadin  
> Created at: 2020-06-12 21:21:00 UTC  
> Url: https://github.com/boostorg/beast/issues/1879#issuecomment-643488151  

Yes.

---

## Comment 17

> Username: ashtum  
> Created at: 2024-02-20 11:33:52 UTC  
> Updated at: 2024-02-20 11:41:34 UTC  
> Url: https://github.com/boostorg/beast/issues/1879#issuecomment-1954026208  

To check whether a file is a directory within the `open` function, an additional system call is necessary, such as `fstat` in POSIX or `GetFileAttributesA` in Win32. However, some users may find the added overhead of this step unacceptable because it might be unnecessary in their application.  
  
Given the possibility of accessing the underlying file handle, one can create a free function to perform the check:  
  
```C++  
bool is_directory(beast::file_posix::native_handle_type fd, beast::error_code& ec)  
{  
  struct stat st;  
  if (::fstat(fd, &st) != 0)  
  {  
    ec.assign(errno, beast::system_category());  
    return false;  
  }  
  
  return S_ISDIR(st.st_mode);  
}  
  
// Usage  
beast::error_code ec;  
if (is_directory(body.file().native_handle(), ec))  
{  
  // ...  
}  
```
