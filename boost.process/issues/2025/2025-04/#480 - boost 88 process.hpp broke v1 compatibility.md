# #480 - boost 88 process.hpp broke v1 compatibility [Open]

> Username: fsmoke  
> Created at: 2025-04-11 13:56:21 UTC  
> Updated at: 2026-01-14 11:51:26 UTC  
> Url: https://github.com/boostorg/process/issues/480  

boost 88 process.hpp broke v1 compatibility, now there is no common header for v1 - we ware enforced to fix dozens of projects !!   
  
As far as i know there is no groups support in v2 for now - why compat was broken? without any alternatives...

---

## Comment 1

> Username: klemens-morgenstern  
> Created at: 2025-04-11 15:20:22 UTC  
> Url: https://github.com/boostorg/process/issues/480#issuecomment-2797215531  

I can add a `process/v1.hpp` header for 1.89 if that's all you need. If you `#define BOOST_PROCESS_VERSION 1` the API would be the same.  
  
What's your use-case for process groups? I did not add them to v2 because they are too different between posix & windows.

---

## Comment 2

> Username: fsmoke  
> Created at: 2025-04-11 15:59:36 UTC  
> Updated at: 2025-04-11 16:00:54 UTC  
> Url: https://github.com/boostorg/process/issues/480#issuecomment-2797335257  

> I can add a process/v1.hpp header for 1.89 if that's all you need  
  
Yes this whould be nice  
  
we have some kind of shell launcher which run multiple child process - theese children run subchild processes and so on. But when we close/kill shell(or it crashes) - all subprocesses and subsubprocesses must be guaranteed closed  
  
in other words we use groups to automatic kill all (child)processes in group

---

## Comment 3

> Username: klemens-morgenstern  
> Created at: 2025-04-11 16:01:46 UTC  
> Url: https://github.com/boostorg/process/issues/480#issuecomment-2797341869  

Are you running windows & posix or just one?

---

## Comment 4

> Username: fsmoke  
> Created at: 2025-04-12 06:17:47 UTC  
> Url: https://github.com/boostorg/process/issues/480#issuecomment-2798519967  

Yes, we have cross platform product for windows and couple of linux distros

---

## Comment 5

> Username: Nelson-numerical-software  
> Created at: 2025-04-14 17:40:03 UTC  
> Url: https://github.com/boostorg/process/issues/480#issuecomment-2802425547  

Will v1 compatibility continue to be maintained or will we start migrating to v2?

---

## Comment 6

> Username: nega0  
> Created at: 2025-04-17 18:57:06 UTC  
> Url: https://github.com/boostorg/process/issues/480#issuecomment-2813784079  

If this bit you too, and you got something like  
  
```console  
$ make |& grep error: |& cut -d: -f4-  
 error: no type named 'child' in namespace 'boost::process'  
 error: no type named 'opstream' in namespace 'boost::process'  
 error: no member named 'std_in' in namespace 'boost::process'  
 error: no member named 'std_out' in namespace 'boost::process'  
 error: no member named 'null' in namespace 'boost::process'  
 error: no member named 'std_err' in namespace 'boost::process'  
 error: no member named 'start_dir' in namespace 'boost::process'  
 error: no member named 'child' in namespace 'boost::process'  
```  
  
You now want something like  
```diff  
@@ -1,2 +1,11 @@  
+#include <boost/version.hpp>  
+#if BOOST_VERSION < 108800  
 #include <boost/process.hpp>  
 namespace process = boost::process;  
+#else  
+#include <boost/process/v1/child.hpp>  
+#include <boost/process/v1/io.hpp>  
+#include <boost/process/v1/pipe.hpp>  
+#include <boost/process/v1/start_dir.hpp>  
+namespace process = boost::process::v1;  
+#endif  
```

---

## Comment 7

> Username: klemens-morgenstern  
> Created at: 2025-04-18 14:27:57 UTC  
> Url: https://github.com/boostorg/process/issues/480#issuecomment-2815554171  

You can also define `BOOST_PROCESS_VERSION 1` so all the v1 symbols are in `boost::process` and not `boost::process::v1`.

---

## Comment 8

> Username: jakobandersen  
> Created at: 2025-04-23 09:02:34 UTC  
> Url: https://github.com/boostorg/process/issues/480#issuecomment-2823579464  

I was hit by the changes in 1.88 as well, in that I didn't even know v2 existed. Is there a changelog somewhere of what happened in each release? The overall Boost release notes have no entries for Process that I could find in multiple releases.

---

## Comment 9

