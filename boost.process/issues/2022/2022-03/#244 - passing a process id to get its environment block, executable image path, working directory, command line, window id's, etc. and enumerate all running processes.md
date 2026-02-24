# #244 - passing a process id to get its environment block, executable image path, working directory, command line, window id's, etc. and enumerate all running processes [Closed]

> Username: ghost  
> Created at: 2022-03-25 01:35:43 UTC  
> Updated at: 2022-05-18 10:09:02 UTC  
> Closed at: 2022-05-18 10:09:02 UTC  
> Url: https://github.com/boostorg/process/issues/244  

I wrote some things for my own process library I'd like to contribute to the boost process library.  
  
Source code here:  
https://github.com/time-killer-games/xproc/blob/main/apiprocess/process.cpp  
  
As explained in the title, this is capable of getting environ, cmdline, cwd, exe, and wid of an arbitrary pid. The wid, or Window ID, on Windows is an std::to_string((uintptr_t)(void *)HWND), on Mac it is a std::to_string((uintptr_t)CGWindowID) though not sure if iOS has CGWindowID or something else) and on Linux currently only X11 Xlib windows are supported, but Wayland and the Android "equivalent" will be added in the future, i.e. X11 Xlib Window ID would be std::to_string((uintptr_t)Window). A CGWindowID is a typedef of an unsigned int. An Xlib Window is a typedef of unsigned long. And of course a Windows HWND is a typedef of a void *. The Window ID doesn't need to be a string; I only did that for easy concatenation in shell scripting and to unify each platform to use the same datatype underneath to represent a window. Although I admit using a string is slow and not all that practical otherwise.  
  
I also support getting a list of all processes running in the current user session.  
  
I support currently Windows, macOS, Linux, FreebSD, DragonFly BSD, and OpenBSD. I plan to support NetBSD and Illumos as well. As far as I know, though I haven't tested, iOS and Android should also be supported, iOS would need to run in Developer mode to have access to certain features that aren't allowed on the AppStore. Android would require libprocps from linux world to be compiled specifically for Android, although I'm not sure if Android supports all the same procfs stuff that Linux does.  
  
I was wondering if these ideas would be considered at all for a future release of boost process if I were to make a pull request and listen to all of your code review critiques. Also I think it would be good to having Window ID stuff only available to the user when enabled via a macro defined at compile time, as there are a lot of platforms that don't even have a windowing system that I'd like to support and would prefer to not rely on for example Xlib or Wayland by default because that isn't 100% needed.  
  
The only platforms that have dependencies are Linux and Android (libprocps)  
  
Let me know what you think.  
Samuel

---

## Comment 1

> Username: klemens-morgenstern  
> Created at: 2022-04-28 01:26:38 UTC  
> Url: https://github.com/boostorg/process/issues/244#issuecomment-1111638137  

I did look into that kind of stuff some time ago, and didn't plan on adding it because it did look more like  reverse engineering than like using system APIs to me.   
  
It looks like you've added a few internal windows structs, that are not part of the official API; nothing wrong with that, but it does make it less reliable.   
  
The boost::process::child class is meant to manage the lifetime of a process so this is a bit outside the scope. I did however have people that asked for that. I am also working on a process v2.  
  
I think the better approach would be to make this it's own boost library (xproc would be a fine name, or `boost.top`).   
Then it can just interfaces with boost.process, e.g.:  
  
```cpp  
  
#include <boost/xproc.hpp>  
#include <boost/process/child.hpp>  
  
int main()  
{  
    boost::process::child c{ /* start a proc ... */ };  
    boost::xproc::proc_info pi{c}; // now use xproc to inspect it.  
    std::cout << pi.env() << std::endl;  
}  
```  
  
This shouldn't be too hard since child can be construct from a PID.

---

## Comment 2

> Username: ghost  
> Created at: 2022-04-28 15:08:02 UTC  
> Updated at: 2022-04-28 15:08:44 UTC  
> Url: https://github.com/boostorg/process/issues/244#issuecomment-1112325750  

Would you by any chance be ok with making a new repository with a basic skeleton for the structure of how you want the new library to implemented? Then it would just be a matter of me plugging in and adapting my existing code to the relevant places in a pull request?  
  
It was called "xproc" to imply "cross-platform process library" but now I can see it could also imply "cross-process" or inter-process exchange of process information, which would be a very specific and niche type of IPC so it would probably need a name that reflects what it does more specifically. I'll let you decide how that should be done if you are really thinking about doing this. :) Maybe something that could differentiate it from the existing boost ipc module.

