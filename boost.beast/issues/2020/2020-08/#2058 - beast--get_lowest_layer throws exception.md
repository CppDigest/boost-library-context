# #2058 - beast::get_lowest_layer throws exception [Closed]

> Username: GaneshTambat1  
> Created at: 2020-08-20 13:49:14 UTC  
> Updated at: 2020-10-12 00:33:03 UTC  
> Closed at: 2020-10-12 00:33:03 UTC  
> Url: https://github.com/boostorg/beast/issues/2058  

I am facing a really weird issue. I have the following code in my two apps which run on the same system.  In one app it runs perfectly fine but it crashes at "beast::get_lowest_layer(stream).connect(results);" continuously in the second app.  
  
Following is the same code present in two apps:  
***************  
        net::io_context ioc;  
        ssl::context ctx(ssl::context::tlsv12_client);  
        ctx.load_verify_file(strCertFile.c_str());  
        ctx.set_verify_mode(ssl::verify_peer);  
                  
        tcp::resolver resolver(ioc);  
        beast::ssl_stream<beast::tcp_stream> stream(ioc, ctx);  
  
        // Set SNI Hostname (many hosts need this to handshake successfully)  
        if (!SSL_set_tlsext_host_name(stream.native_handle(), strHost.c_str()))  
        {  
            beast::error_code ec{ static_cast<int>(::ERR_get_error()), net::error::get_ssl_category() };  
            throw beast::system_error{ ec };  
        }  
  
        // Look up the domain name  
        auto const results = resolver.resolve(strHost, strPort);  
  
        // Make the connection on the IP address we get from a lookup  
        beast::get_lowest_layer(stream).connect(results);  
