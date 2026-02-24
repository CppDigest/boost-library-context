# #169 - POSIX multi-thread parallel childs invoked with piped std_in often do not receive STDIN close [Open]

> Username: martlaak-gmail  
> Created at: 2020-08-18 12:10:08 UTC  
> Updated at: 2020-11-03 22:03:20 UTC  
> Url: https://github.com/boostorg/process/issues/169  

For example this parallel POSIX child invoke with std_in from pipe often hangs on requestProcess->wait(), because child never receives STDIN close:  
```  
#include <iostream>  
#include <boost/process/child.hpp>  
#include <boost/process/io.hpp>  
#include <thread>  
  
using namespace std;  
namespace bp = boost::process;  
  
void runProcess()  
{  
  bp::opstream inputStream;  
  bp::ipstream outputStream;  
  inputStream << "test" << endl;  
  
  unique_ptr<bp::child> requestProcess(new bp::child("\"cat\"", bp::shell,  
                                                     bp::std_in < inputStream,  
                                                     (bp::std_out & bp::std_err) > outputStream));  
  inputStream.pipe().close();  
  requestProcess->wait();  
  
  std::string line;  
  while (outputStream && std::getline(outputStream, line) && !line.empty())  
     cerr << line << std::endl;  
}  
  
int main()  
{  
  std::thread thread1(runProcess);  
  std::thread thread2(runProcess);  
  thread1.join();  
  thread2.join();  
}  
```  
Note, that:  
- same program does not hang on Windows ("cat" has to be replaced with "more" then)  
- same program does not hang when std_in is read from file (using bp::std_in < "some_file_name.txt")  
- for me it usually does not hang under debugger but hangs 100% of tries while running without debugger

---

## Comment 1

> Username: Boronak  
> Created at: 2020-08-23 11:47:01 UTC  
> Url: https://github.com/boostorg/process/issues/169#issuecomment-678764237  

The child processes are inheriting the pipe input filehandle and therefore the pipe can never truly close. Pipes should be created with O_CLOEXEC.

---

## Comment 2

> Username: klemens-morgenstern  
> Created at: 2020-08-24 14:03:53 UTC  
> Url: https://github.com/boostorg/process/issues/169#issuecomment-679147085  

@Boronak It would be nice if you kept your issue reporting to one issue instead of misusing comments like this.

---

## Comment 3

> Username: klemens-morgenstern  
> Created at: 2020-08-24 14:05:49 UTC  
> Url: https://github.com/boostorg/process/issues/169#issuecomment-679148106  

Can you try putting the `requestProcess->wait();` after the while loop?

---

## Comment 4

> Username: Boronak  
> Created at: 2020-08-24 14:45:36 UTC  
> Url: https://github.com/boostorg/process/issues/169#issuecomment-679170651  

@klemens-morgenstern  Sorry for being so unclear. The workaround looks like this:  
```  
#include <iostream>  
#include <boost/process/child.hpp>  
#include <boost/process/io.hpp>  
#include <thread>  
  
using namespace std;  
namespace bp = boost::process;  
  
void runProcess()  
{  
  int pipefd[2];  
  if (pipe2(pipefd, O_CLOEXEC) != 0)  
      throw std::system_error{errno, std::generic_category()};  
  boost::process::pipe PIPE{pipefd[0], pipefd[1]};  
  
  bp::opstream inputStream{std::move(PIPE)};  
  bp::ipstream outputStream;  
  inputStream << "test" << endl;  
  
  unique_ptr<bp::child> requestProcess(new bp::child("\"cat\"", bp::shell,  
                                                     bp::std_in < inputStream,  
                                                     (bp::std_out & bp::std_err) > outputStream));  
  inputStream.pipe().close();  
  requestProcess->wait();  
  
  std::string line;  
  while (outputStream && std::getline(outputStream, line) && !line.empty())  
     cerr << line << std::endl;  
}  
  
int main()  
{  
  std::thread thread1(runProcess);  
  std::thread thread2(runProcess);  
  thread1.join();  
  thread2.join();  
}  
```

---

## Comment 5

> Username: klemens-morgenstern  
> Created at: 2020-08-24 14:49:34 UTC  
> Url: https://github.com/boostorg/process/issues/169#issuecomment-679172868  

@Boronak does that work without `bp::shell` ?

---

## Comment 6

