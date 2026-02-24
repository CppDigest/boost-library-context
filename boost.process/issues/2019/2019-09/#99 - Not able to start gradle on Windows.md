# #99 - Not able to start gradle on Windows [Closed]

> Username: saarbastler  
> Created at: 2019-09-05 11:58:14 UTC  
> Updated at: 2019-09-10 11:16:29 UTC  
> Closed at: 2019-09-06 06:28:40 UTC  
> Url: https://github.com/boostorg/process/issues/99  

I try to start a gradle (V5.4.1) process on Windows. The sample is compiled using Visual Studio 2017, boost 1.67.0 in 64 Bit.  
  
short sample:  
`#include <iostream>  
  
#include <boost/process.hpp>  
#include <boost/asio.hpp>  
  
namespace bp = boost::process;  
  
int main()  
{  
  bp::system("gradle.bat --stacktrace",  
    bp::on_exit([](int, const std::error_code & ec)  
  { std::cerr << ec.message() << std::endl; })  
  );  
}`  
  
longer sample Code (to see the error message):  
`#include <iostream>  
  
#include <boost/process.hpp>  
#include <boost/asio.hpp>  
  
namespace bp = boost::process;  
  
int main()  
{  
  boost::asio::io_service ios;  
  std::vector<char> buf_out(4096);  
  std::vector<char> buf_err(4096);  
  
  bp::async_pipe ap_out(ios);  
  bp::async_pipe ap_err(ios);  
  
  boost::process::child c("gradle.bat --stacktrace", bp::std_out > ap_out, bp::std_err > ap_err, ios);  
  
  std::function<void(bp::async_pipe&, std::vector<char>&)> readLoop = [&](bp::async_pipe& ap, std::vector<char>& buf) mutable  
  {  
    ap.async_read_some(boost::asio::buffer(buf), [&](const boost::system::error_code &ec, std::size_t size)  
    {  
      std::string tmp(buf.data(), size);  
  
      std::cout << tmp << std::endl;  
  
      if (ec)  
        std::cout << "read error: " << ec.message() << std::endl;  
      else  
        readLoop(ap, buf);  
    });  
  };  
  
  readLoop(ap_out, buf_out);  
  readLoop(ap_err, buf_err);  
  
  ios.run();  
  std::cout << "Exit Code: " << c.exit_code() << std::endl;  
}`  
  
The error message is:  
`FAILURE: Build failed with an exception.  
  
* What went wrong:  
A problem occurred starting process 'Gradle build daemon'  
  
* Try:  
Run with --info or --debug option to get more log output. Run with --scan to get full insights.  
  
  
* Exception is:  
org.gradle.process.internal.ExecException: A problem occurred starting process 'Gradle build daemon'  
        at org.gradle.process.internal.DefaultExecHandle.execExceptionFor(DefaultExecHandle.java:232)  
        at org.gradle.process.internal.DefaultExecHandle.setEndStateInfo(DefaultExecHandle.java:209)  
        at org.gradle.process.internal.DefaultExecHandle.failed(DefaultExecHandle.java:356)  
        at org.gradle.process.internal.ExecHandleRunner.run(ExecHandleRunner.java:86)  
        at org.gradle.internal.operations.CurrentBuildOperationPreservingRunnable.run(CurrentBuildOperationPreservingRunnable.java:38)  
        at org.gradle.internal.concurrent.ExecutorPolicy$CatchAndRecordFailures.onExecute(ExecutorPolicy.java:63)  
        at org.gradle.internal.concurrent.ManagedExecutorImpl$1.run(ManagedExecutorImpl.java:46)  
        at java.util.concurrent.ThreadPoolExecutor.runWorker(ThreadPoolExecutor.java:1149)  
        at java.util.concurrent.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:624)  
        at org.gradle.internal.concurrent.ThreadFactoryImpl$ManagedThreadRunnable.run(ThreadFactoryImpl.java:55)  
        at java.lang.Thread.run(Thread.java:748)  
Caused by: net.rubygrapefruit.platform.NativeException: Could not start 'C:\Program Files\Java\jdk1.8.0_202\bin\java.exe': could not get std handle (errno 6)  
  
        at net.rubygrapefruit.platform.internal.WindowsProcessLauncher.start(WindowsProcessLauncher.java:18)  
        at net.rubygrapefruit.platform.internal.WrapperProcessLauncher.start(WrapperProcessLauncher.java:36)  
        at org.gradle.process.internal.ExecHandleRunner.startProcess(ExecHandleRunner.java:97)  
        at org.gradle.process.internal.ExecHandleRunner.run(ExecHandleRunner.java:70)  
        ... 7 more  
  
  
* Get more help at https://help.gradle.org  
  
  
read error: Die Pipe wurde beendet  
  
read error: Die Pipe wurde beendet  
Exit Code: 259`  
  
Gradle is starting a Daemon in the Background, I guess redirecting stdin/stdout is not working.

---

## Comment 1

> Username: klemens-morgenstern  
> Created at: 2019-09-06 06:28:40 UTC  
> Url: https://github.com/boostorg/process/issues/99#issuecomment-528728699  

That looks like correct behaviour to me, when the pipe closes you receive that as an error_code. That means that gradle completed running, though with an error. That's gradle though, not the process library.

---

## Comment 2

> Username: saarbastler  
> Created at: 2019-09-10 11:16:29 UTC  
> Url: https://github.com/boostorg/process/issues/99#issuecomment-529888714  

I compiled the same code on Ubuntu (except line `bp::system("gradle.bat --stacktrace",` modified to `bp::system("gradle --stacktrace",`  
  
Executing is shows the expected result:   
```:help  
  
Welcome to Gradle 4.4.1.  
  
To run a build, run gradle <task> ...  
  
To see a list of available tasks, run gradle tasks  
  
To see a list of command-line options, run gradle --help  
  
To see more detail about a task, run gradle help --task <task>  
  
BUILD SUCCESSFUL in 0s  
1 actionable task: 1 executed  
  
  
read error: End of file  
  
read error: End of file  
Exit Code: 0  
```  
 So I think this is not in issue of gradle, beacuse running it from Windows command line does work. And running the code on Linux is working, too.