***************  
  
  
It looks like in VC\Tools\MSVC\14.26.28801\include\memory file, it is going in loop in following function:  
***************  
template <class _Ty1, class _Ty2>  
_NODISCARD bool operator!=(const shared_ptr<_Ty1>& _Left, const shared_ptr<_Ty2>& _Right) noexcept {  
    return _Left != _Right;  
}  
**************  
  
  
The complete call stack is below: (pasted only bottom entries. Its a very huge call stack with possible loop.)  
**************  
 	TestAuth.exe!std::operator!=<std::vector<boost::asio::ip::basic_resolver_entry<boost::asio::ip::tcp>,std::allocator<boost::asio::ip::basic_resolver_entry<boost::asio::ip::tcp>>>,std::vector<boost::asio::ip::basic_resolver_entry<boost::asio::ip::tcp>,std::allocator<boost::asio::ip::basic_resolver_entry<boost::asio::ip::tcp>>>>(const std::shared_ptr<std::vector<boost::asio::ip::basic_resolver_entry<boost::asio::ip::tcp>,std::allocator<boost::asio::ip::basic_resolver_entry<boost::asio::ip::tcp>>>> & _Left, const std::shared_ptr<std::vector<boost::asio::ip::basic_resolver_entry<boost::asio::ip::tcp>,std::allocator<boost::asio::ip::basic_resolver_entry<boost::asio::ip::tcp>>>> & _Right) Line 1302	C++  
 	TestAuth.exe!std::operator!=<std::vector<boost::asio::ip::basic_resolver_entry<boost::asio::ip::tcp>,std::allocator<boost::asio::ip::basic_resolver_entry<boost::asio::ip::tcp>>>,std::vector<boost::asio::ip::basic_resolver_entry<boost::asio::ip::tcp>,std::allocator<boost::asio::ip::basic_resolver_entry<boost::asio::ip::tcp>>>>(const std::shared_ptr<std::vector<boost::asio::ip::basic_resolver_entry<boost::asio::ip::tcp>,std::allocator<boost::asio::ip::basic_resolver_entry<boost::asio::ip::tcp>>>> & _Left, const std::shared_ptr<std::vector<boost::asio::ip::basic_resolver_entry<boost::asio::ip::tcp>,std::allocator<boost::asio::ip::basic_resolver_entry<boost::asio::ip::tcp>>>> & _Right) Line 1302	C++  
 	TestAuth.exe!std::operator!=<std::vector<boost::asio::ip::basic_resolver_entry<boost::asio::ip::tcp>,std::allocator<boost::asio::ip::basic_resolver_entry<boost::asio::ip::tcp>>>,std::vector<boost::asio::ip::basic_resolver_entry<boost::asio::ip::tcp>,std::allocator<boost::asio::ip::basic_resolver_entry<boost::asio::ip::tcp>>>>(const std::shared_ptr<std::vector<boost::asio::ip::basic_resolver_entry<boost::asio::ip::tcp>,std::allocator<boost::asio::ip::basic_resolver_entry<boost::asio::ip::tcp>>>> & _Left, const std::shared_ptr<std::vector<boost::asio::ip::basic_resolver_entry<boost::asio::ip::tcp>,std::allocator<boost::asio::ip::basic_resolver_entry<boost::asio::ip::tcp>>>> & _Right) Line 1302	C++  
 	TestAuth.exe!boost::asio::ip::basic_resolver_iterator<boost::asio::ip::tcp>::equal(const boost::asio::ip::basic_resolver_iterator<boost::asio::ip::tcp> & other) Line 172	C++  
 	TestAuth.exe!boost::asio::ip::operator!=(const boost::asio::ip::basic_resolver_iterator<boost::asio::ip::tcp> & a, const boost::asio::ip::basic_resolver_iterator<boost::asio::ip::tcp> & b) Line 154	C++  
 	TestAuth.exe!boost::asio::connect<boost::asio::ip::tcp,boost::asio::executor,boost::asio::ip::basic_resolver_iterator<boost::asio::ip::tcp>,boost::asio::detail::default_connect_condition>(boost::asio::basic_socket<boost::asio::ip::tcp,boost::asio::executor> & s, boost::asio::ip::basic_resolver_iterator<boost::asio::ip::tcp> begin, boost::asio::ip::basic_resolver_iterator<boost::asio::ip::tcp> end, boost::asio::detail::default_connect_condition connect_condition, boost::system::error_code & ec) Line 235	C++  
 	TestAuth.exe!boost::asio::connect<boost::asio::ip::tcp,boost::asio::executor,boost::asio::ip::basic_resolver_results<boost::asio::ip::tcp>>(boost::asio::basic_socket<boost::asio::ip::tcp,boost::asio::executor> & s, const boost::asio::ip::basic_resolver_results<boost::asio::ip::tcp> & endpoints, boost::system::error_code & ec, void * __formal) Line 123	C++  
 	TestAuth.exe!boost::asio::connect<boost::asio::ip::tcp,boost::asio::executor,boost::asio::ip::basic_resolver_results<boost::asio::ip::tcp>>(boost::asio::basic_socket<boost::asio::ip::tcp,boost::asio::executor> & s, const boost::asio::ip::basic_resolver_results<boost::asio::ip::tcp> & endpoints, void * __formal) Line 113	C++  
 	TestAuth.exe!boost::beast::basic_stream<boost::asio::ip::tcp,boost::asio::executor,boost::beast::unlimited_rate_policy>::connect<boost::asio::ip::basic_resolver_results<boost::asio::ip::tcp>,void>(const boost::asio::ip::basic_resolver_results<boost::asio::ip::tcp> & endpoints) Line 596	C++  
 	TestAuth.exe!OAuthImpl::RequestToken() Line 113	C++ // This is my function  
**************  
  
Any help on this is much appreciated.  
  
Regards,  
Ganesh  
  
  
PLEASE DON'T FORGET TO "STAR" THIS REPOSITORY :)  
  
If you rather keep your project secret, feel free  
to email the author at `<vinnie.falco@gmail.com>`; any  
private correspondence is treated as confidential.  
  
When reporting a bug please include the following:  
  
### Version of Beast  
  
You can find the version number in the file `<boost/beast/version.hpp>`,  
or by using the command "git log -1" in the local Beast repository  
directory.  
  
### Steps necessary to reproduce the problem  
  
A small compiling program is the best. If your code is  
public, you can provide a link to the repository.  
  
### All relevant compiler information  
  
