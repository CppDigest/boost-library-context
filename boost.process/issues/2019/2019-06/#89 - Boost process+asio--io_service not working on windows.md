# #89 - Boost process+asio::io_service not working on windows [Closed]

> Username: kenkit  
> Created at: 2019-06-18 00:01:13 UTC  
> Updated at: 2019-06-19 19:56:45 UTC  
> Closed at: 2019-06-18 10:19:45 UTC  
> Url: https://github.com/boostorg/process/issues/89  

Hi,   
I've been trying to run this on windows but it doesn't i've tried almost all options but the process does not run   
  
```cpp  
std::tuple<boost::process::child,std::future<std::string>> start_process(std::string program, std::string params,bool wait)  
{  
  
boost::asio::io_service ios;  
std::tuple<boost::process::child, std::future<std::string>> new_process;  
  
  std::get<0>(new_process) = boost::process::child (program, params,  // set the input  
                                                 boost::process::std_in.close(),  
                                                 boost::process::std_out > std::get<1>(new_process),  // so it can be written without anything  
                                                 boost::process::std_err > std::get<1>(new_process), ios);  
  
 ios.run();  // this will actually block until the compiler is finished  
 if (wait) std::get<0>(new_process).wait();  
  
 return new_process;  
 }  
```

---

## Comment 1

> Username: kenkit  
> Created at: 2019-06-18 00:03:05 UTC  
> Url: https://github.com/boostorg/process/issues/89#issuecomment-502894182  

The program variable contains the fullpath to the program

---

## Comment 2

> Username: klemens-morgenstern  
> Created at: 2019-06-18 00:43:46 UTC  
> Url: https://github.com/boostorg/process/issues/89#issuecomment-502901751  

This is expected behavior, if you don't pass ‘ios‘ to the child constructor it will only block until the pipes are closed (!).

---

## Comment 3

> Username: kenkit  
> Created at: 2019-06-18 09:06:59 UTC  
> Url: https://github.com/boostorg/process/issues/89#issuecomment-503017141  

I did pass ios but the process is not starting.

---

## Comment 4

> Username: kenkit  
> Created at: 2019-06-18 10:19:45 UTC  
> Url: https://github.com/boostorg/process/issues/89#issuecomment-503043293  

Found the bug  
Apparantly this doesn't not work  
```cpp  
  std::get<0>(new_process) = boost::process::child (program, params,  // set the input  
```  
But this works  
```cpp  
  std::get<0>(new_process) = boost::process::child (program+ params,  // set the input  
```

---

## Comment 5

> Username: kenkit  
> Created at: 2019-06-18 11:31:43 UTC  
> Updated at: 2019-06-19 19:54:39 UTC  
> Url: https://github.com/boostorg/process/issues/89#issuecomment-503064722  

Although I cannot get the complete shell output.  
```cpp  
  std::get<0>(ipfs).wait();  
  std::cout <<"PROMISED:"<< std::get<1>(ipfs).get() << std::endl;  
```

---

## Comment 6

> Username: kenkit  
> Created at: 2019-06-19 17:38:15 UTC  
> Updated at: 2019-06-19 19:56:45 UTC  
> Url: https://github.com/boostorg/process/issues/89#issuecomment-503658891  

Good it turnsout my code is just fine :)  
EDIT:This is the modified code that works  
```cpp  
std::tuple<boost::process::child,std::future<std::string>>  UtilitiesPlugin::start_process(std::string program, std::string params,bool wait)  
{  
boost::asio::io_service ios;  
std::tuple<boost::process::child, std::future<std::string>> new_process;  
  
  std::get<0>(new_process) = boost::process::child (program+ params,  // set the input  
                                                 boost::process::std_in.close(),  
                                                 boost::process::std_out > std::get<1>(new_process),  // so it can be written without anything  
                                                 boost::process::std_err > std::get<1>(new_process), ios);  
if (wait) {  
 ios.run();  // this will actually block until the compiler is finished  
 std::get<0>(new_process).wait();  
}  
 return new_process;  
}  
```
