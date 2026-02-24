# #482 - Doc images [Open]

> Username: sdarwin  
> Created at: 2025-04-24 16:47:01 UTC  
> Updated at: 2025-04-24 16:47:01 UTC  
> Url: https://github.com/boostorg/process/issues/482  

The latest docs from 1.88.0 reference a few images which exist in the file system at `../images` however the html pages fails to discover them since it's using 1 dot instead of 2.  
  
`<img src="./images/posix_exec_err.svg" alt="posix exec err">`  
  
Possible relocate the images during the build process so ./images works.   Or use `../images/`.
