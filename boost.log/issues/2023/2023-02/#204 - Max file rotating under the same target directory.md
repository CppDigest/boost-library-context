# #204 - Max file rotating under the same target directory [Closed]

> Username: ropieur  
> Created at: 2023-02-14 11:06:53 UTC  
> Updated at: 2023-02-14 15:22:51 UTC  
> Closed at: 2023-02-14 15:22:50 UTC  
> Url: https://github.com/boostorg/log/issues/204  

Hi,  
  
I have 2 sinks targetting different filename patterns, but targetting the same directory, each of them configured to have maximum 5 files.  
The problem is that the number of files is 5 for the total of both file sinks. I could increase the max files to 10, but this is counter-intuitive and even then, I am not sure to end up with 5 files of each, depending on the log usage.  
The point is that file sinks are configurable in my application, and targetting different directories leads to the expected behaviour (max 5 files for each file sink) and if configured to the same directory, it leads to a different behaviour (max 5 files in total for both sinks).  
Is there a way to have max 5 files per sink whatever the target directory without rewriting completely the file collector ?  
  
Thank you

---

## Comment 1

> Username: Lastique  
> Created at: 2023-02-14 15:22:50 UTC  
> Url: https://github.com/boostorg/log/issues/204#issuecomment-1429923748  

The limits are maintained by the file collector, not the sink. File collectors are created per target directory, which means if multiple sinks store their files in the same target directory, they will use a single file collector. File collectors don't discriminate between files produced by different sinks. So, in order to have separate limits, including on the max number of stored files, you will have to use different target directories.  
  
This design is deliberate. Some of the limits are filesystem-oriented, e.g. the min free space is a filesystem property, and it doesn't make sense to have different limits for the same directory (which means the same filesystem). Furthermore, the information about which sink produced which files is not maintained or preserved across process restarts. So if multiple file collectors were created for the same target directory, they would not be able to deduce which files which and would conflict when maintaining their set limits.
