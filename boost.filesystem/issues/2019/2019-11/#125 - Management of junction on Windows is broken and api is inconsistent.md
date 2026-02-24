# #125 - Management of junction on Windows is broken and api is inconsistent [Closed]

> Username: ghost  
> Created at: 2019-11-21 19:46:54 UTC  
> Updated at: 2020-05-02 17:18:28 UTC  
> Closed at: 2020-05-02 17:18:28 UTC  
> Url: https://github.com/boostorg/filesystem/issues/125  

This bug was originally posted on boost-users mailing list on 06th November 2019 but since there is no answer I am reporting the bug here:  
http://boost.2283326.n4.nabble.com/filesystem-Management-of-junction-on-Windows-is-broken-and-api-is-inconsistent-td4710249.html  
  
  
I am bit disappointed to see that symlink and junctions are still not really properly handled on Windows with boost (and even with standard MS libs but that's another story.)  
First my environment: Windows 10, VS2019 16.3.7, boost 1.71 (static, x64).  
I would like to write a small c++ utility that does not use os dependent code to handle files so filesystem seems a good candidate.  
  
Here is what I am doing on Windows:  
  
cd "C:\Users\Vincent\AppData\Roaming\Apple Computer\MobileSync"  
mklink /j Backup E:\Backup  
  
So basically I am creating inside C:\..\MobileSync a junction folder that points to my second harddisk E:\Backup  
  
Then I am checking that Windows recognize it by entering dir command:  
  
01/11/2019  23:48    <DIR>          .  
01/11/2019  23:48    <DIR>          ..  
01/11/2019  23:29    <JUNCTION>     Backup [E:\Backup]  
               0 fichier(s)                0 octets  
               3 Rép(s)  59 393 630 208 octets libres  
  
As you can see I have a junction targeting E:\Backup, so now let's see what happens with boost:  
  
```  
fs::path p(_T("C:\\Users\\Vincent\\AppData\\Roaming\\Apple Computer\\MobileSync\\Backup"));  
  
sys::error_code ec;  
if (fs::is_symlink(p))  
{  
     fs::path pathTarget = fs::read_symlink(p, ec);  
}  
```  
When I display the content of pathTarget I have Backup...  
I might not understand anything about junction but is it not supposed to return E:\Backup ??  
  
When I look at source code I can see the following lines:  
```  
/********************** boost_1_71_0\libs\filesystem\src\operations.cpp **************************************/  
symlink_path.assign(  
  
static_cast<wchar_t*>(info.rdb.SymbolicLinkReparseBuffer.PathBuffer)  
        + info.rdb.SymbolicLinkReparseBuffer.PrintNameOffset/sizeof(wchar_t),  
  
static_cast<wchar_t*>(info.rdb.SymbolicLinkReparseBuffer.PathBuffer)  
        + info.rdb.SymbolicLinkReparseBuffer.PrintNameOffset/sizeof(wchar_t)  
        + info.rdb.SymbolicLinkReparseBuffer.PrintNameLength/sizeof(wchar_t));  
```  
  
This is wrong since we need to distinguish between a symlink and a junction folder so here is my contribution :  
```  
--- a/src/operations.cpp  
+++ b/src/operations.cpp  
@@ -201,6 +201,12 @@ typedef struct _REPARSE_DATA_BUFFER {  
 # ifndef IO_REPARSE_TAG_SYMLINK  
 #   define IO_REPARSE_TAG_SYMLINK (0xA000000CL)  
 # endif  
+# ifndef IO_REPARSE_TAG_MOUNT_POINT  
+#   define IO_REPARSE_TAG_MOUNT_POINT (0xA0000003L)  
+# endif  
+# ifndef IO_REPARSE_TAG_DEDUP  
+#   define IO_REPARSE_TAG_DEDUP (0x80000013L)  
+# endif  
  
 inline std::wstring wgetenv(const wchar_t* name)  
 {  
@@ -1661,12 +1667,24 @@ namespace detail  
     if (!error(::DeviceIoControl(h.handle, FSCTL_GET_REPARSE_POINT,  
           0, 0, info.buf, sizeof(info), &sz, 0) == 0 ? BOOST_ERRNO : 0, p, ec,  
           "boost::filesystem::read_symlink" ))  
+       if (info.rdb.ReparseTag == IO_REPARSE_TAG_SYMLINK)  
+       {  
       symlink_path.assign(  
  
static_cast<wchar_t*>(info.rdb.SymbolicLinkReparseBuffer.PathBuffer)  
         + info.rdb.SymbolicLinkReparseBuffer.PrintNameOffset/sizeof(wchar_t),  
  
static_cast<wchar_t*>(info.rdb.SymbolicLinkReparseBuffer.PathBuffer)  
         + info.rdb.SymbolicLinkReparseBuffer.PrintNameOffset/sizeof(wchar_t)  
         + info.rdb.SymbolicLinkReparseBuffer.PrintNameLength/sizeof(wchar_t));  
+       }  
+       else if (info.rdb.ReparseTag == IO_REPARSE_TAG_MOUNT_POINT)  
+       {  
+          symlink_path.assign(  
+ static_cast<wchar_t*>(info.rdb.MountPointReparseBuffer.PathBuffer)  
+             + info.rdb.MountPointReparseBuffer.PrintNameOffset / sizeof(wchar_t),  
+ static_cast<wchar_t*>(info.rdb.MountPointReparseBuffer.PathBuffer)  
+             + info.rdb.MountPointReparseBuffer.PrintNameOffset / sizeof(wchar_t)  
+             + info.rdb.MountPointReparseBuffer.PrintNameLength / sizeof(wchar_t));  
+       }  
 #     endif  
     return symlink_path;  
   }   
```

---

## Comment 1

> Username: Flamefire  
> Created at: 2020-01-07 19:04:52 UTC  
> Url: https://github.com/boostorg/filesystem/issues/125#issuecomment-571725512  

Would be fixed by https://github.com/boostorg/filesystem/pull/100
