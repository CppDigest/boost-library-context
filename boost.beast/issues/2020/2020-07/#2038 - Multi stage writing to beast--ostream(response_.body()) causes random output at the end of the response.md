# #2038 - Multi stage writing to beast::ostream(response_.body()) causes random output at the end of the response [Closed]

> Username: menkaur  
> Created at: 2020-07-26 02:02:39 UTC  
> Updated at: 2020-09-05 19:10:09 UTC  
> Closed at: 2020-09-05 19:10:09 UTC  
> Url: https://github.com/boostorg/beast/issues/2038  

I've found this issue in   
BOOST_BEAST_VERSION 277  
  
I took code from following example: https://www.boost.org/doc/libs/master/libs/beast/example/http/server/small/http_server_small.cpp  
  
instead of directly writing to beast::ostream(response_.body()), I created variable  
auto outputStream=beast::ostream(response_.body());  
and than proceeded writing to it several distinct times.  
  
As result, http response contained invalid data after html body, probably unused stream buffer

---

## Comment 1

> Username: vinniefalco  
> Created at: 2020-07-27 19:19:47 UTC  
> Url: https://github.com/boostorg/beast/issues/2038#issuecomment-664588888  

Can you show us your exact changes to the code?

---

## Comment 2

> Username: menkaur  
> Created at: 2020-07-27 19:36:47 UTC  
> Updated at: 2020-07-27 19:37:35 UTC  
> Url: https://github.com/boostorg/beast/issues/2038#issuecomment-664596731  

I've already changed the code on my end to avoid the bug, it looks like this now:  
  
````  
std::stringstream outputStream;                	  
                          
                        outputStream << "<html>"<<std::endl;  
                        outputStream << "<head><title>"<< myTitle <<"</title>"<<  
                            R"delimiter(<script type="text/javascript">window.setTimeout(function(){ document.location.reload(true); }, 15000);</script>)delimiter"  
                            <<"</head>" << std::endl;  
                        outputStream << "<body>" << std::endl;  
  
                        if (!data.empty())   
                        {  
                            outputStream << "<table>" << std::endl;  
  
                            //after this, I'm writing table head and than table content, line by line  
  
                            outputStream << "</table>" << std::endl;  
                        }  
                        else  
                        {  
                            outputStream << "<h1>Nothing to report yet</h1>"<<std::endl;  
                        }  
                        outputStream << "</body>" << std::endl;  
                        outputStream << "</html>" << std::endl;  
  
                        beast::ostream(response_.body()) << outputStream.str();  
````  
  
The way I remember it, the code that produced the bug  didn't have the last line, and instead of `std::stringstream outputStream;` I wrote `auto outputStream=beast::ostream(response_.body());`

---

## Comment 3

> Username: stale[bot]  
> Created at: 2020-08-29 10:13:40 UTC  
> Url: https://github.com/boostorg/beast/issues/2038#issuecomment-683269359  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 4

> Username: stale[bot]  
> Created at: 2020-09-05 19:10:07 UTC  
> Url: https://github.com/boostorg/beast/issues/2038#issuecomment-687650043  

It looks like this issue has either been abandoned or resolved so I will go ahead and close it. Feel free to re-open this issue if you feel it needs attention, or open new issues as needed. Thanks!