If you are unable to compile please include the type and  
version of compiler you are using as well as all compiler  
output including the error message, file, and line numbers  
involved.  
  
The more information you provide the sooner your issue  
can get resolved!

---

## Comment 1

> Username: GaneshTambat1  
> Created at: 2020-08-20 14:53:15 UTC  
> Url: https://github.com/boostorg/beast/issues/2058#issuecomment-677714640  

My boost version is 1.73. I have also tried with 1.72 and have seen the same issue.

---

## Comment 2

> Username: madmongo1  
> Created at: 2020-08-20 15:09:05 UTC  
> Url: https://github.com/boostorg/beast/issues/2058#issuecomment-677723903  

Are you getting a crash or an exception?

---

## Comment 3

> Username: GaneshTambat1  
> Created at: 2020-08-20 16:07:57 UTC  
> Url: https://github.com/boostorg/beast/issues/2058#issuecomment-677757483  

Getting unhandled exception.

---

## Comment 4

> Username: GaneshTambat1  
> Created at: 2020-08-20 16:28:14 UTC  
> Url: https://github.com/boostorg/beast/issues/2058#issuecomment-677768369  

I am seeing an unhandled exception in debug build. In release build, it just hangs. So I suspect this is some infinite loop (maybe related to string arguments) but I am not sure why it doesn't happen with other app with the same code and parameters.

---

## Comment 5

> Username: madmongo1  
> Created at: 2020-08-20 16:45:00 UTC  
> Url: https://github.com/boostorg/beast/issues/2058#issuecomment-677776998  

Have you caught the exception and printed it?

---

## Comment 6

> Username: madmongo1  
> Created at: 2020-08-20 16:52:18 UTC  
> Url: https://github.com/boostorg/beast/issues/2058#issuecomment-677780965  

Honestly, it sounds like there is some code that you are not showing me that is causing undefined behaviour.

---

## Comment 7

> Username: GaneshTambat1  
> Created at: 2020-08-20 16:56:15 UTC  
> Updated at: 2020-08-20 16:56:42 UTC  
> Url: https://github.com/boostorg/beast/issues/2058#issuecomment-677782975  

Ok. Which part of the code do you want? Right now Host, post, and cert file name all are hardcoded in the code.   
  
Again pasting the code block with hardcoded value. In the initial configuration only the exceptions are seen.  
  
        std::string strCertFile = "F:\\cacert.pem";  
        std::string strHost = "login.microsoftonline.com";  
        std::string strPort = "443";  
  
        net::io_context ioc;  
        ssl::context ctx(ssl::context::tlsv12_client);  
        ctx.load_verify_file(strCertFile.c_str());  
        ctx.set_verify_mode(ssl::verify_peer);  
  
        tcp::resolver resolver(ioc);  
        beast::ssl_stream<beast::tcp_stream> stream(ioc, ctx);  
  
        // Set SNI Hostname (many hosts need this to handshake successfully)  
        if (!SSL_set_tlsext_host_name(stream.native_handle(), strHost.c_str()))  
        {  
            beast::error_code ec{ static_cast<int>(::ERR_get_error()), net::error::get_ssl_category() };  
            throw beast::system_error{ ec };  
        }  
  
        // Look up the domain name  
        auto const results = resolver.resolve(strHost, strPort);  
  
        // Make the connection on the IP address we get from a lookup  
        beast::get_lowest_layer(stream).connect(results);  
  
        // Perform the SSL handshake  
        stream.handshake(ssl::stream_base::client);

---

## Comment 8

> Username: GaneshTambat1  
> Created at: 2020-08-20 16:57:23 UTC  
> Url: https://github.com/boostorg/beast/issues/2058#issuecomment-677783535  

This code is taken from a published Beast sample on GitHub.

---

## Comment 9

> Username: madmongo1  
> Created at: 2020-08-20 16:58:40 UTC  
> Url: https://github.com/boostorg/beast/issues/2058#issuecomment-677784201  

can you catch the exception, determine its type and print its message, and it relevant, error_code?

---

## Comment 10

> Username: GaneshTambat1  
> Created at: 2020-08-20 17:13:10 UTC  
> Url: https://github.com/boostorg/beast/issues/2058#issuecomment-677791253  

