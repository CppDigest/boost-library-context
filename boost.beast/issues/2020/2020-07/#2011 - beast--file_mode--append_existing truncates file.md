# #2011 - beast::file_mode::append_existing truncates file [Closed]

> Username: jade2k11598  
> Created at: 2020-07-08 19:35:04 UTC  
> Updated at: 2020-08-29 09:39:51 UTC  
> Closed at: 2020-08-29 09:39:51 UTC  
> Url: https://github.com/boostorg/beast/issues/2011  

I'm using boost version release 1.72, using g++ compiler with -std=c++14 and the following behavior was observed on linux and mac.  
  
I had been using `beast::http::file_body` as the body in POST requests (in combination with the 100-continue protocol) to transport files.  If for whatever reason that connectivity is lost mid-way to processing a POST request (e.g. only part of the file is received), a PATCH request would follow, once connectivity is established, to append to part of the file already transported.  I had noticed that after a PATCH request was processed and the file body was closed, the finished full size of the file was always the size of the file body that was sent with the PATCH request (the expected full size would be larger than this since the initial part of the file was initially sent by an earlier POST).  
  
For clarification, when sending out the POST request, the server end would open the received request's `beast::http::file_body` with mode `beast::file_mode::write` since this would be a new file, before the file body is sent.  But when a PATCH is received for an existing file, the server end would open the `beast::http::file_body` with file mode `beast::file_mode::append_existing`.  As [noted here](https://www.boost.org/doc/libs/develop/boost/beast/core/file_base.hpp) that for `append_existing`:  
  
> The current file position shall be set to the end of the file prior to each write.  
  
But from what I've observed, that is not the case.    
  
Since `using file_body = basic_file_body< file >;`, I decided to write a simplified test using `beast::file`, opening to this mode.  The following is my test code:  
  
```  
#include <sstream>  
#include <stdexcept>  
  
#include <boost/beast/core.hpp>  
  
  
void throwError(const std::string& msg,  
                const boost::system::error_code &ec)  
{  
    std::stringstream ss;  
    ss << msg << ":" << ec.message();  
    throw std::runtime_error(ss.str());  
}  
  
namespace beast = boost::beast;  
  
int main()  
{  
    std::string fileName("Mytest.txt");  
    beast::file f;  
    beast::error_code ec;  
    f.open(fileName.c_str(), beast::file_mode::write, ec);  
    if (ec)  
    {  
        throwError("Failed to open file", ec);  
    }  
  
    std::string msg("hello");  
    f.write(msg.c_str(), msg.length(), ec);  
    if (ec)  
    {  
        throwError("Failed to write to file", ec);  
    }  
  
    f.close(ec);  
    if (ec)  
    {  
        throwError("Failed to close 1st file", ec);  
    }  
  
    beast::file f2;  
    f2.open(fileName.c_str(), beast::file_mode::append_existing, ec);  
    if (ec)  
    {  
        throwError("Failed to open append to file", ec);  
    }  
  
    msg = " Jules";  
    f2.write(msg.c_str(), msg.length(), ec);  
    if (ec)  
    {  
        throwError("Failed to append to file", ec);  
    }  
    f2.close(ec);  
    if (ec)  
    {  
        throwError("Failed to close 2nd file", ec);  
    }  
  
    return 0;  
}  
```  
So the when the file `Mytest.txt` is initially opened, it is opened with `beast::file_mode::write` and writes to the file "hello".  That file is then closed.  A new instance of `beast::file` is instantiated (`f2`), opening the same file with mode `beast::file_mode::append_existing` and writes to the file " Jules" and then closes the file.  What one would have expected after running this test is a file that contains "hello Jules".  But instead just sees:  
```  
$ more Mytest.txt   
 Jules  
```  
which shows that the `beast::file_mode::append_existing` is in fact, truncating the file.

---

## Comment 1

> Username: madmongo1  
> Created at: 2020-07-21 15:25:47 UTC  
> Url: https://github.com/boostorg/beast/issues/2011#issuecomment-661929036  

Is this happening in Windows or Linux?

---

## Comment 2

> Username: jade2k11598  
> Created at: 2020-07-21 15:28:43 UTC  
> Url: https://github.com/boostorg/beast/issues/2011#issuecomment-661930761  

As I noted at the top of my thread, that I've only tested this on linux and mac.  I don't have windows.

---

## Comment 3

> Username: madmongo1  
> Created at: 2020-07-21 15:46:31 UTC  
> Url: https://github.com/boostorg/beast/issues/2011#issuecomment-661940968  

> As I noted at the top of my thread, that I've only tested this on linux and mac. I don't have windows.  
  
Yup, sorry. I'm not at my best today.

---

## Comment 4

> Username: stale[bot]  
> Created at: 2020-08-22 19:00:49 UTC  
> Url: https://github.com/boostorg/beast/issues/2011#issuecomment-678678964  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 5

> Username: vinniefalco  
> Created at: 2020-08-22 19:08:53 UTC  
> Url: https://github.com/boostorg/beast/issues/2011#issuecomment-678679971  

I thought this was fixed?

---

## Comment 6

> Username: madmongo1  
> Created at: 2020-08-22 19:10:11 UTC  
> Url: https://github.com/boostorg/beast/issues/2011#issuecomment-678680151  

We held off on merging some fixes because of the release
