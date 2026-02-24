# #399 - Feature request: Add support for pty fds in process [Open]

> Username: leha-bot  
> Created at: 2024-09-09 11:06:17 UTC  
> Updated at: 2024-10-29 15:12:24 UTC  
> Url: https://github.com/boostorg/process/issues/399  

The main use case is for batch run some apps which doesn't understand `"non-tty"` stdin.  
  
Windows supports this from win10 , 1803 and requires the special HANDLEs created via [CreatePseudoConsole](https://learn.microsoft.com/en-us/windows/console/createpseudoconsole)  
Linux creates these pty via [openpty(3)](https://www.man7.org/linux/man-pages/man3/openpty.3.html).

---

## Comment 1

> Username: klemens-morgenstern  
> Created at: 2024-09-26 15:11:27 UTC  
> Url: https://github.com/boostorg/process/issues/399#issuecomment-2377253153  

What do you have in mind here? An IO Object for the ability to set this for a subprocess?

---

## Comment 2

> Username: leha-bot  
> Created at: 2024-09-29 18:34:15 UTC  
> Url: https://github.com/boostorg/process/issues/399#issuecomment-2381455635  

I thought about some kind of API like  
(it's not exactly boost process API, just bikeshedding in bp v1 API style):  
  
```cpp  
bp::pty pty;  
bp::process child("/some/binary", bp::std_in > pty, bp::std_out < pty);  
  
std::string process_output_line;  
pty >> process_output_line; // read string from process (until first space , or use std::getline())  
pty << 15; // write to child' stdin via pty  
```  
  
Currently, there is no portable way (at least I tried to (ab)use the posix file descriptors, but no luck, pipe helpers won't work with same pty fd and either copies them, or hangs)

---

## Comment 3

> Username: klemens-morgenstern  
> Created at: 2024-10-29 15:12:23 UTC  
> Url: https://github.com/boostorg/process/issues/399#issuecomment-2444558867  

I did some work here: https://github.com/boostorg/process/tree/pty  
  
I don't think I'll finish it, the windows API is just too hostile and the posix one is straight forward enough to not need built-in support.
