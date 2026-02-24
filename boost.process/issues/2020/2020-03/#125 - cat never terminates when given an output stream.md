# #125 - cat never terminates when given an output stream [Open]

> Username: sudosandwich  
> Created at: 2020-03-06 20:12:48 UTC  
> Updated at: 2021-08-31 10:55:20 UTC  
> Url: https://github.com/boostorg/process/issues/125  

When I call cat and attach bp::stdout (wether it be stdout or a pstream) the program hangs.  
The following hopefully shows the problem.  
  
```c++  
#include <boost/process.hpp>  
#include <boost/filesystem.hpp>  
  
#include <iostream>  
  
using namespace std;  
namespace bp = boost::process;  
  
int main(int argc, char **argv)  
{  
    cout << boolalpha;  
  
    try  
    {  
        bp::opstream goodcat_in;  
        bp::child goodcat(bp::search_path("cat"), bp::std_in < goodcat_in);  
  
        goodcat_in << "foo" << endl;  
        goodcat_in.close();  
  
        cout << "Trying to wait for goodcat to finish" << endl;  
        goodcat.wait_for(std::chrono::seconds(3));  
        cout << "Is goodcat still running: " << goodcat.running() << endl;  
  
        bp::opstream badcat_in;  
        bp::child badcat(bp::search_path("cat"), bp::std_in<badcat_in, bp::std_out> stdout);  
  
        badcat_in << "foo" << endl;  
        badcat_in.close();  
  
        cout << "Trying to wait for badcat to finish" << endl;  
        badcat.wait_for(std::chrono::seconds(3));  
        cout << "Is badcat still running: " << badcat.running() << endl;  
    }  
    catch (bp::process_error &e)  
    {  
        cerr << "err " << e.what() << endl;  
    }  
}  
```  
  
wandbox says both are still running (on a *nix): https://wandbox.org/permlink/4i8ZZik4f0dRjj0t  
  
My local tests are on Windows10 on a cygwin shell (babun) and the builtin command prompt as well.

---

## Comment 1

> Username: Boronak  
> Created at: 2020-08-23 11:47:44 UTC  
> Updated at: 2020-08-23 11:47:51 UTC  
> Url: https://github.com/boostorg/process/issues/125#issuecomment-678764308  

The child processes is inheriting the pipe input filehandle and therefore the pipe can never truly close. Pipes should be created with O_CLOEXEC.

---

## Comment 2

> Username: klemens-morgenstern  
> Created at: 2020-08-24 14:02:52 UTC  
> Url: https://github.com/boostorg/process/issues/125#issuecomment-679146345  

Weird, thought it might be an issue with how `cat` handles pipe closing. It doesn't check for `EOF` but for reading an empty line.

---

## Comment 3

> Username: Boronak  
> Created at: 2020-08-24 15:17:05 UTC  
> Url: https://github.com/boostorg/process/issues/125#issuecomment-679190167  

I'm pretty sure `cat` works OK. I notice that this code is fixed by changing `*cat_in.close()` to `*cat_in.pipe().close()` because closing a `pipebuf` doesn't actually close the underlying pipe. Not sure if this is intended behaviour.

---

## Comment 4

> Username: Boronak  
> Created at: 2020-08-24 15:30:04 UTC  
> Url: https://github.com/boostorg/process/issues/125#issuecomment-679197922  

I withdraw my comment about `O_CLOEXEC`. That's an issue related to multithreading on Linux.

---

## Comment 5

> Username: SignalWhisperer  
> Created at: 2020-11-03 20:01:26 UTC  
> Updated at: 2020-11-03 20:03:17 UTC  
> Url: https://github.com/boostorg/process/issues/125#issuecomment-721346011  

I found an old (8 years old) thread on [StackOverflow](https://stackoverflow.com/questions/12329065/how-to-bind-program-termination-with-end-of-stream-in-boost-process-0-5) which has a workaround for this kind of problem. It seems like closing the stream does not mark its end because the pipe remains open. It needs to go out of scope for the pipe to be closed by the destructor.  
  
https://github.com/boostorg/process/blob/b2a96a3e139ae42ea2bcb80cc58f108494a995c5/include/boost/process/pipe.hpp#L249-L256  
This needs to additionally call `_pipe.close();` for the other end of the stream to see the EOF.  
  
Something along those lines:  
```c++  
    ///Flush the buffer & close the pipe  
    basic_pipebuf<CharT, Traits>* close()  
    {  
        if (!is_open())  
            return nullptr;  
        overflow(Traits::eof());  
        _pipe.close();  
        return this;  
    }  
```

---

## Comment 6

> Username: prlw1  
> Created at: 2021-08-31 10:46:17 UTC  
> Updated at: 2021-08-31 10:55:20 UTC  
> Url: https://github.com/boostorg/process/issues/125#issuecomment-909120605  

I have just encountered this bug with a fortran program which reads some configuration from stdin. `cat` is blameless...  
  
The workaround is indeed  
```  
cat_in.close();  
cat_in.pipe().close();  
```  
as suggested above by @Boronak. I needed both rather than change. If I didn't cat_in.close(), the output file would be empty, but the process would complete. (without the pipe().close() it would hang.)  
  
At least a documentation update necessary?
