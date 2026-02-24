# #152 - boost::filsystem::copy fails to copy recursively if error code parameter provided [Closed]

> Username: x-santiaga-x  
> Created at: 2020-07-07 10:19:11 UTC  
> Updated at: 2020-07-07 12:59:28 UTC  
> Closed at: 2020-07-07 12:59:16 UTC  
> Url: https://github.com/boostorg/filesystem/issues/152  

In test/copy_test.cpp test will fail in test_copy_dir_default if we change  
```  
    fs::copy(root_dir, target_dir);  
```  
to  
```  
    boost::system::error_code ec;  
    fs::copy(root_dir, target_dir, ec);  
```  
  
because of checking stat of non-existent destination directory before creating it.

---

## Comment 1

> Username: Lastique  
> Created at: 2020-07-07 12:59:28 UTC  
> Url: https://github.com/boostorg/filesystem/issues/152#issuecomment-654839654  

Thanks for the report.
