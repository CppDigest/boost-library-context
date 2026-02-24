# #385 Fixed a crash when reading PNG files with an invalid header. [Merged]

> Username: macmade  
> Created at: 2019-08-26 15:28:51 UTC  
> Updated at: 2019-08-26 17:42:15 UTC  
> Merged at: 2019-08-26 17:41:58 UTC  
> Closed at: 2019-08-26 17:41:59 UTC  
> Url: https://github.com/boostorg/gil/pull/385  

### Description  
  
**Use of `BOOST_ASSERT` in `png_ptr_read_deleter` and `png_ptr_write_deleter` in ‎⁨`include/boost/gil/extension/⁨io/png/⁨detail⁩/reader_backend.hpp` will produce a crash with PNG files with an invalid header.**  
  
In such a case (bad PNG file), we expect a `std::ios_base::failure` exception (see below).    
Unfortunately, the assertions will prevent the exception to be caught by terminating the program.  
  
This hotfix replaces the assertions with conditional checks, so we can get the expected exception handling.  
  
#### Steps to reproduce  
  
  1. Create a text file with at least 4 characters (like `ABCD`).  
  2. Try to read the file as PNG:  
  
    using backend_t   = typename boost::gil::get_reader_backend< std::string, boost::gil::png_tag >::type;  
    backend_t backend = boost::gil::read_image_info( "path/to/invalid/png", boost::gil::png_tag() );  
  
#### Explanation  
  
A `reader_backend` object is created: `‎⁨include/boost/gil/extension/⁨io/png/⁨detail⁩/reader_backend.hpp`.  
  
The base class' constructor (`png_struct_info_wrapper`) has a `std::shared_ptr` member for the `png_ptr_wrapper`.    
The shared pointer is created with a custom deleter (`png_ptr_read_deleter` and `png_ptr_write_deleter`).  
  
So inside the backend constructor, the `png_ptr_wrapper` is non-null.  
Then, still from the constructor, the PNG header is read by calling `read_header`.  
  
        io_error_if( png_sig_cmp( png_bytep(buf)  
                                , png_size_t(0)  
                                , PNG_BYTES_TO_CHECK  
                                ) != 0  
                   , "png_check_validity: invalid png image"  
                   );  
  
So for an invalid header, `io_error_if` will throw an exception.    
This is the expected behavior.  
  
But as the exception is thrown, the backend object gets deallocated.    
So is the `std::shared_ptr`. And as the deleter is called:  
  
    static void png_ptr_read_deleter( png_ptr_wrapper* png_ptr )  
    {  
        if( png_ptr )  
        {  
            BOOST_ASSERT(png_ptr->_struct && png_ptr->_info);  
  
`png_ptr` is valid, but all fields will be `null`, as we haven't read the PNG file yet.    
Assertion will fail, making the program crash, and thus preventing the expected exception to be caught.  
  
### References  
  
### Tasklist  
  
- [x] Ensure all CI builds pass  
- [x] Review and approve

---

## Comment 1

> Username: mloskot  
> Created_at: 2019-08-26 17:40:24 UTC  
> Updated_at: 2019-08-26 17:41:32 UTC  
> Url: https://github.com/boostorg/gil/pull/385#issuecomment-524955336  

@macmade   
> Assertion will fail, making the program crash  
  
It should have also stated "only if compiled with `-DNDEBUG`", no?  
  
Thanks for the report

---

## Review 2 [Approved]

> Username: mloskot  
> Created_at: 2019-08-26 17:41:21 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/gil/pull/385#pullrequestreview-279731831  

LGTM. Thanks for the fix.

---
