# #257 - Memory read violation in Boost XML iarchive [Closed]

> Username: stefaanderoeck  
> Created at: 2022-05-19 15:52:00 UTC  
> Updated at: 2022-09-16 23:13:03 UTC  
> Closed at: 2022-09-16 23:13:02 UTC  
> Url: https://github.com/boostorg/serialization/issues/257  

https://github.com/boostorg/serialization/blob/b7ae64e95c74f6536f268e9ed2fe0f8d1fa8475e/include/boost/archive/impl/basic_xml_iarchive.ipp#L61  
  
Context:  
- An application using Boost XML Serialization.   
- The application was built with "-fsanitize=address" to detect memory access violations  
- Most likely, there was a file written to disk with a format incompatible with the format expected by the application.   
  
Observation:  
=================================================================  
==41255==ERROR: AddressSanitizer: global-buffer-overflow on address 0x0000002ec806 at pc 0x7f1bd7092405 bp 0x7f1bc9213690 sp 0x7f1bc9212e38  
READ of size 11 at 0x0000002ec806 thread T47 (DCM_IO:SaverThr)  
#0 0x7f1bd7092404 in MemcmpInterceptorCommon(void*, int (*)(void const*, void const*, unsigned long), void const*, void const*, unsigned long) (/lib64/libasan.so.6+0x94404)  
#1 0x7f1bd7092b16 in memcmp (/lib64/libasan.so.6+0x94b16)  
#2 0x7f1bd7bd0d94 in boost::archive::basic_xml_iarchive<boost::archive::xml_iarchive>::load_end(char const*) (/opt/vdcm/lib/boost-1.78.0/libboost_serialization.so.1.78.0+0x2cd94)  
#3 0x10184344 in boost::archive::detail::polymorphic_iarchive_route<boost::archive::xml_iarchive>::load_end(char const*) /opt/vdcm-external-sdks/boost-1.78.0/include/boost/archive/detail/polymorphic_iarchive_route.hpp:167  
#4 0x7236ed8 in void boost::archive::polymorphic_iarchive_impl::load_override<boost::serialization::collection_size_type>(boost::serialization::nvp<boost::serialization::collection_size_type> const&) /opt/vdcm-external-sdks/boost-1.78.0/include/boost/archive/polymorphic_iarchive.hpp:117  
--snip--  
  
Analysis:  
- Two variables are to be considered: 'name' (parameter) and 'object_name' (member)  
- These variables are being compared by the code referenced above. There are 3 possible cases: either their length is the same, or 'name' is longer than 'object_name', or 'name' is shorter than 'object_name'  
1) In case 'name' and 'object_name' are of equal length, we can expect the condition on line 61 to appropriately evaluate to false, and  the std::equal check to give the correct result  
2) In case 'name' is a longer C-string than 'object_name', we can expect the condition on line 61 to appropriately evaluate to true, thus skipping the second check and correctly yield 'true' as the full condition's result  
3) In case 'name' is a shorter C-string than 'object_name', the condition on line 61 tries to access memory that does not belong to 'name': the size of 'object_name' is an out-of-bounds index with respect to the shorter 'name'.   
   **IF** you assume the memory access does not trigger an error in the CPU, the result of that check is undefined, and the code may either continue to evaluate the second condition or not.  
   Evaluating the second condition can then again result in invalid memory access for some bytes beyond the `\0` at the end of 'name', depending on how std::equal is being implemented.   
  
Suggestion:  
- first thought: use `std::string_view` instead of `char*` to propagate name. But this change would propagate everywhere, and `string_view` is too new to use in Boost (requires C++17)  
- the serious suggestion: use `std::strncmp` instead (see [strncmp](https://en.cppreference.com/w/cpp/string/byte/strncmp)):  
```  
   if (std::strncmp(name, object_name.begin(), object_name.size()) { ... }  
```  
  that should stop reading after the first `\0` char in either C-string.

---

## Comment 1

> Username: robertramey  
> Created at: 2022-09-16 23:13:02 UTC  
> Url: https://github.com/boostorg/serialization/issues/257#issuecomment-1249928667  

I spent too much time looking into this.  A big issue (for me) is the usage of char in wide char archives.  I think this is a mistake but now I remember perhaps doing it this way for a reason.  This would let me make everything simpler.  But it would also change the type of the "class_name" implementation which might have repercussions for old archives - a nightmare for me.  
  
In any case the above is not relevant to the question at hand.  I agree that using string_view would have ripple effects perhaps similar to the above and unintended consequences for older archives.  On the other hand, using strncmp doesn't work for wide char archives.  These are not so common now - if ever - but I'm still interested in maintaining compatibility with older archives.  Also having archives parametrized on character type might come in handy in the future.   Sooo - I reformatted the code in the following way to make things easier to understand.  
  
`         
        // double check that the tag matches what is expected - useful for debug  
        std::size_t parameter_name_length = std::strlen(name);  
        std::size_t object_name_length = this->This()->gimpl->rv.object_name.size();  
  
        if(parameter_name_length != object_name_length  
        || ! std::equal(  
                this->This()->gimpl->rv.object_name.begin(),  
                this->This()->gimpl->rv.object_name.end(),  
                name  
            )  
        ){  
            boost::serialization::throw_exception(  
                xml_archive_exception(  
                    xml_archive_exception::xml_archive_tag_mismatch,  
                    name  
                )  
            );  
        }  
`
