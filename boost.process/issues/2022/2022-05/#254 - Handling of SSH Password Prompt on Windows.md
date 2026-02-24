# #254 - Handling of SSH Password Prompt on Windows [Open]

> Username: SeeRich  
> Created at: 2022-05-24 13:23:21 UTC  
> Updated at: 2022-08-30 09:14:29 UTC  
> Url: https://github.com/boostorg/process/issues/254  

This is more of a question rather than an issue.   
  
I am trying to administer a remote linux server from a Windows device using OpenSSH. The server requires a password for SSH and my program will ask the user for that via a GUI prompt and provide it to the boost process. Using ssh keys isn't really an option because I am not in direct control of the server and this may be used for many similar servers.  
  
It seemed like this would all be easy after reading the documentation; however, I can't seem to get past the password stage. When this code runs, the ssh prompt for the password appears in the console without the accompanying "STD OUT: " prefix that I was expecting.  
  
It seems that SSH is using some other mechanism rather than the std::out stream to prompt for password. If I do enter the password, then the ssh output is redirected as expected to the `is` stream.  
  
How can my application detect the password prompt as well?  
  
Thank you!  
  
```c++  
#include <iostream>  
  
#include <boost/process.hpp>  
#include <boost/process/windows.hpp>  
#include <boost/asio/io_service.hpp>  
  
namespace bp = boost::process;  
  
int main()  
{  
    bp::group g;  
  
    // Start a subprocess and attempt to connect to via ssh.  
    std::string cmd = "ssh -tt -p<your_port> <your_server_port>";  
  
    bp::ipstream is; //reading pipe-stream  
    bp::child c(cmd, bp::std_out > is, g);  
  
    std::vector<std::string> data;  
    std::string line;  
  
    while(c.running() && std::getline(is, line) && !line.empty())  
        std::cout << "STD OUT: " << line << std::endl;  
  
    c.wait();  
  
    return 0;  
}  
```

---

## Comment 1

> Username: klemens-morgenstern  
> Created at: 2022-08-30 09:14:29 UTC  
> Url: https://github.com/boostorg/process/issues/254#issuecomment-1231391820  

My suspicion is that ssh check if it's a shell or not. Can you try to write your password plus `\n` to ssh and see what happens?
