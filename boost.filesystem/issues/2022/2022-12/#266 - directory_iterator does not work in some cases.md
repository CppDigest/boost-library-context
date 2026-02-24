# #266 - directory_iterator does not work in some cases [Closed]

> Username: iskiselev  
> Created at: 2022-12-01 04:45:16 UTC  
> Updated at: 2022-12-02 13:23:55 UTC  
> Closed at: 2022-12-02 13:23:55 UTC  
> Url: https://github.com/boostorg/filesystem/issues/266  

directory_iterator does not work in Azure App Service environment when created over any folder in C:\home.  
That environment is special with home folder mounted there in some way.  
  
It fails with error 87 in:  
file_id_extd_dir_info_format  
file_full_dir_info_format  
file_id_both_dir_info_format  
  
I debuged it, and looks like it works in default case (file_directory_information_format), but it never fallback to file_directory_information_format, so logic in default case is never executed.  
  
In the same environment current code works if iterator created over some other folders.  
  
Problem reproducible on at least boost 1.79 and 1.80. Potentially same issue with #261 and #255.
