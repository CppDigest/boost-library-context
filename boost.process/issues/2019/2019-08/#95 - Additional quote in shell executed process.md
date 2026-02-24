# #95 - Additional quote in shell executed process [Open]

> Username: ghost  
> Created at: 2019-08-16 13:57:06 UTC  
> Updated at: 2020-08-04 19:02:53 UTC  
> Url: https://github.com/boostorg/process/issues/95  

https://github.com/boostorg/process/blob/6ccce9104ae91f6d07ac64c6d4e5fc0b57a649fe/include/boost/process/detail/posix/basic_cmd.hpp#L142  
The quote around the last argument makes the shell interpret the entire command as a single command, unless it is quoted again, hence running `boost::process::system("echo any cmd", boost::process::shell)` will not work as expected.  
  
I currently am using [this](https://github.com/w1d3m0d3/mpdfm/blob/1780495701d994a1add0122678f5399ce6c88519/src/config/config_file.cpp#L168-L178) as a workaround.
