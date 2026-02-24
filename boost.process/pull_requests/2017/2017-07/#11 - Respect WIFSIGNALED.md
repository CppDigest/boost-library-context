# #11 Respect WIFSIGNALED [Merged]

> Username: thomasschaub  
> Created at: 2017-07-19 10:27:57 UTC  
> Updated at: 2017-07-24 10:08:58 UTC  
> Merged at: 2017-07-23 21:18:17 UTC  
> Closed at: 2017-07-23 21:18:17 UTC  
> Url: https://github.com/boostorg/process/pull/11  

This is making the library a little nicer. Consider this example:  
  
    // child.cpp  
    int main()  
    {  
      throw 0;  
    }  
  
    // parent.cpp  
    #include <boost/process.hpp>  
    int main()  
    {  
      boost::process::child("./child").wait();  
    }`  
  
In the current state, this will output the following:  
  
     % ./parent  
    libc++abi.dylib: terminating with uncaught exception of type int  
    libc++abi.dylib: terminating with uncaught exception of type boost::process::process_error: waitpid(2) failed: No child processes  
    zsh: abort      ./parent`  
  
With this patch, you get this output instead  
  
     % ./parent  
    libc++abi.dylib: terminating with uncaught exception of type int  
    libc++abi.dylib: terminating with uncaught exception of type boost::process::process_error: process terminated due to receipt of a signal  
    zsh: abort      ./parent`  
  
I want to point out again that this is only about making the library a little nicer, i.e., the patch is certainly not required. However, I spent a bit of time debugging my application and the error `waitpid(2) failed: No child processes` sent me down the wrong path in that process, so I think it's useful. :-)

---

## Comment 1

> Username: klemens-morgenstern  
> Created_at: 2017-07-19 11:04:19 UTC  
> Url: https://github.com/boostorg/process/pull/11#issuecomment-316350778  

Thanks for the PR - I have to think about that. It seems right, but then I have not concept of the whole signalling business, that I might only want to throw for terminate (i.e. `SIGKILL`). Though that could mean that I already handle the signal wrong now, and you make that nicer. I'll get back to it after I had time to think that through. Could be some time though.

---

## Comment 2

> Username: egorpugin  
> Created_at: 2017-07-24 08:28:15 UTC  
> Url: https://github.com/boostorg/process/pull/11#issuecomment-317353895  

Why this change was only for one wait function inside that file?  
Aren't other methods can be still affected by the original issue?

---

## Comment 3

> Username: thomasschaub  
> Created_at: 2017-07-24 08:46:43 UTC  
> Updated_at: 2017-07-24 08:47:46 UTC  
> Url: https://github.com/boostorg/process/pull/11#issuecomment-317358231  

True, I just had a quick look, I should have changed the `wait`s in `wait_group.hpp` as well. I'll create another PR.  
  
edit: there are actually even more, my bad.

---

## Comment 4

> Username: klemens-morgenstern  
> Created_at: 2017-07-24 10:08:47 UTC  
> Url: https://github.com/boostorg/process/pull/11#issuecomment-317378061  

@egorpugin @thomasschaub Please create further PRs against https://github.com/klemens-morgenstern/boost-process so the CI will test every PR.

---

## Comment 5

> Username: klemens-morgenstern  
> Created_at: 2017-07-24 10:08:58 UTC  
> Url: https://github.com/boostorg/process/pull/11#issuecomment-317378107  

And thanks for the work!

---
