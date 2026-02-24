# #237 - directory_iterator_construct fails on FAT32 formatted flash drive under Windows [Closed]

> Username: chenche23  
> Created at: 2022-05-13 12:33:36 UTC  
> Updated at: 2022-05-24 12:01:44 UTC  
> Closed at: 2022-05-15 15:42:42 UTC  
> Url: https://github.com/boostorg/filesystem/issues/237  

I believe the root cause and workaround is similar to Issue #236 .  
  
I am using v1.79 compiled with msvc-14.1.  
I try to create a directory Iterator on the root directory of a flash drive (say E:/)  
```  
boost::system::error_code error;  
boost::filesystem::directory_iterator it("E:/", error);  
```  
  
That fails with error code 87 (ERROR_INVALID_PARAMETER)  
  
dir_itr_create() uses get_file_information_by_handle_ex() in directory.cpp(775). When I jump to the else-branch using GetFileInformationByHandle(), the Iterator is created successfully.

---

## Comment 1

> Username: truemanc  
> Created at: 2022-05-15 10:53:01 UTC  
> Url: https://github.com/boostorg/filesystem/issues/237#issuecomment-1126907814  

I'm using exFat formatted disk under windows.  
  
Hit the same issue via remove_all() which depends on dir_itr_create() . dir_itr_create() uses the same approach as #236.

---

## Comment 2

> Username: Lastique  
> Created at: 2022-05-15 15:44:24 UTC  
> Url: https://github.com/boostorg/filesystem/issues/237#issuecomment-1126967066  

Thanks for the report.

---

## Comment 3

> Username: truemanc  
> Created at: 2022-05-24 10:04:29 UTC  
> Url: https://github.com/boostorg/filesystem/issues/237#issuecomment-1135701916  

Using the latest code, I confirm remove_all() passes on my exFat formatted drive under Windows.

---

## Comment 4

> Username: Lastique  
> Created at: 2022-05-24 12:01:44 UTC  
> Url: https://github.com/boostorg/filesystem/issues/237#issuecomment-1135828002  

Thank you for testing.
