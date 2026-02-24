# #367 - Error C2039: 'category': is not a member of 'boost::mpl::void_'. [Closed]

> Username: Brijgopal  
> Created at: 2020-10-29 13:33:35 UTC  
> Updated at: 2020-12-30 01:14:55 UTC  
> Closed at: 2020-12-30 01:14:54 UTC  
> Url: https://github.com/boostorg/asio/issues/367  

Problem statement - \boost\iostreams\traits.hpp(218,46): error C2039: 'category': is not a member of 'boost::mpl::void_'.  
  
Description : We are executing the project on VS 2019 with Boost 1.74 version. Earlier we were using the boost 1.45 for the same project on VS2010. But now we have upgraded the boost1.45 to boost 1.74 to run the project on VS2019 and we have found that there are many changes have been done in boost . In our project there are some Third party headers with name like xyz_stream_handle.hpp and xyz_stream_handle_service.hpp that are derived from boost headers.Third party headers using the "Stream_handle_service.hpp" file that is deprecated . So we have removed it -  
  
This is our xyz_stream_handle_service.hpp file -  
  
class xyz_stream_handle_service : public stream_handle_service  
{  
public: explicit xyz_stream_handle_service(boost::asio::io_context& io_service)  
    : stream_handle_service(io_service)  
    , iocp_service_(use_service<detail::win_iocp_io_service>(io_service))  
    {  
    }  
---  
---  
}  
  
We change this code snippet in our project  after removing stream_handle_service class.  
class xyz_stream_handle_service : public any_io_executor()  
{  
public : explicit xyz_stream_handle_service( boost::asio::io_context::executor_type io_service)        //change io_service to io_context    
    : any_io_executor()  
{  
}  
  
  
}  
  
After this change we got an error -  
Error C2039 'char_type': is not a member of 'boost::asio::windows::xyz_stream_handle' pointing to boost\iostreams\traits.hpp 179  
  
  
This is xyz_stream_handle.hpp file -  
class xyz_stream_handle : public basic_stream_handle< xyz_stream_handle_service >  
{  
  
public:  
   typedef  char char_type;      // we define this here to resolve the error C2039 mentioned above  
   // typedef boost::iostreams::output_filter_tag  category;  
     
    public : explicit xyz_stream_handle( ::boost::asio::io_context & ioSrvc )  
    : basic_stream_handle< xyz_stream_handle_service >( ioSrvc )  
    {  
    }  
----  
---  
}  
  
After these changes we are getting the error - \boost\iostreams\traits.hpp(218,46): error C2039: 'category': is not a member of 'boost::mpl::void_'  
  
We have also tried to define 'category' in xyz_stream_handle.hpp file as commented in above code snippet but  error is still same.  
  
Our query -  
  
    1. Is it correct to use "any_io_executor" to use in place of stream_handle_service class as we have used in our files above.  
    2. What we have to do to resolve this "category" type error.

---

## Comment 1

> Username: ghost  
> Created at: 2020-12-30 01:14:53 UTC  
> Url: https://github.com/boostorg/asio/issues/367#issuecomment-752293748  

This issue was moved by [chriskohlhoff](https://github.com/chriskohlhoff) to [chriskohlhoff/asio#768](https://github.com/chriskohlhoff/asio/issues/768).