Exception details from debugger:  
Unhandled exception at 0x00007FF77D2D8D45 in TestAuth.exe: 0xC00000FD: Stack overflow (parameters: 0x0000000000000001, 0x000000DC85C03F80). occurred  
  
The following try catch block doesn't catch this exception:  
         try  
        {              
            beast::get_lowest_layer(stream).connect(results);  
        }  
        catch (std::exception const& e)  
        {  
            std::cerr << "Error: " << e.what() << std::endl;  
            return false;  
        }  
  
Visual Studio caught the exception (not the above catch block).  
  
Like I have mentioned in my first comment the stack is probably getting full because of the infinite loop.

---

## Comment 11

> Username: madmongo1  
> Created at: 2020-08-20 17:15:38 UTC  
> Url: https://github.com/boostorg/beast/issues/2058#issuecomment-677792476  

Hmm. Did you copy the example exactly? I see that you set the TLS ext_host_name before connecting the underlying socket.

---

## Comment 12

> Username: GaneshTambat1  
> Created at: 2020-08-20 17:18:58 UTC  
> Url: https://github.com/boostorg/beast/issues/2058#issuecomment-677794010  

I am referring this: https://github.com/boostorg/beast/blob/develop/example/http/client/sync-ssl/http_client_sync_ssl.cpp  
  
And the flow seems similar.

---

## Comment 13

> Username: madmongo1  
> Created at: 2020-08-20 17:21:26 UTC  
> Url: https://github.com/boostorg/beast/issues/2058#issuecomment-677795196  

OK, can we try a couple of things?  
  
first let's put in a loop to print the endpoints discovered by the resolver to be sure there's no infinite loop:  
  
`for(auto entry : results) { std::cout << entry.endpoint() << std::endl; }`  
  
Then, let's move the tls_ext_host_name until after the tcp connect.

---

## Comment 14

> Username: GaneshTambat1  
> Created at: 2020-08-20 17:31:25 UTC  
> Url: https://github.com/boostorg/beast/issues/2058#issuecomment-677799988  

The for loop is throwing the stackoverflow exception.   
for(auto entry : results) { std::cout << entry.endpoint() << std::endl; }  
  
So looks like something wrong is fetched as the output of resolver. Is there any particular field from the results you want me to check?  
  
Relevant code block with try-catch block.  
  
            try {  
            // Look up the domain name  
            auto const results = resolver.resolve(strHost, strPort);  
  
            for (auto entry : results) { std::cout << entry.endpoint() << std::endl; }  
         
            // Make the connection on the IP address we get from a lookup  
            beast::get_lowest_layer(stream).connect(results);  
        }  
        catch (std::exception const& e){  
            std::cerr << "Error: " << e.what() << std::endl;  
            return false;  
        }

---

## Comment 15

> Username: GaneshTambat1  
> Created at: 2020-08-21 05:58:40 UTC  
> Updated at: 2020-08-21 05:58:52 UTC  
> Url: https://github.com/boostorg/beast/issues/2058#issuecomment-678056075  

Let me know if there are any suggestions to debug and resolve this issue.

---

## Comment 16

> Username: madmongo1  
> Created at: 2020-08-21 11:02:09 UTC  
> Updated at: 2020-08-21 11:02:28 UTC  
> Url: https://github.com/boostorg/beast/issues/2058#issuecomment-678234025  

It wouldn't hurt to print all the members of `entry`.  
  
It's extremely strange that you're getting an invalid resolver results object. I would expect an exception and no object returned if there was a problem with DNS lookup. We need to know as much about that object as possible, including perhaps its address and memory contents.  
  
Are you sure the resolver object is valid?

---

## Comment 17

> Username: GaneshTambat1  
> Created at: 2020-08-21 15:37:18 UTC  
> Url: https://github.com/boostorg/beast/issues/2058#issuecomment-678355430  

