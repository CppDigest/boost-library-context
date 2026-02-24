# #62 - Confusing behavior when order of long/short options are swapped [Open]

> Username: mossheim  
> Created at: 2018-09-13 02:34:25 UTC  
> Updated at: 2018-09-13 02:34:25 UTC  
> Url: https://github.com/boostorg/program_options/issues/62  

Adding an option like this:  
  
```cpp  
desc.add_options()( "h,help", "Print help and exit" );  
```  
  
In a simple test program and then trying to use `--help` results in a confusing error:  
  
```  
$ ./prog --help  
libc++abi.dylib: terminating with uncaught exception of type boost::exception_detail::clone_impl<boost::exception_detail::error_info_injector<boost::program_options::unknown_option> >: unrecognised option '--help'  
```  
  
The [documentation](https://www.boost.org/doc/libs/1_66_0/doc/html/boost/program_options/option_description.html) for `option_description::option_description(const char*, const value_semantic*)` says:  
  
> The 'name' parameter is interpreted by the following rules:  
>    if there's no "," character in 'name', it specifies long name  
>    otherwise, the part before "," specifies long name and the part after -- short name.  
  
  
However, it is easy for new users of this library to think that swapping them is OK.  
  
The reason for this limitation isn't clear to me -- presumably, if one is "long" and the other "short", providing them in either order should be OK, and a simple test can tell which is which. In either case, the error message on misuse is needlessly cryptic and could probably be turned into an error thrown from `option_description::option_description`. This seems reasonable, since using `"h,help"` as the option name results in bad behavior regardless: the resulting executable has options `--h` and `-h`.
