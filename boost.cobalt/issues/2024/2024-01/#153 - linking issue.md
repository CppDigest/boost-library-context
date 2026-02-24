# #153 - linking issue [Closed]

> Username: teathinker  
> Created at: 2024-01-24 02:58:37 UTC  
> Updated at: 2024-01-24 04:58:16 UTC  
> Closed at: 2024-01-24 04:58:15 UTC  
> Url: https://github.com/boostorg/cobalt/issues/153  

Hey,  
  
After download, compile and install boost 1.84.0, the example couldn't find cobalt to link.  
some error was like : main.cpp:(.text+0x17c3): undefined reference to `boost::cobalt::this_thread::get_executor(boost::source_location const&)'  
  
My understanding is that this project isn't built by boost yet so I have to manually build it?  
And it only provides static linking for now?  
  
Thanks

---

## Comment 1

> Username: teathinker  
> Created at: 2024-01-24 04:58:16 UTC  
> Url: https://github.com/boostorg/cobalt/issues/153#issuecomment-1907377211  

figured out, I need to choose a newer standard:)