![BeastError](https://user-images.githubusercontent.com/44489505/90907795-0d6b9800-e3f1-11ea-8073-8f5fb2572a29.png)  
  
The exception comes when first value of results is getting assigned to entry. Code for your reference below:  
  
        auto const results = resolver.resolve(GetHost(), GetPort());  // GetHost and GetPort return const std::string &  
        for (auto entry : results)   
        {   
            std::cout << entry.endpoint() << std::endl;   
        }  
  
So entry doesn't get assigned any value and we see the exception.  
  
I have attached values shown in the watch window of "results" object. I think there is some issue in getting the IPs associated with the host and that is causing the issues. Instead of IPs I can see many values with "\x1".   
  
Now I am not sure what exactly is the problem or solution for it. And I would expect library calls to come out of this situation and not result in stack overflow or hang.

---

## Comment 18

> Username: madmongo1  
> Created at: 2020-08-21 20:07:59 UTC  
> Updated at: 2020-08-21 20:09:25 UTC  
> Url: https://github.com/boostorg/beast/issues/2058#issuecomment-678470690  

The entries in the results object look reasonable. Remember that they are in network byte order (big-endian).  
  
There must be a problem somewhere else.  
  
Evidence:  
```  
$ nslookup login.microsoftonline.com  
Server:		192.168.0.1  
Address:	192.168.0.1#53  
  
Non-authoritative answer:  
login.microsoftonline.com	canonical name = a.privatelink.msidentity.com.  
a.privatelink.msidentity.com	canonical name = prda.aadg.msidentity.com.  
prda.aadg.msidentity.com	canonical name = www.tm.a.prd.aadg.akadns.net.  
Name:	www.tm.a.prd.aadg.akadns.net  
Address: 40.126.9.66  
Name:	www.tm.a.prd.aadg.akadns.net  
Address: 40.126.9.77  
Name:	www.tm.a.prd.aadg.akadns.net  
Address: 20.190.137.6  
Name:	www.tm.a.prd.aadg.akadns.net  
Address: 20.190.137.96  
Name:	www.tm.a.prd.aadg.akadns.net  
Address: 20.190.137.75  
Name:	www.tm.a.prd.aadg.akadns.net  
Address: 20.190.137.98  
Name:	www.tm.a.prd.aadg.akadns.net  
Address: 40.126.9.8  
Name:	www.tm.a.prd.aadg.akadns.net  
Address: 40.126.9.73  
```

---

## Comment 19

> Username: GaneshTambat1  
> Created at: 2020-08-23 17:41:17 UTC  
> Url: https://github.com/boostorg/beast/issues/2058#issuecomment-678803050  

C:\Users\Administrator>nslookup login.microsoftonline.com  
Server:  UnKnown  
Address:  10.210.76.202  
  
Non-authoritative answer:  
Name:    www.tm.a.prd.aadg.akadns.net  
Addresses:  40.126.5.100  
          40.126.5.98  
          40.126.5.64  
          40.126.5.33  
          40.126.5.35  
          20.190.133.76  
          20.190.133.78  
          20.190.133.75  
Aliases:  login.microsoftonline.com  
          a.privatelink.msidentity.com  
          prda.aadg.msidentity.com  
  
Nslookup output from the system on which I am seeing this issue. Is there anything we can do to debug this further?

---

## Comment 20

> Username: madmongo1  
> Created at: 2020-08-24 08:41:47 UTC  
> Url: https://github.com/boostorg/beast/issues/2058#issuecomment-678992967  

I am interested to know whether there is anything unusual about your setup. It's very strange that the resolve return result seems valid when we look at the debugger and yet you get a crash when you try to inspect it.  
  
Something like a difference in the way two compilation units or libraries have been compiled? e.g. shared c++ runtime library vs static?

---

## Comment 21

> Username: stale[bot]  
> Created at: 2020-10-04 03:01:33 UTC  
> Url: https://github.com/boostorg/beast/issues/2058#issuecomment-703193599  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 22

> Username: stale[bot]  
> Created at: 2020-10-12 00:32:56 UTC  
> Url: https://github.com/boostorg/beast/issues/2058#issuecomment-706795739  

It looks like this issue has either been abandoned or resolved so I will go ahead and close it. Feel free to re-open this issue if you feel it needs attention, or open new issues as needed. Thanks!
