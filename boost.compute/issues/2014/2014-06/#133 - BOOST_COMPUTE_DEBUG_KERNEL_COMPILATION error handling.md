# #133 - BOOST_COMPUTE_DEBUG_KERNEL_COMPILATION error handling [Closed]

> Username: Mageswaran1989  
> Created at: 2014-06-07 15:33:44 UTC  
> Updated at: 2014-06-25 04:18:40 UTC  
> Closed at: 2014-06-25 04:18:40 UTC  
> Url: https://github.com/boostorg/compute/issues/133  

While I was trying with my faulty kernels, I got following boost exception   
  
terminate called after throwing an instance of 'boost::exception_detail::clone_implboost::exception_detail::error_info_injector<boost::compute::opencl_error >'  
  what():  Build Program Failure  
Aborted (core dumped)  
  
Reference:  
In file program.hpp Line: 208 to 223  
  
```  
    #ifdef BOOST_COMPUTE_DEBUG_KERNEL_COMPILATION  
    if(ret != CL_SUCCESS){  
        // print the error, source code and build log  
        std::cerr << "Boost.Compute: "  
                  << "kernel compilation failed (" << ret << ")\n"  
                  << "--- source ---\n"  
                  << source()  
                  << "\n--- build log ---\n"  
                  << build_log()  
                  << std::endl;  
    }  
    #endif  
  
    if(ret != CL_SUCCESS){  
        BOOST_THROW_EXCEPTION(opencl_error(ret));  
    }  
```  
  
So I tried to print build log, for my surprise it didn't work out. Later find out about the macro "BOOST_COMPUTE_DEBUG_KERNEL_COMPILATION", which then helped me.  
  
For a user, a build that fails in first case, he/she wont be able to get the log with build_log(), as the exception is thrown before that, unless the user figures out that there is a macro to be enabled (not difficult though!).  
  
A suggestion with following snippet...  
With this we will be able to remove iostream overhead!   
and I hope there wont be any API changes :)  
  
```  
/// Builds the program with \p options and get build logs at \p build_log.  
bool build(const std::string &options = std::string(), std::string *build_logs = NULL)  
{  
    const char *options_string = 0;  
  
    if(!options.empty()){  
        options_string = options.c_str();  
    }  
  
    cl_int ret = clBuildProgram(m_program, 0, 0, options_string, 0, 0);  
  
    if(ret != CL_SUCCESS){  
        if(build_logs != NULL){  
            *build_logs = build_log();  
            return false;  
        }  
        else  
         BOOST_THROW_EXCEPTION(opencl_error(ret));  
    }  
    return true;  
}  
```  
## Cases Covered:  
  
Exception is still maintained to know where OpenCL error occurs.  
When build fails, user can pass a empty options string and an build log string (knows through API signature only), to check the status of build.  
User can get failed build log without an extra macro  :)  
  
Eg:  
     //program.build(); //Throws error  
    if(!program.build(options, &build_logs))  
    {  
        std::cout<<"\n Build Log: "  
                <<build_logs  
               <<std::endl;  
        exit(-1);  
    }

---

## Comment 1

> Username: kylelutz  
> Created at: 2014-06-07 16:05:28 UTC  
> Url: https://github.com/boostorg/compute/issues/133#issuecomment-45413863  

You can just catch the error thrown from `program::build()` and then print out the build log there:  
  
```  
try {                                                                                                                                                                                                                                      
    program.build();                                                                                                                                                                                                                       
}                                                                                                                                                                                                                                          
catch(boost::compute::opencl_error &e){                                                                                                                                                                                                    
    std::cout << program.build_log() << std::endl;                                                                                                                                                                                         
}  
```

---

## Comment 2

> Username: Mageswaran1989  
> Created at: 2014-06-07 16:27:43 UTC  
> Url: https://github.com/boostorg/compute/issues/133#issuecomment-45414413  

That seems simple.  
I was thinking about that but didn't know what to catch? (In my next example I will add this)  
I came across in web, not to use try..cath{} blocks especially in embedded environment.   
What is your opinion on that?

---

## Comment 3

> Username: kylelutz  
> Created at: 2014-06-07 16:41:58 UTC  
> Url: https://github.com/boostorg/compute/issues/133#issuecomment-45414764  

Yeah, I'll work on documenting that better. In general, any Boost.Compute function which calls a OpenCL function that fails (i.e. doesn't return `CL_SUCCESS`) will throw a `opencl_error` which contains the OpenCL error code and a string describing the error.  
  
I think exceptions are the proper error handling mechanism to use in this case, especially for setup and initialization functions like `program::build()`.

---

## Comment 4

> Username: kylelutz  
> Created at: 2014-06-07 22:12:52 UTC  
> Url: https://github.com/boostorg/compute/issues/133#issuecomment-45422590  

I've updated the documentation for [`program::build()`](http://kylelutz.github.io/compute/boost/compute/program.html) to show how to catch and print out the build log if compilation fails.
