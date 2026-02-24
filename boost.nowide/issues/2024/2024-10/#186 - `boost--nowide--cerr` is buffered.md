# #186 - `boost::nowide::cerr` is buffered [Closed]

> Username: Flamefire  
> Created at: 2024-10-16 07:36:16 UTC  
> Updated at: 2024-10-19 12:02:16 UTC  
> Closed at: 2024-10-19 12:02:16 UTC  
> Url: https://github.com/boostorg/nowide/issues/186  

When using `std::cerr << "Something\n"` it behaves the same as `std::cerr << "Something" << std::endl`, i.e. any output is directly flushed/printed to the console.  
  
However there is some buffering happening such that if the application crashes or otherwise terminates it is not ensured that the output is/was actually visible.  
  
Investigate that e.g.[ the following](https://en.cppreference.com/w/cpp/io/cerr) holds:  
  
```  
Once initialized, (std::cerr.flags() & unitbuf) != 0 (same for std::wcerr) meaning that any output sent to these stream objects is immediately flushed to the OS (via [std::basic_ostream::sentry](https://en.cppreference.com/w/cpp/io/basic_ostream/sentry)'s destructor).  
  
In addition, std::cerr.tie() returns &[std::cout](http://en.cppreference.com/w/cpp/io/cout) (same for std::wcerr and [std::wcout](https://en.cppreference.com/w/cpp/io/cout)), meaning that any output operation on std::cerr first executes [std::cout](http://en.cppreference.com/w/cpp/io/cout).flush() (via [std::basic_ostream::sentry](https://en.cppreference.com/w/cpp/io/basic_ostream/sentry)'s constructor).  
```

---

## Comment 1

> Username: Flamefire  
> Created at: 2024-10-17 17:24:44 UTC  
> Url: https://github.com/boostorg/nowide/issues/186#issuecomment-2420099910  

This has been fixed for Boost 1.77 / Nowide 11.2 by 4ac0ca57b175561b1599a14f60b9b33a91df802a
