# #312 - If a folder doesn't have read/write access, if (!(permission & fs::perms::owner_read) == fs::perms::no_perms) will always return success. [Closed]

> Username: Rahool24  
> Created at: 2024-06-06 11:28:04 UTC  
> Updated at: 2024-06-06 14:33:06 UTC  
> Closed at: 2024-06-06 14:32:37 UTC  
> Url: https://github.com/boostorg/filesystem/issues/312  

I have used the boost-1.79.0 version.  
  
**Case 1:**  
If the folder doesn't have read or write permission, the boost fs::status(filename, error). permissions() returns success.  
then I have checked.  
  
`if (!(permission & fs::perms::owner_read) == fs::perms::no_perms)` this condition ,it returns true. but actually that folder doesn't have permissions.  
  
  
```  
boost::system::error_code error;  
fs::perms permission = fs::status(filename, error).permissions();  
if (error.value() == boost::system::errc::success)  
{  
	if (!(permission & fs::perms::owner_read) == fs::perms::no_perms) // if folder have not permissions this condition should failed for folder , but this condition satisfied , then execute next line  
	{  
		if (file.is_open())  // here try to open the folder using (file.is_open()) function .It return false because the folder doesn't have permissions.  
		{  
			buffer.Resize(GetFileSize(filename, status));  
			if(status.GetStatus())  
				file.read(&buffer[0], buffer.Size());  
		}  
		else  
			cout<<"Can not Open File/Folder" <<endl;  
	}  
	else  
		cout<<"Permission Denied For Read" <<endl;  
}  
```  
  
  
For example:  
  
  
F:\home\test this folder doesn't have write permission. should not write a file in F:\home\test folder.   
  
  
  
**Case 2:**  
  
1. Check the read access to the folder using `"if (!(permission & fs::perms::owner_read) == fs::perms::no_perms)";` here this condition gets true even though the folder doesn't have read access.  
ex:  
F:\home\test  
  
2. In this same case, I have checked for the file permission ("F:/home/test/check.json") in the same folder. "`If (!(permission & fs::perms::owner_read) == fs::perms::no_perms),`" this condition works (it returns false).  
  
3. The same issue is happening for the write permission check for the folder.  
  
  
how to validate folder permission?  
  
Thanks!

---

## Comment 1

> Username: Lastique  
> Created at: 2024-06-06 14:32:37 UTC  
> Updated at: 2024-06-06 14:33:06 UTC  
> Url: https://github.com/boostorg/filesystem/issues/312#issuecomment-2152693963  

1. `if (!(permission & fs::perms::owner_read) == fs::perms::no_perms)` is likely incorrect. `operator!` has higher [precedence](https://en.cppreference.com/w/cpp/language/operator_precedence) than `operator==`, so the condition reads as `if ( (!(permission & fs::perms::owner_read)) == fs::perms::no_perms )`, i.e. the result of `permission & fs::perms::owner_read` is converted to `bool`, then an inverse of it is converted to `fs::perms` and compared to `fs::perms::no_perms`. This makes no sense at all. I think, you wanted to say `if ((permission & fs::perms::owner_read) != fs::perms::no_perms)` instead.  
2. On Windows, read permission is always indicated as present because Windows doesn't really support POSIX permissions. Basically, if a file is marked with the "read-only" attribute, Boost.Filesystem will indicate the file as readable. If it is not marked, it will indicate it as both readable and writable.  
3. Boost.Filesystem does not support or interpret ACLs, if that's how you define the file as "readable". There are no plans to support ACLs, and such support would not translate to POSIX-style permissions anyway.
