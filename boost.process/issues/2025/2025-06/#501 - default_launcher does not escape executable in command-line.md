# #501 - [windows] default_launcher does not escape executable in command-line [Closed]

> Username: melak47  
> Created at: 2025-06-29 00:40:29 UTC  
> Updated at: 2025-07-01 10:59:21 UTC  
> Closed at: 2025-07-01 10:59:21 UTC  
> Url: https://github.com/boostorg/process/issues/501  

When `args` is empty, [`boost::process::v2::windows::default_launcher::build_command_line`](  
https://github.com/boostorg/process/blob/develop/include/boost/process/v2/windows/default_launcher.hpp#L403-L409) just returns the executable (`pt`) as the command-line string:  
```cpp  
if (std::begin(args) == std::end(args))  
  return pt.native();  
  
return build_command_line_impl(pt, args, *std::begin(args));  
```  
  
Unfortunately, this skips any escaping; if `pt` is a path containing spaces,  
like `D:\example path\example.exe`, it will get passed verbatim and arrives as two arguments in the child process' `argv`.  
  
When args is not empty, both `args` and `pt` get escaped by `build_command_line_impl`.  
  
Something like this seems to work:  
  
```cpp  
if (std::begin(args) == std::end(args)) {  
  std::wstring buffer;  
  buffer.resize(escaped_argv_length(pt.native()));  
  escape_argv_string(buffer.data(), buffer.size(), pt.native());  
  return buffer;  
}  
```  
  
but it might be nicer to change `build_command_line_impl` a little so it can be called even with an empty `args` sequence, and remove the special casing here.
