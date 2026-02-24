# #146 - Clang-tidy bugprone-exception-escape warnings [Closed]

> Username: EugeneZelenko  
> Created at: 2020-05-29 03:11:24 UTC  
> Updated at: 2020-05-29 07:50:27 UTC  
> Closed at: 2020-05-29 07:50:27 UTC  
> Url: https://github.com/boostorg/filesystem/issues/146  

Complete list of warnings for `develop` from Clang-tidy 10.0:  
  
<pre>  
./boost/filesystem/directory.hpp:273:3: warning: an exception may be thrown in function 'directory_iterator' which should not throw exceptions [bugprone-exception-escape]  
./boost/filesystem/directory.hpp:278:3: warning: an exception may be thrown in function 'directory_iterator' which should not throw exceptions [bugprone-exception-escape]  
./boost/filesystem/directory.hpp:299:23: warning: an exception may be thrown in function 'increment' which should not throw exceptions [bugprone-exception-escape]  
./boost/filesystem/directory.hpp:468:3: warning: an exception may be thrown in function 'recursive_directory_iterator' which should not throw exceptions [bugprone-exception-escape]  
./boost/filesystem/directory.hpp:490:33: warning: an exception may be thrown in function 'increment' which should not throw exceptions [bugprone-exception-escape]  
./boost/filesystem/directory.hpp:519:8: warning: an exception may be thrown in function 'pop' which should not throw exceptions [bugprone-exception-escape]  
./boost/filesystem/operations.hpp:303:6: warning: an exception may be thrown in function 'copy' which should not throw exceptions [bugprone-exception-escape]  
./boost/filesystem/operations.hpp:313:6: warning: an exception may be thrown in function 'copy' which should not throw exceptions [bugprone-exception-escape]  
./boost/filesystem/operations.hpp:367:6: warning: an exception may be thrown in function 'copy_symlink' which should not throw exceptions [bugprone-exception-escape]  
./boost/filesystem/operations.hpp:374:6: warning: an exception may be thrown in function 'create_directories' which should not throw exceptions [bugprone-exception-escape]  
./boost/filesystem/operations.hpp:441:6: warning: an exception may be thrown in function 'permissions' which should not throw exceptions [bugprone-exception-escape]  
</pre>

---

## Comment 1

> Username: Lastique  
> Created at: 2020-05-29 07:50:27 UTC  
> Url: https://github.com/boostorg/filesystem/issues/146#issuecomment-635819970  

These warnings are incorrect, I'm not going to fix them.
