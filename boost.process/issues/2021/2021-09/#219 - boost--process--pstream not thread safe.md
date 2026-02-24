# #219 - boost::process::pstream not thread safe? [Open]

> Username: prlw1  
> Created at: 2021-09-01 09:49:55 UTC  
> Updated at: 2021-09-01 11:06:53 UTC  
> Url: https://github.com/boostorg/process/issues/219  

I thought that `boost::process` could allow me to convert what is becoming a rather involved shell script into a C++ program. It is of the usual "run the same program lots of times with different inputs then process the results" variety, with the magical "&" character in the shell script.  
  
For a quick test `/bin/cat` is the program:  
```  
// boost::process::pstream not thread safe?  
// Related to  
//   https://github.com/boostorg/process/issues/206  
//   https://github.com/boostorg/process/issues/169  
//   https://github.com/boostorg/process/issues/170  
// ?  
#include <cstdlib>  
#include <future>  
#include <iostream>  
#include <list>  
#include <string>  
  
#include <boost/process.hpp>  
namespace bp = boost::process;  
  
int job(const int n)  
{  
#if 1  
        bp::ipstream is;  
        bp::opstream os;  
        bp::child c("/bin/cat", bp::std_in < os, bp::std_out > is);  
        os << "job " << n << '\n';  
        os.close();  
        // https://github.com/boostorg/process/issues/125  
        os.pipe().close();  
        std::string line;  
        while (c.running() && std::getline(is, line)) {  
                // do something with line locally  
        }  
        c.wait();  
        if (c.exit_code()) { std::cerr << "Ooops!\n"; exit(1); }  
        is.close();  
#endif  
        return n;  
}  
  
int main(int argc, char **argv)  
{  
        int nproc;  
        if (!(argc == 2 && (nproc=std::atoi(argv[1])) > 0)) {  
                std::cerr << "Usage: " << argv[0] << " n_processes\n";  
                exit(1);  
        }  
  
        std::list<std::future<int>> jobs;  
        for (int n = 1; n <= nproc; ++n) {  
                jobs.push_back(std::async(std::launch::async, job, n));  
        }  
        while (!jobs.empty()) {  
                std::cout << "got " << jobs.front().get() << '\n';  
                jobs.pop_front();  
        }  
}  
```  
- When run with `n_processes=2`, `cat` hangs in state `pipe_r`.  
- When compiled with `#if 0`, the `async` side of the program seems fine.  
- Everything in job() apart from `n` is local from the C++ point of view, but presumably some file descriptor sharing is going on underneath...

---

## Comment 1

> Username: prlw1  
> Created at: 2021-09-01 10:11:02 UTC  
> Url: https://github.com/boostorg/process/issues/219#issuecomment-910138845  

It seems #170 is the underlying bug. Either create pipes O_CLOEXEC, or update all the tutorials / documentation with the workaround in #169 as being the way to use boost::process. The following works:  
```  
// boost::process::pstream not thread safe?  
//   https://github.com/boostorg/process/issues/219  
// Related to  
//   https://github.com/boostorg/process/issues/206  
//   https://github.com/boostorg/process/issues/169  
//   https://github.com/boostorg/process/issues/170  
// ?  
#include <cerrno>  
#include <cstdlib>  
#include <future>  
#include <iostream>  
#include <list>  
#include <string>  
#include <system_error>  
  
#include <boost/process.hpp>  
namespace bp = boost::process;  
  
int job(const int n)  
{  
#if 1  
#if 1  
        // https://github.com/boostorg/process/issues/169  
        int pipefd[2];  
        if (pipe2(pipefd, O_CLOEXEC) != 0)  
                throw std::system_error{errno, std::generic_category()};  
        bp::pipe PIPE{pipefd[0], pipefd[1]};  
  
        bp::opstream os{std::move(PIPE)};  
#else  
        bp::opstream os;  
#endif  
        bp::ipstream is;  
        bp::child c("/bin/cat", bp::std_in < os, bp::std_out > is);  
        os << "job " << n << '\n';  
        os.close();  
        // https://github.com/boostorg/process/issues/125  
        os.pipe().close();  
        std::string line;  
        while (c.running() && std::getline(is, line)) {  
                // do something with line locally  
        }  
        c.wait();  
        if (c.exit_code()) { std::cerr << "Ooops!\n"; exit(1); }  
        is.close();  
#endif  
        return n;  
}  
  
int main(int argc, char **argv)  
{  
        int nproc;  
        if (!(argc == 2 && (nproc=std::atoi(argv[1])) > 0)) {  
                std::cerr << "Usage: " << argv[0] << " n_processes\n";  
                exit(1);  
        }  
  
        std::list<std::future<int>> jobs;  
        for (int n = 1; n <= nproc; ++n) {  
                jobs.push_back(std::async(std::launch::async, job, n));  
        }  
        while (!jobs.empty()) {  
                std::cout << "got " << jobs.front().get() << '\n';  
                jobs.pop_front();  
        }  
}  
```

---

## Comment 2

> Username: klemens-morgenstern  
> Created at: 2021-09-01 10:37:08 UTC  
> Url: https://github.com/boostorg/process/issues/219#issuecomment-910156863  

I think this might get you stuck:  
  
```cpp  
while (c.running() && std::getline(is, line)) {  
                // do something with line locally  
        }  
 ```  
   
 The write end will still be open after launching the process, so you have a data race if cat is done writing but didn't exit. THen `c.running()` will return true, while `getline()` then waits forever.

---

## Comment 3

> Username: prlw1  
> Created at: 2021-09-01 10:52:02 UTC  
> Url: https://github.com/boostorg/process/issues/219#issuecomment-910166019  

On Wed, Sep 01, 2021 at 03:37:18AM -0700, Klemens Morgenstern wrote:  
> I think this might get you stuck:  
>   
> ```cpp  
> while (c.running() && std::getline(is, line)) {  
>                 // do something with line locally  
>         }  
>  ```  
>    
>  The write end will still be open after launching the process, so you have a data race if cat is done writing but didn't exit. THen `c.running()` will return true, while `getline()` then waits forever.  
  
  
Maybe, but with nproc = 100 I haven't hit that one, but with nproc = 2,  
I do hit your CLOEXEC issue. Slightly different order of magnitude, and  
just because there might be something else up, isn't a reason to ignore  
the elephant...

---

## Comment 4

> Username: klemens-morgenstern  
> Created at: 2021-09-01 11:06:53 UTC  
> Url: https://github.com/boostorg/process/issues/219#issuecomment-910176592  

CLOEXEC is not a bug, it's a deliberate decision for portability. Does it happen with N=2 if you close the write end on the pipe after launching `cat`?