> Username: Boronak  
> Created at: 2020-08-24 14:51:47 UTC  
> Url: https://github.com/boostorg/process/issues/169#issuecomment-679174198  

@klemens-morgenstern Yes.  
```  
unique_ptr<bp::child> requestProcess(new bp::child("\"cat\"",// bp::shell,  
```

---

## Comment 7

> Username: Boronak  
> Created at: 2020-08-24 14:53:57 UTC  
> Url: https://github.com/boostorg/process/issues/169#issuecomment-679175443  

Please note there are some other problems with this code. As @klemens-morgenstern correctly points out the `wait` is in the wrong place and the whole code is dependant on OS pipe buffers being big enough to take up the slack. This code may easily deadlock in other ways with more data passing through it.

---

## Comment 8

> Username: martlaak-gmail  
> Created at: 2020-08-25 10:31:50 UTC  
> Url: https://github.com/boostorg/process/issues/169#issuecomment-679943796  

Yes, I confirm that creating inputStream using pipe created with O_CLOEXEC helps to fix my issue. Thanks!  
  
It is still little confusing to me why the O_CLOEXEC flag was not needed when running only one process then (eg. same example without parallel threads works correctly without it)?

---

## Comment 9

> Username: martlaak-gmail  
> Created at: 2020-08-25 10:33:57 UTC  
> Updated at: 2020-08-25 11:00:51 UTC  
> Url: https://github.com/boostorg/process/issues/169#issuecomment-679944686  

> Can you try putting the `requestProcess->wait();` after the while loop?  
  
And BTW, this did not help. It then simply blocks on getline(outputStream, line) (as child ("cat") endlessly runs and waits for STDIN to be closed).

---

## Comment 10

> Username: Boronak  
> Created at: 2020-08-25 11:39:05 UTC  
> Url: https://github.com/boostorg/process/issues/169#issuecomment-679972389  

@martlaak-gmail The pipe handles passed to `child::child` get closed by `boost::process`, but the `cat` instance spawned by the other thread justly blindly inherits them and "steals" the handles keeping them open.

---

## Comment 11

> Username: martlaak-gmail  
> Created at: 2020-08-26 07:22:01 UTC  
> Url: https://github.com/boostorg/process/issues/169#issuecomment-680707186  

> @martlaak-gmail The pipe handles passed to `child::child` get closed by `boost::process`, but the `cat` instance spawned by the other thread justly blindly inherits them and "steals" the handles keeping them open.  
  
Yes, but exactly the same happens when only one child is launched. Why it is never problem then?

---

## Comment 12

> Username: Boronak  
> Created at: 2020-08-26 09:07:46 UTC  
> Url: https://github.com/boostorg/process/issues/169#issuecomment-680757659  

Single thread:  
```  
Thread 1:  
    Make pipe 1  
    Fork process 1  
Process 1:  
    Inherit pipe 1  
    Dup pipe 1 to STDIN  
    Close pipe 1  
    Exec -> cat  
```  
  
Multi thread:  
```  
Thread 1:  
    Make pipe 1  
Thread 2:  
    Make pipe 2  
Thread 1:  
   Fork process 1  
Process 1:  
   Inherit pipe 1  
   Inherit pipe 2  
   Dup pipe 1 to STDIN  
   Close pipe 1  
   Exec -> cat  
   Pipe 2 is still open...  
```

---

## Comment 13

> Username: SignalWhisperer  
> Created at: 2020-11-03 22:03:20 UTC  
> Url: https://github.com/boostorg/process/issues/169#issuecomment-721397811  

I might have some insight on this. I was facing the same issue where launching a single child process would have no problem, but launching multiple in a multi-threaded fashion caused none of them to receive data from stdin.  
  
I tried to workaround by implementing the code myself using `posix_spawn` and found the exact same problem. The issue is a race condition where one thread calls `fork` and before it can call `execve`, the second thread calls `fork`. The problem would not occur on Windows as it would be a single call to `CreateProcess` (or whatever is used) and would be an atomic-like action.  
  
The workaround is to add a mutex to the child spawning, which ensures the calls to `fork` are not interleaved. As to whether the mutex should be inside the Process library or inside user code, that's up to the project owners. Personally, I think the different behavior between Windows and POSIX/Linux (MT-safety that is) should at least be warned against, if not made an atomic-like operation.  
  
I don't think this library makes any MT-safety promises anywhere, but I think it should be safe to assume that launching a new process should be MT-safe, although that's my own opinion.
