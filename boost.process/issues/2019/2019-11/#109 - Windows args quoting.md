# #109 - Windows args quoting [Open]

> Username: jkammerer  
> Created at: 2019-11-06 12:57:31 UTC  
> Updated at: 2019-11-06 12:57:31 UTC  
> Url: https://github.com/boostorg/process/issues/109  

`bp::detail::windows::build_args("echo", {"trailing backspace and spaces\\"})`  
returns:  
`echo "trailing backspace and spaces\"`  
Note that the closing quote that is inserted by boost::process is escaped.  
According to [this microsoft blog post](https://blogs.msdn.microsoft.com/twistylittlepassagesallalike/2011/04/23/everyone-quotes-command-line-arguments-the-wrong-way/) it should be:  
`echo "traling backspace and spaces\\"`  
  
Also:  
`bp::detail::windows::build_args("echo", {"\\\" backslash in front of quote"})`  
returns:  
`echo "\\" backslash in front of quote"`  
According to the blog it should be:  
`echo "\\\" backslash in front of quote"`  
  
As a workaround, i am assembling and escaping the command string myself as described in the above blog post instead of using the `bp::args()` interface.
