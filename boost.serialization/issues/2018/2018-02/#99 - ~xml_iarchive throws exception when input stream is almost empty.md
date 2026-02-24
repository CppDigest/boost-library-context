# #99 - ~xml_iarchive throws exception when input stream is almost empty [Open]

> Username: Radrik5  
> Created at: 2018-02-27 12:05:35 UTC  
> Updated at: 2018-02-27 12:08:37 UTC  
> Url: https://github.com/boostorg/serialization/issues/99  

Call stack:  
```  
boost::archive::basic_xml_grammar<char>::my_parse  
boost::archive::basic_xml_grammar<char>::windup  
boost::archive::xml_iarchive_impl<boost::archive::xml_iarchive>::~xml_iarchive_impl<boost::archive::xml_iarchive>  
boost::archive::xml_iarchive::~xml_iarchive  
```  
  
When the destructor is called, my input stream contains "\r\n".  
  
std::basic_stream::get() sets eofbit and failbit, basic_xml_grammar<CharType>::my_parse() checks failbit before eofbit and throws the exception: https://github.com/boostorg/serialization/blob/develop/src/basic_xml_grammar.ipp#L193  
  
This behavior was introduced in boost 1.66 (commit https://github.com/boostorg/serialization/commit/64dc62099238a44341132d7cd4e237d1c1004020).  
  
Checking eofbit first fixed the problem for me. My application stopped crashing because of exception thrown from ~xml_iarchive_impl() which is implicitly declared as noexcept in VS2017.  
  
However I'm not sure what the correct fix should be. I don't think windup() should throw any exception as it's called from noexcept destructor.

---

## Comment 1

> Username: Radrik5  
> Created at: 2018-02-27 12:08:37 UTC  
> Url: https://github.com/boostorg/serialization/issues/99#issuecomment-368853223  

Sorry, it seems to be a duplicate of #82.