---

## Comment 3

> Username: ghost  
> Created at: 2022-04-28 15:21:24 UTC  
> Updated at: 2022-04-28 16:01:20 UTC  
> Url: https://github.com/boostorg/process/issues/244#issuecomment-1112340656  

Additonal information about this issue I posted here may also be found where I suggested adding these features to SDL2.  
  
https://github.com/libsdl-org/SDL/issues/5570  
  
More specifically, this bit I failed to mention on my original boost issue:  
  
> Windows currently can only read some process information based on processes that are the same architecture as the calling process, and i do have a means to read between 64-bit <-> 32-bit x86 but it's rather convoluted and crashes when built with MSVC instead if MinGW, so I already know it won't be approved of in my PR. This would include the following:  
>   
>     cmdline from pid  
>     environ from pid  
>     cwd from pid  
>   
> note there is a more ideal way to read a cmdline on windows using COM which will work across 32-bit <> 64-bit x86 and will also not rely on the undocumented structure i am currently using for this on windows, but the COM implementation will not work on Windows executables that are run through WINE on non-Windows platforms so it is probably better to use my current approach depending on your preference. Either way it is a bit limiting for reasons given, regardless of approach.  
>   
> Only one other feature is currently planned:  
>   
>     get all hardlinks associated with file descriptor opened by process id  
>   
> This will rely on scanning the whole filesystem on OpenBSD while checking a match of pid, fd, dev, inode, and other things. There will be an error code when the file size and other things don't match up to indicate the file was either modified or there was a potential race condition. There will also be a timeout interval and an error code the user can check for and if they wish to continue the search they may do so in a while loop and pick up on traversing the filesystem where they left off. This is to avoid long hanging. On Windows i will likely need another private API or perhaps dll injection. The other platforms that I've messed with have much more clean solutions and are the OS developers' "best effort". The OpenBSD solution might have to be considered for other platforms as well depending on the API's available.  
  
Alternatively I could look into dll injection to get the 32-bit <-> 64-bit Windows communication more reliably, but that will take a while at this stage as DLL injection I've looked into in the past and it was fairly difficult to wrap my brain around with so little information on it floating around the web.  
  
In regards to the OpenBSD solution to get all hardlinks from a process id's opened file descriptor, you may refer to my somewhat incomplete answer on stackoverflow here which crashes when the function is called multiple times due to a memory leak I'm going to address:   
  
https://stackoverflow.com/a/70728234  
  
```  
#include <string>  
#include <vector>  
  
#include <cstdio>  
#include <cstring>  
  
#include <sys/stat.h>  
#include <fts.h>  
  
#include <sys/sysctl.h>  
#include <kvm.h>  
  
using std::string;  
using std::vector;  
  
string pidfd2path(int pid, int fd) {  
  string path; char errbuf[_POSIX2_LINE_MAX];  
  static kvm_t *kd = nullptr; kinfo_file *kif = nullptr; int cntp = 0;  
  kd = kvm_openfiles(nullptr, nullptr, nullptr, KVM_NO_FILES, errbuf); if (!kd) return "";  
  if ((kif = kvm_getfiles(kd, KERN_FILE_BYPID, pid, sizeof(struct kinfo_file), &cntp))) {  
    for (int i = 0; i < cntp; i++) {  
      if (kif[i].fd_fd == fd) {  
        FTS *file_system = nullptr; FTSENT *child = nullptr; FTSENT *parent = nullptr;  
        vector<char *> root; char buffer[2]; strcpy(buffer, "/"); root.push_back(buffer);  
        file_system = fts_open(&root[0], FTS_COMFOLLOW | FTS_NOCHDIR, nullptr);  
        if (file_system) {  
          while ((parent = fts_read(file_system))) {  
            child = fts_children(file_system, 0);  
            while (child && child->fts_link) {  
              child = child->fts_link;  
              if (!S_ISSOCK(child->fts_statp->st_mode)) {  
                if (child->fts_statp->st_dev == kif[i].va_fsid) {  
                  if (child->fts_statp->st_ino == kif[i].va_fileid) {  
                    path = child->fts_path + string(child->fts_name);  
                    goto finish;  
                  }  
                }  
              }  
            }  
          }  
          finish:  
          fts_close(file_system);   
        }  
      }  
    }  
  }  
  kvm_close(kd);  
  return path;  
}  
  
int main(int argc, char **argv) {  
  if (argc == 3) {  
    printf("%s\n", pidfd2path((int)strtoul(argv[1], nullptr, 10),   
      (int)strtoul(argv[2], nullptr, 10)).c_str());  
  } else {  
    printf("usage: \"%s\" <pid> <fd>\n", argv[0]);  
  }  
  return 0;  
}  
```  
  