> Username: Nelson-numerical-software  
> Created at: 2025-04-23 18:05:23 UTC  
> Url: https://github.com/boostorg/process/issues/480#issuecomment-2825110229  

Will compatibility with version 1 continue to be maintained in future releases, or migration to version 2 is required ?

---

## Comment 10

> Username: klemens-morgenstern  
> Created at: 2025-04-28 15:55:57 UTC  
> Url: https://github.com/boostorg/process/issues/480#issuecomment-2835727000  

There is documentation of the v2 IF here: https://www.boost.org/doc/libs/1_88_0/libs/process/doc/html/index.html#version_2  
The v2 was added several versions ago, I think in 1.81. I sadly have to admit that I am bad at release notes.  
  
I will keep the v1 sources around as is and may accept PRs. The v1 interface has major problems, so I had to introduce a breaking change at some point. I would strongly encourage migration.

---

## Comment 11

> Username: nigels-com  
> Created at: 2025-04-29 22:03:19 UTC  
> Url: https://github.com/boostorg/process/issues/480#issuecomment-2840344177  

We did a migration to "get with the program" and modernise some surrounding mess of our own making.  
It was the main blocker for moving to boost 1.88.0 and it does seem like we're safely on the other side now.  
One positive thing for us is that it aligns better with boost::asio, which we also have in the mix.

---

## Comment 12

> Username: toonetown  
> Created at: 2025-05-02 01:04:34 UTC  
> Url: https://github.com/boostorg/process/issues/480#issuecomment-2846088927  

Is there some kind of migration guide to move from v1 to v2?  For example, I cannot find anywhere where the v2 equivalent of `boost::process::v1::spawn` would be.

---

## Comment 13

> Username: klemens-morgenstern  
> Created at: 2025-05-02 17:34:35 UTC  
> Url: https://github.com/boostorg/process/issues/480#issuecomment-2847750114  

You can find a summary here: https://www.boost.io/doc/libs/latest/doc/html/process.html#version_2  
  
`spawn(...)` would be `process(...).detach()`.

---

## Comment 14

> Username: marek22k  
> Created at: 2025-05-30 00:05:17 UTC  
> Url: https://github.com/boostorg/process/issues/480#issuecomment-2920849362  

I have the same problem with my project. It compiles on Debian, but not on Arch: `error: ‘child’ is not a member of ‘boost::process’`  
  
I want to convert the following code to v2:  
```cpp  
for (const auto& postup_command : this->_postup_commands)  
{  
    BOOST_LOG_TRIVIAL(debug)  
        << "Execute post up command: " << postup_command << std::endl;  
    std::error_code ec;  
    boost::process::child child(  
        postup_command,  
        boost::process::std_out > boost::process::null,  
        boost::process::std_err > boost::process::null,  
        ec);  
    child.wait();  
    BOOST_LOG_TRIVIAL(debug)  
        << "Post up command result: " << child.exit_code() << std::endl;  
    if (child.exit_code() != 0 || ec)  
    {  
        if (ec)  
        {  
            BOOST_LOG_TRIVIAL(fatal)  
                << "Failed to execute post up command: " << ec.message()  
                << std::endl;  
        }  
        throw std::runtime_error("Failed to execute post up command.");  
    }  
}  
```  
`postup_command` may also contain full commands like `ip set ...`.  
But  
```cpp  
for (const auto& postup_command : this->_postup_commands)  
{  
    BOOST_LOG_TRIVIAL(debug)  
        << "Execute post up command: " << postup_command << std::endl;  
    boost::process::process child(ex, postup_command, {}, boost::process::process_stdio{.in=nullptr, .out=nullptr, .err=nullptr});  
    child.wait();  
    BOOST_LOG_TRIVIAL(debug)  
        << "Post up command result: " << child.exit_code() << std::endl;  
    if (child.exit_code() != 0)  
    {  
        throw std::runtime_error("Failed to execute post up command.");  
    }  
}  
```  
results in  
```  
[fatal]   Error: default_launcher: No such file or directory [system:2 at /usr/include/boost/process/v2/posix/default_launcher.hpp:409:17 in function 'boost::process::v2::basic_process<Executor> boost::process::v2::posix::default_launcher::operator()(Executor, boost::system::error_code&, const typename std::enable_if<(boost::asio::execution::is_executor<T>::value || boost::asio::is_executor<Executor>::value), boost::filesystem::path>::type&, Args&&, Inits&& ...)']  
```  
  
Is there a kind of migration guide?

---

## Comment 15

> Username: klemens-morgenstern  
> Created at: 2025-06-02 09:59:09 UTC  
> Url: https://github.com/boostorg/process/issues/480#issuecomment-2929784152  

