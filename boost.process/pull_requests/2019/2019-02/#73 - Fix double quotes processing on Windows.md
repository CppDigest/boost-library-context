# #73 Fix double quotes processing on Windows [Merged]

> Username: fkonvick  
> Created at: 2019-02-27 13:07:22 UTC  
> Updated at: 2021-03-23 20:52:01 UTC  
> Merged at: 2020-01-26 08:16:39 UTC  
> Closed at: 2020-01-26 08:16:39 UTC  
> Url: https://github.com/boostorg/process/pull/73  

Fix double quotes processing on Windows:  
- quotes should only be added when not already present  
- quotes need to be used when an argument includes spaces or double quotes  
- quotes inside an argument should be doubled (that's the way Windows does escaping)  
  
Test:  
`BOOST_ASSERT(bp::detail::windows::build_args("echo", {"\"1\"", "2 3", "4\"5", "6 \"7"}) == "echo \"1\" \"2 3\" \"4\"\"5\" \"6 \"\"7\"");  
`

---

## Comment 1

> Username: fkonvick  
> Created_at: 2019-03-01 09:03:11 UTC  
> Url: https://github.com/boostorg/process/pull/73#issuecomment-468594378  

TBH I think that the CI build failure is not related to this PR; the only change in this PR is for the Windows sources; the failures are occuring on Linux & Mac.

---

## Comment 2

> Username: klemens-morgenstern  
> Created_at: 2019-03-01 10:52:58 UTC  
> Url: https://github.com/boostorg/process/pull/73#issuecomment-468626060  

Indeed unrelated, that's from a boost.asio update.  
  
I don't think you're right though.   
Could you maybe provide a test with a program reflecting the arguments, so we can it is received by the subprocess correctly?

---

## Comment 3

> Username: fkonvick  
> Created_at: 2019-03-01 17:29:48 UTC  
> Url: https://github.com/boostorg/process/pull/73#issuecomment-468744848  

Sure.  This was driven by an issue that we found with launching a batch file via `bp::shell`.  The problem was that `basic_cmd.hpp` was unconditonally replacing `"` with `\"`, which does not make sense on Windows as `\` is not an escaping character for `cmd.exe`.  
The following illustrates the problem:  
```  
#include <boost/process.hpp>  
namespace bp = boost::process;  
int main() {  
  bp::child(bp::shell, bp::args += {"echo", "Hello \"world\"!"}).join();  
}  
```  
This prints (IMHO clearly incorrectly):  
`"Hello \"world\"!"`  
With the patch applied, it prints the following:  
`"Hello ""world""!"`  
  
In my comment accompanying the PR, the `BOOST_ASSERT` captures what I think is right (or at least better).  In the PR I just did what I could to patch what seemed wrong to me and what could be fixed without a major change.  
  
For more information, you may look here: [MSDN Blog](https://blogs.msdn.microsoft.com/twistylittlepassagesallalike/2011/04/23/everyone-quotes-command-line-arguments-the-wrong-way/), sections 'The correct solution' and  'cmd.exe' (which is particularly interesting as `bp::shell` seems to be running `cmd.exe /c ...`).  Reading this makes me think that we should open an issue so that this is properly implemented in the future.  Part of the reference spec is probably this: [docs.microsoft.com](https://docs.microsoft.com/en-us/previous-versions//17w5ykft(v=vs.85)).

---

## Comment 4

> Username: fkonvick  
> Created_at: 2021-03-23 20:52:01 UTC  
> Url: https://github.com/boostorg/process/pull/73#issuecomment-805246406  

Perhaps, but I'm not sure where :) My findings:  
  
An attempt to run the above on the command line does not work:  
`robocopy "C:\Path\To\Src\" "C:\Path\To\Dst\"`  
This thinks that this is the source path: `C:\Path\To\Src" C:\Path\To\Dst"\`  
This in my opinion is due to cmd.exe's cmdline parsing, [docs.microsoft.com](https://docs.microsoft.com/en-us/previous-versions//17w5ykft(v=vs.85))  
The `\"` sequence is treated as an escaped quote and so does not split to 2 args.  
  
The correct way is to either double the trailing path backslash or remove it such as:  
`robocopy "C:\Path\To\Src\\" "C:\Path\To\Dst\\"`  
`robocopy "C:\Path\To\Src" "C:\Path\To\Dst"`  
  
I ran your code with both `bp::exe` and `bp::shell`.  From the output that I can see, the `"` is indeed passed to robocopy as a part of the argument, but robocopy fails to decode this as an absolute path and treats it as a relative path, as you observed.  
  
I think the problem is in your statement "Robocopy treats arguments inside of double quotes as absolute paths".  I think that robocopy does not actually process the double quotes, it is `cmd.exe`.  And, it removes those quotes from the actual `argv` passed to robocopy.  When using `bp::args`, we in fact do not need to surround each argument in double quotes.  I tried running with  
`bp::args = { "C:\\Path\\To\\Src", "C:\\Path\\To\\Dest", "/mir" }`  
and this seems to work fine.  Note that this works even when the paths contain spaces etc.  
  
Hopefully this solves the problem.

---
