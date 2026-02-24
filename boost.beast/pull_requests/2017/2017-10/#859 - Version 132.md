# #859 Version 132 [Merged]

> Username: vinniefalco  
> Created at: 2017-10-30 16:32:43 UTC  
> Updated at: 2017-11-02 21:59:42 UTC  
> Merged at: 2017-10-31 20:37:07 UTC  
> Closed at: 2017-10-31 20:37:07 UTC  
> Url: https://github.com/boostorg/beast/pull/859  

* Tidy up project folders in CMakeLists.txt  
* Rename Cmake variables for clarity  
* Add ref-qualified overloads for message::body  
* Tidy up FieldsReader doc  
  
API Changes:  
  
* Fields::writer replaces Fields::reader  
* BodyReader and BodyWriter names are swapped  
  
Actions Required:  
  
* Rename reader to writer for user defined Fields  
* Swap the reader and writer names for user defined Body types  
* Swap use of is_body_reader and is_body_writer

---