You cannot use command strings directly to start a process anymore.   
  
You either use the shell explicitly (`process child(ex, "/bin/sh", {"-c", postup_command})`) or you can use [shell](https://www.boost.org/doc/libs/latest/doc/html/process.html#shell_hpp) to parse the command & look up the exe from path.  
  
```cpp  
    boost::process::shell postup_shell(postup_command);  
    boost::process::process child(ex, postup_shell.exe(), postup_shell.args() boost::process::process_stdio{.in=nullptr, .out=nullptr, .err=nullptr});  
```

---

## Comment 16

> Username: timrae  
> Created at: 2025-06-26 13:19:28 UTC  
> Url: https://github.com/boostorg/process/issues/480#issuecomment-3008474139  

> I can add a process/v1.hpp header for 1.89 if that's all you need  
  
Yes please!! It's very difficult to do the update to 1.88 without this header for some code

---

## Comment 17

> Username: klemens-morgenstern  
> Created at: 2025-06-26 14:23:04 UTC  
> Url: https://github.com/boostorg/process/issues/480#issuecomment-3008680692  

added: https://github.com/boostorg/process/blob/develop/include/boost/process/v1.hpp

---

## Comment 18

> Username: martin5233  
> Created at: 2025-06-30 09:33:04 UTC  
> Url: https://github.com/boostorg/process/issues/480#issuecomment-3018458114  

I also have a similar use case for process groups, which I cannot migrate. For now I have left that code to use V1, but it would be very helpful to have process groups in V2 as well.

---

## Comment 19

> Username: klemens-morgenstern  
> Created at: 2025-06-30 13:35:39 UTC  
> Url: https://github.com/boostorg/process/issues/480#issuecomment-3019191692  

As stated above: job objects (windows) & process groups  (posix) are too different to consider them portable.  
  
Writing custom initializers is pretty easy.

---

## Comment 20

> Username: marek22k  
> Created at: 2025-10-10 18:42:48 UTC  
> Url: https://github.com/boostorg/process/issues/480#issuecomment-3391792902  

> You cannot use command strings directly to start a process anymore.  
>   
> You either use the shell explicitly (`process child(ex, "/bin/sh", {"-c", postup_command})`) or you can use [shell](https://www.boost.org/doc/libs/latest/doc/html/process.html#shell_hpp) to parse the command & look up the exe from path.  
>   
>     boost::process::shell postup_shell(postup_command);  
>     boost::process::process child(ex, postup_shell.exe(), postup_shell.args() boost::process::process_stdio{.in=nullptr, .out=nullptr, .err=nullptr});  
  
Thanks for the reply!  
I just tried it out in more detail. It works. If I use something like `ip ...`, it executes. However, with `/usr/bin/ip`, it no longer executes - then `.exe()` returns an empty string and thus leading to `default_launcher: No such file or directory`.  
Is there a way for the shell to recognize both shell commands and absolute paths?

---

## Comment 21

> Username: klemens-morgenstern  
> Created at: 2025-10-13 03:49:45 UTC  
> Url: https://github.com/boostorg/process/issues/480#issuecomment-3395766279  

`boost::process::environment::find_executable("ip")` gives you the full path if available.

---

## Comment 22

> Username: jwakely  
> Created at: 2026-01-14 10:29:14 UTC  
> Url: https://github.com/boostorg/process/issues/480#issuecomment-3748855762  

> There is documentation of the v2 IF here: https://www.boost.org/doc/libs/1_88_0/libs/process/doc/html/index.html#version_2 The v2 was added several versions ago, I think in 1.81. I sadly have to admit that I am bad at release notes.  
  
Please update the docs to say which version of Boost introduced v2, and which version made it the default. Especially since you've removed the docs for v1, so users need to go and look at the docs in old releases, but they don't know _which_ old releases because the docs don't say anything about when the changes happened.  
  
  
> You can find a summary here: https://www.boost.io/doc/libs/latest/doc/html/process.html#version_2  
  
It doesn't mention that there is no `search_path` in v2.

---

## Comment 23

> Username: jwakely  
> Created at: 2026-01-14 11:51:26 UTC  
> Url: https://github.com/boostorg/process/issues/480#issuecomment-3749203425  

> It doesn't mention that there is no `search_path` in v2.  
  
Edit: looks like the replacement is `environment::find_executable`? Maybe mention at https://www.boost.org/doc/libs/latest/libs/process/doc/html/index.html#environment that it replaces `v1::search_path`, for people trying to find info on how to migrate.
