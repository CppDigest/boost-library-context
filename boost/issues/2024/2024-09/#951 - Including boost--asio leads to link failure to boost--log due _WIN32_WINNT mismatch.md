# #951 - Including boost::asio leads to link failure to boost::log due _WIN32_WINNT mismatch [Open]

> Username: lucaskdc  
> Created at: 2024-09-14 19:13:19 UTC  
> Updated at: 2025-07-23 20:14:37 UTC  
> Url: https://github.com/boostorg/boost/issues/951  

Starting from version 1.84.0, boost::winapi changed the default value of BOOST_USE_WINAPI_VERSION to BOOST_WINAPI_VERSION_WIN10 (0x0A00) when _WIN32_WINNT is undefined. [Check here](https://github.com/boostorg/winapi/commit/85d7889e9b90c3c35bc592013b942f72d5475baf)  
  
This change causes boost::log to be built with a different namespace beginning in version 1.84.0. [Check here](https://github.com/boostorg/log/blob/028247d2e6f4d55713d26c0250ce9b762148db2c/include/boost/log/detail/config.hpp#L355-L358)  
  
Since winapi sets BOOST_USE_WINAPI_VERSION based on _WIN32_WINNT, and asio sets _WIN32_WINNT to 0x0601 by default,  
[Check here](https://github.com/chriskohlhoff/asio/blob/efdc25ab99786101351a5afb39f01dfaf0781401/asio/include/asio/detail/config.hpp#L747-L759), if asio is included before log, the application will attempt to resolve boost::log symbols in the wrong namespace.  
  
As a result, the headers are misinterpreted and do not match the compiled version of boost::log. This leads to link-time failures, and developers may encounter errors such as:  
`error LNK2038: mismatch detected for 'boost_log_abi': value 'v2s_mt_nt6' doesn't match value 'v2s_mt_nt62'`  
`error LNK2001: unresolved external symbol "public: static void __cdecl boost::log::v2s_mt_nt6::record_view::public_data::destroy(struct boost::log::v2s_mt_nt6::record_view::public_data const *)" (?destroy@public_data@record_view@v2s_mt_nt6@log@boost@@SAXPEBU12345@@Z)`  
  
Workaround:  
Set the compiler definition of _WIN32_WINNT to any value ≥ 0x0602, such as 0x0A00 (which corresponds to Windows 10).
