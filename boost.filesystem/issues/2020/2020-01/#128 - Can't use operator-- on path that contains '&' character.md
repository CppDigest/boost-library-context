# #128 - Can't use operator<< on path that contains '&' character [Closed]

> Username: Mzartek  
> Created at: 2020-01-17 15:23:20 UTC  
> Updated at: 2020-01-17 17:13:03 UTC  
> Closed at: 2020-01-17 17:13:03 UTC  
> Url: https://github.com/boostorg/filesystem/issues/128  

Hello,  
  
I have a problem with the use of boost::filesystem::path on Windows when the path contains one '&' character.  
  
For example if my path is like:  
  
"C:\foo & bar\Hello & World.txt"  
  
Then the result of the operator<< of boost::filesystem::path will be:  
  
"C:\foo && bar\Hello && World.txt"  
  
Is this behavior normal ? Seems that the operator<< consider the character "&" as an escape one. But then how do we do when there is this character in the path ?  
  
Any idea ?  
  
Thanks !  
  
Kevin HAVRANEK

---

## Comment 1

> Username: Lastique  
> Created at: 2020-01-17 17:13:03 UTC  
> Url: https://github.com/boostorg/filesystem/issues/128#issuecomment-575715192  

This is normal. The `&` character is used for escaping on both output and input. If you want to have unmodified path string, you should use one of the `path` accessors, but then you will have to deal with escaping yourself, if you need IO roundtrip.
