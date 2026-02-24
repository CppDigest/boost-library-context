# #383 - terminate not killing pipeline [Closed]

> Username: user706  
> Created at: 2024-07-08 18:12:27 UTC  
> Updated at: 2024-07-10 07:17:20 UTC  
> Closed at: 2024-07-09 13:42:31 UTC  
> Url: https://github.com/boostorg/process/issues/383  

Hi,   
  
I'm on a Rpi using boost 1.74.0  
  
I'm running a command, basically like this::  
```bash  
tar bla bla | 7z a -mx=1 -mf=Delta:4 -si out1.7z  > /dev/null  
```  
(note the `7z` in the end!!)  
  
 and then try to terminate it nicely:  
  
```cpp  
    const std::string cmd = std::string("/usr/bin/nice -n 19 /bin/bash -c \"set -Eeuo pipefail; ") +  
                            " \\\n\t\t  echo $BASHPID; tar -C " + c_tDir.native() + " -hcf - " + tFileName +  | 7z a -mx=1 -mf=Delta:4 -si " + zipPath.native() + " > /dev/null  \"";  
  
    boost::process::ipstream pipe_stream;  
    boost::process::child zip_process(cmd, boost::process::std_err > pipe_stream);  
    //const pid_t pid = zip_process.id();  
    unsigned cnt = 0;  
    for (std::string line; pipe_stream && std::getline(pipe_stream, line) && !line.empty(); ) {  
        std::cout << line << std::endl;  
        if (++cnt == 3) {  
             zip_process.terminate();    // TERMINATE... but 7z stays!!!!!  WHY?  help!  
             std::cout << "terminate" << std::endl;  
        }  
    }  
    zip_process.wait();  
    //kill(pid, SIGKILL);  
  
```  
  
But if I check `htop`, I can see a lot of `7z` processes still living! And having high processor load!  
  
```bash  
ps aux | grep $PID   # $PID is same as zip_process.id();  
```  
shows  
`root      4783  0.0  0.0      0     0 ?        ZN   20:06   0:00 [bash] <defunct>`  
  
```  
ps aux | grep 7z  
```  
  
shows   
  
`root      4786  273  0.3  70788 14240 ?        SNl  20:06   1:46 /usr/lib/p7zip/7z a -mx=1 -mf=Delta:4 -si /path/to/file.7z`  
  
Thanks for any help. (Is this an issue with old boost? I better ask, before investing time to get new boost working - but should perhaps be ok'ish with header-only...)

---

## Comment 1

> Username: user706  
> Created at: 2024-07-09 13:42:31 UTC  
> Url: https://github.com/boostorg/process/issues/383#issuecomment-2217782942  

So turns out, ... I need to use a process group:  
  
https://www.boost.org/doc/libs/1_85_0/doc/html/boost_process/tutorial.html#boost_process.tutorial.group  
  
```cpp  
  boost::process::group g;                                              // !  
  boost::process::child c(command, g);  
  
  assert(c.running());  
  std::this_thread::sleep_for(std::chrono::seconds(1));  
  std::cout << "terminate()" << std::endl;  
  g.terminate();                                                        // !  
  std::cout << "terminate() finished" << std::endl;  
  c.wait();  
```
