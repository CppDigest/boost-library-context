# #9 - Change `unique_resource` example in the documentation [Closed]

> Username: Lastique  
> Created at: 2023-11-30 22:50:38 UTC  
> Updated at: 2023-12-02 22:11:07 UTC  
> Closed at: 2023-12-02 22:11:07 UTC  
> Url: https://github.com/boostorg/scope/issues/9  

From Julien Blanc:  
  
> Another issue raises when we look at the example code:  
  
```  
void write_string(std::string const& filename, std::string const& str)  
{  
    // Create a unique resource for a file  
    boost::scope::unique_resource< int, fd_deleter > file(open(filename.c_str(), O_CREAT | O_WRONLY));  
    if (file.get() < 0)  
    {  
        int err = errno;  
        throw std::system_error(err, std::generic_category(), "Failed to open file " + filename);  
    }  
  
    // Use it to write data to the file  
    const char* p = str.data();  
    std::size_t size_left = str.size();  
    while (size_left > 0)  
    {  
        ssize_t written = write(file.get(), p, size_left);  
        if (written < 0)  
        {  
            int err = errno;  
            if (err == EINTR)  
                continue;  
            throw std::system_error(err, std::generic_category(), "Failed to write data");  
        }  
  
        p += written;  
        size_left -= written;  
    }  
}  
```  
  
> In case of a failure, what we got is an half-baked file, which we know nothing about. A proper write_string function should at least try to do its cleanup and delete the file in case of error. But that defeats the purpose of unique_resource... In my experience, automatically closing file handles is not error handling. But it has its use cases, maybe it would be better demonstrated by an example like:  
  
```  
bool compare_files(std::string const& filename1, std::string const& filename2)  
{  
    boost::scope::unique_resource< int, fd_deleter > file1(open(filename.c_str(), O_CREAT | O_WRONLY));  
    if (file1.get() < 0)  
    {  
        int err = errno;  
        throw std::system_error(err, std::generic_category(), "Failed to open file " + filename1);  
    }  
    boost::scope::unique_resource< int, fd_deleter > file2(open(filename.c_str(), O_CREAT | O_WRONLY));  
    if (file2.get() < 0)  
    {  
        int err = errno;  
        throw std::system_error(err, std::generic_category(), "Failed to open file " + filename2);  
    }  
    // do the comparison  
}  
```
