# #62 - Cant link libs with <boost/chrono.hpp> [Open]

> Username: Yachter  
> Created at: 2021-12-18 16:58:36 UTC  
> Updated at: 2021-12-18 17:23:07 UTC  
> Url: https://github.com/boostorg/chrono/issues/62  

Severity	Code	Description	Project	File	Line	Suppression State  
Error	LNK1104	  
cannot open file 'libboost_chrono-vc142-mt-sgd-x64-1_77.lib'	  
Boost_ASIO_HTTP_Async_Client	C:\Users\...\Console_boost_asio_http_async_client\LINK	1	  
  
The file 'libboost_chrono-vc142-mt-sgd-x64-1_77.lib' is really missing, but similar file exist:   
C:\Libs\boost_1_77_0\bin.v2\libs\chrono\build\msvc-14.2\debug\link-static\threading-multi\libboost_chrono-vc142-mt-gd-x64-1_77.lib    
  
Code:  
`    boost::chrono::system_clock::time_point before =  
        boost::chrono::system_clock::now();  
    //do some work  
    boost::chrono::system_clock::time_point now =  
        boost::chrono::system_clock::now();  
    boost::chrono::nanoseconds t = boost::chrono::duration_cast<boost::chrono::nanoseconds>(now - before);  
    std::cout << t.count() << "\n";  
`
