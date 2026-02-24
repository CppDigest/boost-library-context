# #221 - Coverity security issue (minor) [Closed]

> Username: jeking3  
> Created at: 2019-05-04 00:24:04 UTC  
> Updated at: 2020-04-21 14:23:36 UTC  
> Closed at: 2020-03-30 07:02:33 UTC  
> Url: https://github.com/boostorg/test/issues/221  

```  
*** CID 194947:  Security best practices violations  (SECURE_TEMP)  
/home/travis/build/boostorg/boost-root/boost/test/impl/debug.ipp: 425 in _ZN5boost5debug12_GLOBAL__N_1L21prepare_gdb_cmnd_fileERKNS0_16dbg_startup_infoE()  
419         ::snprintf( pid_buff, sizeof(pid_buff), "%ld", dsi.pid );  
420         unit_test::const_string pid_str( pid_buff );  
421       
422         static char cmd_file_name[] = "/tmp/btl_gdb_cmd_XXXXXX"; // !! ??  
423       
424         // prepare commands  
>>>     CID 194947:  Security best practices violations  (SECURE_TEMP)  
>>>     Calling "mkstemp" without securely setting umask first.  
425         fd_holder cmd_fd( ::mkstemp( cmd_file_name ) );  
426       
427         if( cmd_fd == -1 )  
428             return 0;  
429       
430     #define WRITE_STR( str )  if( ::write( cmd_fd, str.begin(), str.size() ) == -1 ) return 0;  
```

---

## Comment 1

> Username: raffienficiaud  
> Created at: 2019-12-12 21:13:05 UTC  
> Url: https://github.com/boostorg/test/issues/221#issuecomment-565189142  

Tentative fix in branch `topic/GH-221-coverity-mkstemp-warning`. I am not sure about the resolution but this is what I understood from the investigations. Tests running.

---

## Comment 2

> Username: raffienficiaud  
> Created at: 2020-03-30 07:02:33 UTC  
> Url: https://github.com/boostorg/test/issues/221#issuecomment-605820190  

Fixed in master
