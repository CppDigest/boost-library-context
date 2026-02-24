# #727 - Mismatched _WIN32_WINNT version between boost::log and boost::asio on Windows >= 8 environments. [Open]

> Username: Adnn  
> Created at: 2023-01-09 11:39:35 UTC  
> Updated at: 2023-01-09 11:42:03 UTC  
> Url: https://github.com/boostorg/boost/issues/727  

I had to hunt down a link error today, and I am taking some time to report it here.  
  
On a Windows 10 environment, I am building a project with two distinct libraries:  
* `Library A` links against `boost::log`  
* `Library B` links against `boost::asio` and `library A`  
  
`Library B` fails to link, with the following linker error:  
> B.lib: error LNK2038: mismatch detected for 'boost_log_abi': value 'v2s_mt_nt6' doesn't match v  
alue 'v2s_mt_nt62' in A.lib  
  
As far as I understand, this is because `boost::log` determines a "default" Windows version of **0x0602** ([here](https://github.com/boostorg/log/blob/develop/include/boost/log/detail/config.hpp#L362)) when compiling on Windows >= 8,  
whereas `boost::asio` assumes a default Windows version of **0x0601** ([here](https://github.com/boostorg/asio/blob/boost-1.81.0/include/boost/asio/detail/config.hpp#L1562)).  
  
Users can solve this issue by setting an explicit Windows API version, yet it could be preferable if it also linked with default values.
