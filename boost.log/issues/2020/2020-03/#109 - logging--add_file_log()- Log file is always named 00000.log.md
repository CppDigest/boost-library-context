# #109 - logging::add_file_log(): Log file is always named 00000.log [Closed]

> Username: Gei0r  
> Created at: 2020-03-26 11:09:03 UTC  
> Updated at: 2020-03-26 11:43:35 UTC  
> Closed at: 2020-03-26 11:43:35 UTC  
> Url: https://github.com/boostorg/log/issues/109  

I'm using the following code straight from the [documentation](https://www.boost.org/doc/libs/1_72_0/libs/log/doc/html/log/detailed/utilities.html#log.detailed.utilities.setup.convenience):  
  
    // Initialize logging to the "test.log" file  
    logging::add_file_log("test.log");  
  
    // Here we go, we can write logs right away  
    src::logger lg;  
    BOOST_LOG(lg) << "Hello world!";  
  
When I execute this, the generated log file is always called `00000.log`. The parameter is not accepted. It doesn't work with the named parameter `file_name` either.  
  
[Complete Example on Coliru](http://coliru.stacked-crooked.com/a/37376f018b428e4e)

---

## Comment 1

> Username: Lastique  
> Created at: 2020-03-26 11:43:35 UTC  
> Url: https://github.com/boostorg/log/issues/109#issuecomment-604383485  

Duplicates https://github.com/boostorg/log/issues/104.
