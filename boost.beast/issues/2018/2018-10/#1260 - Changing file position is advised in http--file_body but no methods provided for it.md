# #1260 - Changing file position is advised in http::file_body but no methods provided for it [Closed]

> Username: krasnorutsky  
> Created at: 2018-10-01 10:01:28 UTC  
> Updated at: 2020-01-23 15:56:40 UTC  
> Closed at: 2020-01-23 15:56:40 UTC  
> Url: https://github.com/boostorg/beast/issues/1260  

http::file_body has a good and convenient way for downloading large files by opening them with file_mode = append_existing. In the documentation (file_base.hpp) It is also advised to change file position:  
"The current file position shall be set to the end of  
        the file prior to each write."  
But I can't see any methods for accomplish this using basic_file_body<File>::value_type class. Moreover, I see that "File file_" is private (not protected) - so I cannot add this functionality by overriding the class. I have to completely rewrite the class to accomplish the desired behavior.  
  
Please add the necessary method (or an additional file_mode) for conveniently downloading large files.

---

## Comment 1

> Username: kaptenhonek  
> Created at: 2019-04-24 09:31:54 UTC  
> Url: https://github.com/boostorg/beast/issues/1260#issuecomment-486144728  

I stumbled upon this as well, I solved it by manually creating a file object. Something like this:  
  
```c++  
boost::beast::http::response_parser<boost::beast::http::file_body> parser;  
boost::system::error_code ec;  
boost::beast::file file;  
file.open("/path/to/file", boost::beast::file_mode::append_existing, ec);  
  
//Seek to the end of the file  
auto size(file.size(ec));  
if (size > 0) {  
    file.seek(size, ec);  
}  
  
//Put the file into the body  
parser.get().body().reset(std::move(file), ec);  
  
//...continue with read/async_read  
```

---

## Comment 2

> Username: nickolasrossi  
> Created at: 2019-07-16 16:35:00 UTC  
> Url: https://github.com/boostorg/beast/issues/1260#issuecomment-511892138  

For now, is there any harm in adding an accessor for the File to basic_file_body?  
  
`File& basic_file_body<File>::value_type::file() { return file_; }`  
  
I came upon a need for this as well, trying to customize the File type and needing the reference to it without resorting to passing along a shared_ptr to internal state.  
  
If it's safe, I can pull-request this change, it's simple.

---

## Comment 3

> Username: vinniefalco  
> Created at: 2019-07-16 16:41:28 UTC  
> Url: https://github.com/boostorg/beast/issues/1260#issuecomment-511894463  

Yes I think we can add that safely

---

## Comment 4

> Username: mika-fischer  
> Created at: 2020-01-21 08:39:53 UTC  
> Url: https://github.com/boostorg/beast/issues/1260#issuecomment-576575519  

This would also help with #1810. Any progress on this?
