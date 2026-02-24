# #95 - Logs with timestamp wildcard names not rotating/appending across days [Closed]

> Username: raemarks  
> Created at: 2019-12-19 20:37:17 UTC  
> Updated at: 2020-08-20 17:18:52 UTC  
> Closed at: 2020-08-20 17:18:52 UTC  
> Url: https://github.com/boostorg/log/issues/95  

### Background/setup:  
I'm using Boost 1.69.  
  
My log file names include the wildcard `"_%Y_%m_%d_%2N`. I have file rotation set up to rotate at time point `(0.0.0)` and at 5MB size. They rotate into a subdirectory of my main log dir. I call `scan_for_files()` on the backend before adding the sink.  
  
### Issue:  
Let's say I was running my application on Wednesday, then I stop it. Then I start it up again on Thursday. Logging does not start appending to the Wednesday file. Additionally, the Wednesday file does not get rotated into my rotation directory, so my main log dir keeps filling up with old log files.   
  
I could grep around and match the wildcard on startup and manually move them to the rotation dir but it's hacky and doesn't work well if my log files have common prefixes. I looked at the documentation for `scan_for_files` but it appears that it won't rotate for you, it's only useful for incrementing wildcards.  
  
Otherwise, it works perfectly.  
  
### Request:  
It would be great if there was a method to match all old wildcards and rotate them out, or of `scan_for_files()` could be expanded to accept a visitor. Then, the developer could specify the listener to call `rotate()` on that current file, or do whatever. That said, I'm not sure that `scan_for_files()` matches on previous dates.

---

## Comment 1

> Username: Lastique  
> Created at: 2019-12-19 20:57:39 UTC  
> Url: https://github.com/boostorg/log/issues/95#issuecomment-567673937  

The default behavior is to rotate the current log file when the sink is destroyed (which usually happens when your application terminates). If that doesn't happen in your case then either you disabled this functionality (e.g. by calling [`enable_final_rotation`](https://github.com/boostorg/log/blob/b395be29c24791d40cfd16662d8966895626c150/include/boost/log/sinks/text_file_backend.hpp#L510)) or your application terminates abnormally (i.e. crashes). If you fix that the file should get rotated on application exit, but you'll obviously lose the ability to append after restart.  
  
Either way, the library does not manage the files in the directory where the active log file is being written. `scan_for_files` works on the *target* directory, where the rotated files are being put. So, neither `scan_for_files` nor `rotate` can pick up any stray files that may be lying around.

---

## Comment 2

> Username: raemarks  
> Created at: 2019-12-19 21:18:49 UTC  
> Url: https://github.com/boostorg/log/issues/95#issuecomment-567680902  

Thanks for the clarification on `scan_for_files`, this all makes sense. I do `pkill` the process occasionally for development since it's daemonized, which would explain it.  
  
That said, there are real world situations when an application crashes and/or doesn't exit the way it should. It would be nice to have a way to clean up these extra log files instead of them sitting indefinitely

---

## Comment 3

> Username: Lastique  
> Created at: 2019-12-19 21:33:55 UTC  
> Url: https://github.com/boostorg/log/issues/95#issuecomment-567685791  

Unfortunately, there is no reliable way to know which files are leftover log files, especially when the file naming pattern changes from run to run. The target directory is important in this regard because we can assume all files in it are log files.

---

## Comment 4

> Username: raemarks  
> Created at: 2019-12-19 21:38:08 UTC  
> Updated at: 2019-12-19 22:02:30 UTC  
> Url: https://github.com/boostorg/log/issues/95#issuecomment-567687129  

Can't we match the wildcard? I am logging to a target directory which I only use for this purpose. It's a tradeoff I suppose - make it easy to clean up in case something unusual happens (which is bound to eventually happen), or allow for some programmer error by having ambiguous log file names or things which could accidentally get rotated. For now, on startup, I'll probably just manually rotate everything in the directory if it exists.

---

## Comment 5

> Username: Lastique  
> Created at: 2020-08-20 17:18:52 UTC  
> Url: https://github.com/boostorg/log/issues/95#issuecomment-677793966  

I think your setup is rather specialized. I don't quite like the idea of having to manage the active log file directory, in addition to the target directory. Not in the least because this can cause user's data loss and corruption.  
  
Note that killing processes or crashing a process at random points makes this even less desirable because you generally don't know at which point the previous process was stopped. For perspective, there are two file name patterns that are used to generate filenames in the active directory, and moving a file to the target directory is also not atomic. Plus, as I said, there may be unrelated files in the active directory. In general, this makes it hard to predict the state of the active directory and what to do about it in a library like Boost.Log.  
  
Your best bet is to do the cleanup on your end, given that you know what you want to do with each file.