There are still plenty of other things that are wrong with the above code. It needs to check for std::filesystem::equivalent-like functionality which includes much more than checking just the dev and inode. It would also need to check file size and time stamps, etc. and do appropriate error reporting and error codes for race conditions and for when the file has been modified like I mentioned earlier. For the sake on simplicity the above code only gets one matching hardlink via the goto statement. we could use the stat struct's st_nlink's member to determine when to stop searching and let that value be checked however often would be needed for most accurate results while still remaining reasonably performant.

---

## Comment 4

> Username: ghost  
> Created at: 2022-04-28 15:46:44 UTC  
> Updated at: 2022-05-06 10:23:26 UTC  
> Url: https://github.com/boostorg/process/issues/244#issuecomment-1112368762  

and last little note I wanted to make of here so I can remember to mention it in the documentation -  
  
All platforms retrieve the cmdline and environ values based on their current state they were in when the calling process tries to retrieve them -- except macOS and iOS --  they get the cmdline and environ based on the initial state of their values when the process is first launched, so if their values ever get modified you will not be able to get their values in a way that works the same on non-Apple/Darwin/XNU kernel platforms.  
  
Let's say i ran from the terminal:  
  
`env NAME=VALUE ./executable --arg1 --arg2 --arg3`  
  
then in the process's C++ source execution it eventually calls  
  
`setenv("NAME", "NEWVALUE")` // or the cmdline vector was modified too for example: `./application --param1 --param2 --param3`  
  
on mac/apple, it would return `NAME=VALE` and `./executable --arg1 --arg2 --arg3` no matter what because it only returns the initial state of those values.  
  
On other platforms, if the code execution reach a point where setenv was called and the cmdline vector were both modified like above, it would return the new values instead, thus it would return differently than the expected behavior on apple platforms:  
  
`NAME=NEWVALUE` and `./application --param1 --param2 --param3` for example.  
  
If an environment variable does not exist during in the parent process or that variable is not passed down to the child process, then apple platforms will not have that variable listed in that process's environ, because it did not yet exist during creation time.

---

## Comment 5

> Username: ghost  
> Created at: 2022-05-08 20:05:04 UTC  
> Url: https://github.com/boostorg/process/issues/244#issuecomment-1120479294  

I added full support for NetBSD since the time I posted this. :)

---

## Comment 6

> Username: klemens-morgenstern  
> Created at: 2022-05-11 03:27:10 UTC  
> Url: https://github.com/boostorg/process/issues/244#issuecomment-1123140457  

I can spin up some basic repo, so we do it outside of process. That wouldn't preclude it from being moved into process later.  
  
You got a projectg name idea for the meantime?  
  
I wrote you an email btw..

---

## Comment 7

> Username: ghost  
> Created at: 2022-05-11 05:12:15 UTC  
> Updated at: 2022-05-11 05:32:41 UTC  
> Url: https://github.com/boostorg/process/issues/244#issuecomment-1123193542  

@klemens-morgenstern hey! Sounds great.  
  
You were automatically put in my spam box so I didn't see it immediately. I marked you as "Not spam" and saved your contact. :)  
  
As for the name, I'm not sure. It would need to have some means to be differentiated from the other process libs. If you can't think of anything you find better, we can stick with xproc.  
  
  
I just fixed some bugs on NetBSD where getting the executable path and current working directory from a pid was done wrong initially. Also fixed some bugs with OpenBSD, DragonFly, macOS, and Linux, in regards to how getting a list of children processes from a parent is handled. I wanted all platforms to include a child of pid zero to be itself (child pid zero) because it is its own parent process. Those platforms didn't include process zero to be a child of itself, so I corrected that.  
  
This is the most well tested and stable my code has ever been for this atm.  
  
Also I wanted to thank you for your interest and allowing me to contribute. It really is an honor to be able to help the boost project grow in some way, as every little bit helps, and you all are very sharp people. I never would've thought I'd be able to do something this exciting.

---

## Comment 8

> Username: klemens-morgenstern  
> Created at: 2022-05-18 10:09:02 UTC  
> Url: https://github.com/boostorg/process/issues/244#issuecomment-1129823421  

Heya mate, I create a repo here (just put it in the boost namespace, being optimistic here) https://github.com/klemens-morgenstern/xproc  
  
You got access rights, so let's move the conversation over tehre.
