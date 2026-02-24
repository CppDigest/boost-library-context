# #46 - Win32's system_category does not handle boost::winapi::ERROR_CONNECTION_ABORTED_ [Closed]

> Username: KABoissonneault  
> Created at: 2019-05-13 21:44:44 UTC  
> Updated at: 2019-05-14 01:15:47 UTC  
> Closed at: 2019-05-14 01:15:47 UTC  
> Url: https://github.com/boostorg/system/issues/46  

boost::winapi::ERROR_CONNECTION_ABORTED_ may be returned by certain Windows functions, which is separate from WSACONNABORTED_. ERROR_CONNECTION_ABORTED_ should be equivalent to errc::connection_aborted
