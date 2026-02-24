# #82 - boost::filesystem::canonical returns incorrect paths on older linux versions. [Closed]

> Username: hirden  
> Created at: 2018-08-01 11:15:07 UTC  
> Updated at: 2018-12-09 09:44:58 UTC  
> Closed at: 2018-12-09 09:44:58 UTC  
> Url: https://github.com/boostorg/filesystem/issues/82  

in operations.cpp in function `path read_symlink(const path& p, system::error_code* ec)` the GNU coreutils function `readlink` is used to resolve the symlink, however the function does not set a null termination byte in the buffer after the call is made.  
  
The documentation for the gnu coreutils function `readlink` states that it does not set a null termination byte. When the buffer is then used to assign a new `path` it, in some cases, contain control characters that a call to the readlink cmdline tool does not return.  
  
This is mainly an issue on older linux versions (I have tested on Centos 5.11) in conjunction with the prelink tool.  
  
Code used to reproduce:  
```c++  
boost::system::error_code error_code;  
boost::filesystem::path path("/proc");  
if (boost::filesystem::is_directory(path, error_code) && !error_code)  
{  
    boost::filesystem::directory_iterator it(path, error_code);  
    if (!error_code)  
    {  
        for (;it != boost::filesystem::directory_iterator(); ++it)  
        {  
            bool is_dir = boost::filesystem::is_directory(it->path(), error_code);  
            if (is_dir && !error_code)  
            {  
                boost::filesystem::path exe_path = it->path() / "exe";  
                auto is_link = boost::filesystem::is_symlink(exe_path, error_code);  
                if (!error_code && is_link)  
                {  
                    auto resolved = boost::filesystem::canonical(exe_path, error_code);  
                    if (!error_code)  
                    {  
                        std::cout << "resolved: " << resolved << '\n';  
                    }  
                }  
            }  
        }  
    }  
}  
```  
  
Sample output:  
```  
resolved: /usr/libexec/gnome-vfs-daemon�������֌ (deleted)  
resolved: /usr/bin/bt-applet���5[j+��� (deleted)  
resolved: /usr/bin/python.#prelink# (deleted)  
resolved: /usr/bin/nm-applet_�5[j+P� (deleted)  
resolved: /usr/bin/pam-panel-icon!`˻ (deleted)  
resolved: /sbin/pam_timestamp_checkǽ֌+ (deleted)  
resolved: /usr/bin/gnome-power-manager.#prelink#.p2mBLg (deleted)  
resolved: /usr/lib64/esc-1.1.0/escd  
                                   5  
                                    5  
                                     5  
                                      5  
                                       5 (deleted)  
resolved: /usr/sbin/nm-system-settingsV�O�+f�O�+ (deleted)  
resolved: /usr/libexec/gam_server (deleted)  
resolved: /usr/libexec/wnck-applet�1 (deleted)  
resolved: /usr/libexec/trashapplet�h� (deleted)  
  
```  
  
The readlink cmdline tool implementation and usage of the `readlink` function differs slightly from boost, but it explicitly sets the null terminating byte. And if the resulting path takes the null terminating byte into consideration when doing the `assign` call, the result does not contain the control characters.  
  
Sample solution:  
```c++  
BOOST_FILESYSTEM_DECL  
path read_symlink(const path& p, system::error_code* ec)  
{  
  path symlink_path;  
  
# ifdef BOOST_POSIX_API  
  
  for (std::size_t path_max = 64;; path_max *= 2)// loop 'til buffer large enough  
  {  
    boost::scoped_array<char> buf(new char[path_max]);  
    ssize_t result;  
    if ((result=::readlink(p.c_str(), buf.get(), path_max))== -1)  
    {  
      if (ec == 0)  
        BOOST_FILESYSTEM_THROW(filesystem_error("boost::filesystem::read_symlink",  
          p, error_code(errno, system_category())));  
      else ec->assign(errno, system_category());  
      break;  
    }  
    else  
    {  
      if(result != static_cast<ssize_t>(path_max))  
      {  
        // Set null terminating byte.  
        buf[result] = 0;  
        // Create a string from buffer  
        std::string symlink{buf.get()};  
        // Assign path from string.  
        symlink_path.assign(symlink.begin(), symlink.end());  
        if (ec != 0) ec->clear();  
        break;  
      }  
    }  
  }  
  //...  
#endif  
}  
```  
Not sure if the solution is the proper one for boost, but the idea is there at least.

---

## Comment 1

> Username: Lastique  
> Created at: 2018-12-09 09:44:58 UTC  
> Url: https://github.com/boostorg/filesystem/issues/82#issuecomment-445523630  

`readlink` returns the number of bytes written in the buffer in case of success. The current code makes use of that value, so it doesn't need the terminating zero. I'm not sure why you're having the problem, perhaps the bug was fixed since it was reported.
