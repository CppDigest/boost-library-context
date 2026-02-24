# #199 - logging may substantially exceed total_size [Closed]

> Username: FransM  
> Created at: 2022-10-27 06:05:07 UTC  
> Updated at: 2022-11-04 20:43:07 UTC  
> Closed at: 2022-11-04 20:43:07 UTC  
> Url: https://github.com/boostorg/log/issues/199  

Issue: I had an application with a rotating log; that application crashed after just writing a few lines in the log.  
as this was a systemd service, systemd tried to restart the application with the result that the system ended up with a large number of log files that only contained a few 100 bytes.  
  
scan_for_files contains this code   
                                info.m_Size = filesystem::file_size(info.m_Path);  
                                total_size += info.m_Size;  
so it counts the actual file content.  
  
However the actual disk usage is much larger as each file will occupy a complete disk block (typically 4k on a linux system).  
So from a disk space perspective the total amount occupied by the logs is much larger.  
  
Suggestion is that the value added to total_size is rounded up to the nearest block boundary.   
So for 4k blocks it would be something like  
total_size += (info.m_SIze + 4091) / 4092) * 4092;  
(guess there is a standard function to round up that can be used as well)

---

## Comment 1

> Username: Lastique  
> Created at: 2022-10-27 09:44:00 UTC  
> Url: https://github.com/boostorg/log/issues/199#issuecomment-1293265782  

The actual disk space used is filesystem-specific and I do not want to bake in such specifics into the library. The limits are best effort and there are circumstances where they are not exact.  
  
Did you try using the `min_free_space` limit? I would expect the filesystem to report low free space in the conditions you describe.

---

## Comment 2

> Username: FransM  
> Created at: 2022-10-27 09:59:35 UTC  
> Url: https://github.com/boostorg/log/issues/199#issuecomment-1293283767  

min_free_space is not really something I want to use, I need to save as much room as possible for data on my system that is being produced.  
  
I understand fully that you do not want to drag in file system specifics.  
A possible solution is to use the posix function stat:  
https://pubs.opengroup.org/onlinepubs/009695399/basedefs/sys/stat.h.html  
  
The used space then can be determined by doing a stat call then from the result multiply the st_blksize  and st_blocks   fields  
That should be pretty platform and filesystem independent

---

## Comment 3

> Username: Lastique  
> Created at: 2022-10-27 10:59:13 UTC  
> Updated at: 2022-10-27 11:03:28 UTC  
> Url: https://github.com/boostorg/log/issues/199#issuecomment-1293352378  

As you can see in the `stat` documentation you linked:  
  
> The unit for the st_blocks member of the stat structure is not defined within IEEE Std 1003.1-2001. In some implementations it is 512 bytes. It may differ on a file system basis. There is no correlation between values of the st_blocks and st_blksize, and the f_bsize (from <sys/statvfs.h>) structure members.  
  
I know on some filesystems multiplying `st_blksize` and `st_blocks` would give the result that you expect, but on others it would not, so it's definitely not portable. For example, Linux man explicitly [says](https://linux.die.net/man/2/stat) `st_blocks` is the number of 512-byte blocks. Also note that AFAIK on BTRFS blocks can be shared between multiple files to store their tail bytes, so even if `st_blksize` and `st_blocks` are reported correctly, accumulating their products across multiple files would not give you the right picture. There are likely other cases where counting blocks makes less sense than one might guess initially.  
  
Besides, I'm having a hard time finding anything similar in Windows API. I found [`FILE_STANDARD_INFO::AllocationSize`](https://learn.microsoft.com/en-us/windows/win32/api/winbase/ns-winbase-file_standard_info), but I'm not sure how relevant it is. It is also only available since Windows Vista.

---

## Comment 4

> Username: Lastique  
> Created at: 2022-10-27 11:10:38 UTC  
> Url: https://github.com/boostorg/log/issues/199#issuecomment-1293365977  

One other limit you might try is `max_files`. If you know that your log files are rotated with a given interval (e.g. daily), you can plan on the number of the log files you normally need (e.g. 30, roughly for the last month). Then, in case if your app starts crashing/restarting, you won't exhaust the free space since no more than 30 log files will be maintained.

---

## Comment 5

> Username: FransM  
> Created at: 2022-10-27 11:21:44 UTC  
> Updated at: 2022-10-27 11:23:23 UTC  
> Url: https://github.com/boostorg/log/issues/199#issuecomment-1293378725  

