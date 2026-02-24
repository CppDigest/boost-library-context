# #67 - Rotating Log writes to original file? [Closed]

> Username: lijh8  
> Created at: 2018-12-06 02:39:58 UTC  
> Updated at: 2018-12-07 09:54:58 UTC  
> Closed at: 2018-12-07 09:54:57 UTC  
> Url: https://github.com/boostorg/log/issues/67  

Can Boost Log write data always to the original file.  
And the old data stored in file with name with pattern.  
  
So the newly added log data can always be checked via tail like this:  
  $ tail -f ~/message.log    
  (like: tail -f /var/log/message)  
  
Old data stored when it reach limit in file with pattern:  
  ~/old_log/message_20181206_113059_00001.log

---

## Comment 1

> Username: Lastique  
> Created at: 2018-12-06 09:17:04 UTC  
> Url: https://github.com/boostorg/log/issues/67#issuecomment-444803396  

No, this behavior is currently not supported. The file name is generated once, when the file is created, not when the file is rotated (therefore the timestamp in the file name corresponds to the file creation).

---

## Comment 2

> Username: lijh8  
> Created at: 2018-12-06 14:45:19 UTC  
> Updated at: 2018-12-06 14:59:28 UTC  
> Url: https://github.com/boostorg/log/issues/67#issuecomment-444894492  

Thanks!  
  
I have some more questions.  
  
Can I set a directory for log file (its name given by file_name) like I can set target for rotated log file. Or does it always write log file under the current working directory?  
  
I used to print program log to stdout and redirect log with nohup. I tried the logrotate utility (without copytruncate). When it rotates log, the original file becomes zero bytes. Maybe it caused by me not setting up pre- script and not responding in my program to HUP signal sent from pre- spript.  
  
I made a convenient macro around your Log library:  
BLOG(info) << "An informational severity message";  
BLOG(error) << "An error severity message";  
  
It generated nice log like this:  
2: [20181203 175935.575466] [info] - a.cpp:130 (main): An informational severity message  
3: [20181203 175935.575473] [error] - a.cpp:131 (main): A error severity message

---

## Comment 3

> Username: Lastique  
> Created at: 2018-12-06 15:17:35 UTC  
> Url: https://github.com/boostorg/log/issues/67#issuecomment-444906395  

> Can I set a directory for log file (its name given by file_name) like I can set target for rotated log file.  
  
Yes. If you're using the `add_file_log` helper, you can specify the target directory in the `target` named parameter. If you're configuring sinks manually, you can set the target directory by configuring a file collector. See [here](https://www.boost.org/doc/libs/1_68_0/libs/log/doc/html/log/detailed/sink_backends.html#log.detailed.sink_backends.text_file.managing_rotated_files).  
  
> I used to print program log to stdout and redirect log with nohup. I tried the logrotate utility (without copytruncate). When it rotates log, the original file becomes zero bytes.  
  
The sink will not reopen or recreate the log file until it is rotated. So, after you or logrotate deletes the file that is currently being written, all subsequent log records will be lost until the rotation.  
  
If you want to rotate log files on external events, such as `SIGHUP`, you should call `rotate_file` on the sink backend when you register that event. Note that you cannot call that function in a signal handler, you'll have to call it after you return from the signal handler.

---

## Comment 4

> Username: lijh8  
> Created at: 2018-12-06 16:39:25 UTC  
> Updated at: 2018-12-06 16:45:52 UTC  
> Url: https://github.com/boostorg/log/issues/67#issuecomment-444938905  

Hi Andrey,  
  
 `       keywords::file_name = "sample_%N.log",`  
  
I meant the directory for the current file beeing written before its been rotated (not the target parameter). Can I set the directory for the current log file?  
  
And are LineID & RecordID the same thing?

---

## Comment 5

> Username: Lastique  
> Created at: 2018-12-06 16:45:43 UTC  
> Url: https://github.com/boostorg/log/issues/67#issuecomment-444941582  

> Can I set the directory for the current log file?  
  
Yes, the directory can be part of the path you specify in the `file_name` parameter.  
  
> And are LineID & RecordID the same thing?  
  
Depends on what you mean by them. The library itself does not put any meaning in attributes, except for `Message`.

---

## Comment 6

> Username: lijh8  
> Created at: 2018-12-07 08:00:02 UTC  
> Updated at: 2018-12-07 08:02:50 UTC  
> Url: https://github.com/boostorg/log/issues/67#issuecomment-445152970  

```  
0: 20181207 155113.947575 | info - main.cpp:64 (main): /home/lijh/test/MatchNg.BTC_USDT/MatchNg.BTC_USDT start  
1: 20181207 155113.947950 | info - Resend.cpp:12 (Resend): Resend loop starts  
3: 20181207 155113.948322 | info - MatchProc.cpp:19 (MatchProc): Match loop starts  
2: 20181207 155113.948108 | info - MsgProc.cpp:27 (MsgProc): Msg loop starts  
4: 20181207 155213.949911 | info - Resend.cpp:12 (Resend): Resend loop starts  
```  
  
Hi Andrey,  
  
`3: 20181207 155113.948322 ...`  
  
I'm logging in multiple thread program. This log entry has bigger RecordID and timestamp. Why it appears before the next entry? Is it reasonable?

---

## Comment 7

> Username: Lastique  
> Created at: 2018-12-07 09:54:57 UTC  
> Url: https://github.com/boostorg/log/issues/67#issuecomment-445180816  

https://www.boost.org/doc/libs/1_68_0/libs/log/doc/html/log/rationale/why_weak_record_ordering.html
