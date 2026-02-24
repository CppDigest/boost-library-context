# #138 - filesystem::read_symlink and weakly_canonical fails in parallel access on Windows likely due to wrong access flag [Closed]

> Username: GitEvgen  
> Created at: 2020-04-23 19:58:13 UTC  
> Updated at: 2020-05-03 17:31:05 UTC  
> Closed at: 2020-05-03 17:31:05 UTC  
> Url: https://github.com/boostorg/filesystem/issues/138  

This is described well in [this stack overflow article](https://stackoverflow.com/questions/44393241/boostfilesystemrelative-cannot-access-the-file-because-it-is-being-used-by#) and has examples and a sample fix.  
Two processes accessing a file via link (read-only) get a file sharing violation, because read-only is not passed to the underlying file access call (CreateFileW parameter)