Hm yeah, I did not consider block sharing.   
as for stat: it should be there on windows too. Then again I understand your concern on st_blksize. I thought it would be better standardized.  
As far as using a windows  function that is only available since Vista: I think all versions before Win10 are already EOL (although I am not 100% sure about win7.  
  
max_files would be an ideal solution but...  
I'm on boost 1.72 (that is the version that comes with yocto dunfell). We use rotation (daily and if the logfile grows too big).  
Issue/problem here is that if our application crashes and is restarted it will generate a new logfile even if the filemode is append.  
And since we get a new logfile on every restart, it could be that we loose the info in the log on what causes the issue.  
  
(currently we rotate daily and if the log file exceeds 10M, max allowed space is 70M)  
(the idea was that in normal situations we would be able to store the logs for a week, that triggers another suggestion: give the file collector an option to remove logs that are older than N days)

---

## Comment 6

> Username: Lastique  
> Created at: 2022-10-27 11:40:21 UTC  
> Url: https://github.com/boostorg/log/issues/199#issuecomment-1293399593  

One other data point re. blocks: the block count [may be zero](https://github.com/Microsoft/WSL/issues/2844#issuecomment-357755314) for small enough files if the file data is embedded in inode/MFT/whatever the filesystem has. In fact, I suspect that is what happened in your case - you ran out of inodes on the filesystem. This condition is not detected using filesystem free space or max total file size metrics. Actually, I'm not aware how one is supposed to detect this, let alone portably - there is no way to know the free space for inodes, which may be lower than the free space for data.  
  
> Issue/problem here is that if our application crashes and is restarted it will generate a new logfile even if the filemode is append.  
And since we get a new logfile on every restart, it could be that we loose the info in the log on what causes the issue.  
  
Is it not written in the small log file that was left from the crash?  
  
> the idea was that in normal situations we would be able to store the logs for a week, that triggers another suggestion: give the file collector an option to remove logs that are older than N days  
  
File collector already removes the oldest files first, so you kind of have this behavior already, only without a strict age limit. I could add a max age limit, but I don't see how it would help your case.

---

## Comment 7

> Username: FransM  
> Created at: 2022-10-27 11:58:34 UTC  
> Url: https://github.com/boostorg/log/issues/199#issuecomment-1293419000  

no, I did not run out of i-nodes, nor was data embedded in the inode./MFT or so.   
I added some logging in the text backend and noticed that it gets the size of the files in it.  
  
THere is a way to find out if there are free inodes (maybe also in stat) but that is very linux specific.  
  
The small log typically indicates a failure caused by e.g. a faulty file, but the interesting part is often how this file is acutally created.   
max_age_limit would not help me too much; what would help is if I could have append on reboot, but (at least for 1.72) it does not work in conjunction with rotation.  
  
I suspect things have changed in the latest version. That is if I look at the text_file_backend.cpp on github it looks quite different from what I see in 1.72.   
A while ago I tried upgrading to 1.80 but that gave some issues. Forgot which ones, could be some deprecated methods were gone, could be it didn't work too well with g++14

---

## Comment 8

> Username: Lastique  
> Created at: 2022-10-27 13:16:41 UTC  
> Url: https://github.com/boostorg/log/issues/199#issuecomment-1293512831  

> I added some logging in the text backend and noticed that it gets the size of the files in it.  
  
The file size, as reported by `filesystem::file_size` and `stat`, will be non-zero for files embedded in inodes. The only way to know is to check the block count, and neither Boost.Log nor Boost.Filesystem currently does that.  
  
> if I could have append on reboot, but (at least for 1.72) it does not work in conjunction with rotation.  
  
There are limitations wrt. using both appending and file rotation, but it can be set up. Here are a few suggestions:  
  
- Use a stable filename in `file_name` with no date/time placeholders. Use `target_file_name` to rename the file before rotation.  
- Disable final rotation (`backend->enable_final_rotation(false);`).  
  
This will ensure the log file is found at the same place and under the same name as they were on the previous run.

---

## Comment 9

> Username: Lastique  
> Created at: 2022-11-04 20:43:07 UTC  
> Url: https://github.com/boostorg/log/issues/199#issuecomment-1304218908  

Since I'm not going to add filesystem-specific assumptions to Boost.Log, I'm going to close this. If there is demand I could add an abstraction layer for querying block counts in Boost.Filesystem and then add a corresponding limit in Boost.Log in the future. But for now, I think, the current limits should do fine.
