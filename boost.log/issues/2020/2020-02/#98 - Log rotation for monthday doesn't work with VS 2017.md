# #98 - Log rotation for monthday doesn't work with VS 2017 [Closed]

> Username: Trigve  
> Created at: 2020-02-05 10:36:36 UTC  
> Updated at: 2020-02-05 14:37:21 UTC  
> Closed at: 2020-02-05 14:36:59 UTC  
> Url: https://github.com/boostorg/log/issues/98  

The log rotation doesn't work for monthdays compiled with VS 2017 while application is running. On the application startup, the log is rotated correctly.  
  
I have a log with month rotation, rotating on 1st of every month (filename `journal_%Y_%m.log`)  with `rotation_at_time_point(boost::gregorian::greg_day(1))`. But when the new month comes and application is still running, the new log file isn't created and all the data goes to the "old month" log file.  
  
I've debugged it and it looks like the problems is the enum used as a bit field in https://github.com/boostorg/log/blob/10a63204b38bea26765e694d6342aaf3af7c9ec2/include/boost/log/sinks/text_file_backend.hpp#L239 AFAIK enums could be used as bit fields but the underlying type is implementation defined. And in VS it is int (see  https://stackoverflow.com/a/26728946/5360773) and if the `m_DayKind` is   
`monthday` it is stored as "-2" and then case at https://github.com/boostorg/log/blob/10a63204b38bea26765e694d6342aaf3af7c9ec2/src/text_file_backend.cpp#L1154 isn't triggered at all.  
  
The quick fix is to store `m_DayKind` as `unsigned char` and then cast it to `day_kind` when needed (that is in switch() statement, as it is used only there).

---

## Comment 1

> Username: Lastique  
> Created at: 2020-02-05 14:37:21 UTC  
> Url: https://github.com/boostorg/log/issues/98#issuecomment-582437686  

Thank you for the report.
