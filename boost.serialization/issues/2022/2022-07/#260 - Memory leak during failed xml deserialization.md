# #260 - Memory leak during failed xml deserialization [Open]

> Username: hanzotutu  
> Created at: 2022-07-31 04:22:40 UTC  
> Updated at: 2022-09-19 23:51:32 UTC  
> Url: https://github.com/boostorg/serialization/issues/260  

I have a project using boost::serialization (version 1.74) to and from xmls. Recently, we removed a data member inside a serializable class. When we try to read the old xml (the removed field is still in xml), the deserialization failed as expected. However, when we run Valgrind through the failed deserialization, we found some memory leak. I searched the internet for solution, and tried to catch the exception and do 'delete_created_pointer()', etc. However, nothing worked. I really need your help. Following are some details.  
  
From xml, we read a vector of objects, and store them in shared_ptr of the common base class (serialization is exactly opposite):  
```C++  
     void SerializationFactory::register_in(boost::archive::xml_iarchive &ar,  
          std::vector<std::shared_ptr<BaseObject> >& objs) {  
          try {  
              // ...  
              ar & boost::serialization::make_nvp("My_ObjectDB", objs);  
              // ...  
          } catch (std::exception &) {  
              ar.delete_created_pointers();  
              objs.clear();  
              throw;  
          }  
      }  
```  
The Valgrind report is like below:  
````  
...  
==29114== 1,266 (1,080 direct, 186 indirect) bytes in 3 blocks are definitely lost in loss record 5,765 of 5,961  
==29114==    at 0x4C31556: operator new(unsigned long) (vg_replace_malloc.c:344)  
==29114==    by 0x1120AC1E: boost::archive::detail::basic_iarchive_impl::load_pointer(boost::archive::detail::basic_iarchive&, void*&, boost::archive::detail::basic_pointer_iserializer const*, boost::archive::detail::basic_pointer_iserializer const* (*)(boost::serialization::extended_type_info const&)) (in /home/appadmin/qrlib/alib/code/Release/Linux/lib/libALib.so)  
==29114==    by 0x105730BB: boost::archive::detail::iserializer<boost::archive::xml_iarchive, std::shared_ptr<BaseObject> >::load_object_data(boost::archive::detail::basic_iarchive&, void*, unsigned int) const (in /home/appadmin/qrlib/alib/code/Release/Linux/lib/libALib.so)  
==29114==    by 0x1120A4CA: boost::archive::detail::basic_iarchive::load_object(void*, boost::archive::detail::basic_iserializer const&) (in /home/appadmin/qrlib/alib/code/Release/Linux/lib/libALib.so)  
==29114==    by 0x10570CB0: boost::archive::detail::iserializer<boost::archive::xml_iarchive, std::vector<std::shared_ptr<BaseObject>, std::allocator<std::shared_ptr<BaseObject> > > >::load_object_data(boost::archive::detail::basic_iarchive&, void*, unsigned int) const (in /home/appadmin/qrlib/alib/code/Release/Linux/lib/libALib.so)  
==29114==    by 0x1120A4CA: boost::archive::detail::basic_iarchive::load_object(void*, boost::archive::detail::basic_iserializer const&) (in /home/appadmin/qrlib/alib/code/Release/Linux/lib/libALib.so)  
==29114==    by 0x1056D13E: Addin::SerializationFactory::register_in(boost::archive::xml_iarchive&, std::vector<std::shared_ptr<BaseObject>, std::allocator<std::shared_ptr<BaseObject> > >&) (in /home/appadmin/qrlib/alib/code/Release/Linux/lib/libALib.so)  
...  
````  
  
Thanks in advance.

---

## Comment 1

> Username: robertramey  
> Created at: 2022-07-31 19:53:26 UTC  
> Url: https://github.com/boostorg/serialization/issues/260#issuecomment-1200488746  

I've taken a brief look at this in case it was something simple.  It's not.  At least I couldn't figure it out in the short time I looked at it.  I can offer some hints though.  
  
1. Test serialization of each derived object.  This should be super simple to do. call this test_ap_1 or test_ap_<derived_object_name>.  
2. Test serialization of raw pointer to each derived object.  try loading derived pointer object.  true loading base class pointer.  This should work  
3. Test serialization of a vector of raw pointers to base_class_object.  Be sure that the save function saves the pointer the derived object and loads point to the base object.  Then check that the runtime type of each loaded object is the same as the derived object which was saved.  
4. Test serialization of smart_ptr to each derived object.  
5. Test serialization of a vector of smart_ptr to base_class_object.   
  
Sorry this isn't the answer you're looking for.  But I don't know what else to suggest.  Look the bright side.  You keep the test_apps (under different names of course).  So that every time a changes is made anywhere or different environment (compiler, etc) is selected (and only when that happens) , you'll automatically detect any problems when they occur.  In other words, the above is a one time investment, but it pays dividends for ever.  
  
Robert Ramey

---

## Comment 2

> Username: hanzotutu  
> Created at: 2022-08-04 13:11:58 UTC  
> Url: https://github.com/boostorg/serialization/issues/260#issuecomment-1205237802  

I tried the following cases:  
![image](https://user-images.githubusercontent.com/96936680/182854136-9a37a650-048c-4765-9be4-73fb87608923.png)  
To summarize, as long as pointers are involved, I get memory leak in failed xml read. Archive.delete_created_pointers() only works for derivedObj*. Delete pointers works for raw pointers but not shared_ptr (use_count == 0). So I guess delete_created_pointers doesn't work as designed. Please let me know if you need more information.

---

## Comment 3

> Username: robertramey  
> Created at: 2022-08-04 14:43:45 UTC  
> Url: https://github.com/boostorg/serialization/issues/260#issuecomment-1205356216  

Thanks for doing this.  "Archive.delete_created_pointers() only works for derivedObj*" looks to me that what we really want to say is: Archive.delete_created_pointers() only FAILS for derivedObj* ?  
  
FYI - the fact that this is an xml_archive shouldn't have anything to with this.   BUT it could and it would be interesting to know if did.  It might be interesting to run this with a text archive rather than an xml one (which inserts another layer of objects into the tree).  
  
I'm also curious what the phrase "Leak after ..." means.  The leak is only produced/detected when the interrupt is caught right? I'm guessing the perhaps this means that the place where the leaked object was created as shown in the backtrace?

---

## Comment 4

> Username: hanzotutu  
> Created at: 2022-08-04 15:11:37 UTC  
> Url: https://github.com/boostorg/serialization/issues/260#issuecomment-1205391309  

> Thanks for doing this. "Archive.delete_created_pointers() only works for derivedObj*" looks to me that what we really want to say is: Archive.delete_created_pointers() only FAILS for derivedObj* ?  
>   
Well, for cases involving shared pointers, the allocated pointers are not passed to shared_ptr(use_count == 0). So I assume delete_created_pointers should free them, no?  
  
> FYI - the fact that this is an xml_archive shouldn't have anything to with this. BUT it could and it would be interesting to know if did. It might be interesting to run this with a text archive rather than an xml one (which inserts another layer of objects into the tree).  
>   
I can try text archive.  
  
> I'm also curious what the phrase "Leak after ..." means. The leak is only produced/detected when the interrupt is caught right? I'm guessing the perhaps this means that the place where the leaked object was created as shown in the backtrace?  
  
Yes. the sample code is below.  
  
```C++  
      try {  
            ar & boost::serialization::make_nvp("anObject", base_sp); // derived_sp, base_ptr, derived_ptr  
      } catch(...) {  
            // std::cout << derived_sp.use_count();  
            // std::cout << base_sp.use_count();  
            // ar.delete_created_pointers();  
            // delete derived_ptr;  
            // delete base_ptr;  
            throw;  
      }  
```

---

## Comment 5

> Username: hanzotutu  
> Created at: 2022-08-05 15:48:24 UTC  
> Url: https://github.com/boostorg/serialization/issues/260#issuecomment-1206601681  

Text archives have the same memory leak behavior. I think the walkaround is to use raw pointers. Make share if everything goes fine. Otherwise, we have to manually delete the pointers.

---

## Comment 6

> Username: robertramey  
> Created at: 2022-09-15 21:43:02 UTC  
> Url: https://github.com/boostorg/serialization/issues/260#issuecomment-1248663081  

taking a little more time with this.  
  
If you're serializing a vector of base class pointers it looks like things always work.  
Since you're using a (virtual) classs this seems correct.  
  
If you're using an vector of base class pointers (shared or not), but serializing a derived pointers, I would not be surprised that there might be problems perhaps related to slicing somewhere.   
  
Still, using shared_ptr<Base*> and Base* should work the same.  So I would next investigate a little deeper shared_ptr<Base *>.  
  
Note: it looks like you're compiling in release mode and thus linking the release level libraries which don't have debug code, line #, etc.  It might be a good idea to see what happens while compiling/building/testing in debug mode.

---

## Comment 7

> Username: hanzotutu  
> Created at: 2022-09-19 23:51:32 UTC  
> Url: https://github.com/boostorg/serialization/issues/260#issuecomment-1251686226  

I used a simpler case to reproduce the leak. More information is provided by the debug version.  
```  
==24933== 247 (216 direct, 31 indirect) bytes in 1 blocks are definitely lost in loss record 7,174 of 9,659  
==24933==    at 0x4C2B7DB: operator new(unsigned long) (vg_replace_malloc.c:422)  
==24933==    by 0x189EC3BE: boost::archive::detail::basic_iarchive_impl::load_pointer(boost::archive::detail::basic_iarchive&, void*&, boost::archive::detail::basic_pointer_iserializer const*, boost::archive::detail::basic_pointer_iserializer const* (*)(boost::serialization::extended_type_info const&)) (basic_iarchive.cpp:484)  
==24933==    by 0x189EB015: boost::archive::detail::basic_iarchive::load_pointer(void*&, boost::archive::detail::basic_pointer_iserializer const*, boost::archive::detail::basic_pointer_iserializer const* (*)(boost::serialization::extended_type_info const&)) (basic_iarchive.cpp:575)  
==24933==    by 0x17BFFA0B: boost::archive::detail::iserializer<boost::archive::xml_iarchive, std::shared_ptr<BaseObject> >::load_object_data(boost::archive::detail::basic_iarchive&, void*, unsigned int) const (in /home/appadmin/.local/lib/python3.6/site-packages/pyAddins/alib.cpython-36m-x86_64-linux-gnu.so)  
==24933==    by 0x189EBF01: boost::archive::detail::basic_iarchive_impl::load_object(boost::archive::detail::basic_iarchive&, void*, boost::archive::detail::basic_iserializer const&) (basic_iarchive.cpp:411)  
==24933==    by 0x189EAFCC: boost::archive::detail::basic_iarchive::load_object(void*, boost::archive::detail::basic_iserializer const&) (basic_iarchive.cpp:562)  
==24933==    by 0x17BFCFF0: boost::archive::detail::iserializer<boost::archive::xml_iarchive, std::vector<std::shared_ptr<BaseObject>, std::allocator<std::shared_ptr<BaseObject> > > >::load_object_data(boost::archive::detail::basic_iarchive&, void*, unsigned int) const (in /home/appadmin/.local/lib/python3.6/site-packages/pyAddins/alib.cpython-36m-x86_64-linux-gnu.so)  
==24933==    by 0x189EBF01: boost::archive::detail::basic_iarchive_impl::load_object(boost::archive::detail::basic_iarchive&, void*, boost::archive::detail::basic_iserializer const&) (basic_iarchive.cpp:411)  
==24933==    by 0x189EAFCC: boost::archive::detail::basic_iarchive::load_object(void*, boost::archive::detail::basic_iserializer const&) (basic_iarchive.cpp:562)  
==24933==    by 0x17BF8C08: Addin::SerializationFactory::register_in(boost::archive::xml_iarchive&, std::vector<std::shared_ptr<BaseObject>, std::allocator<std::shared_ptr<BaseObject> > >&) (in /home/appadmin/.local/lib/python3.6/site-packages/pyAddins/alib.cpython-36m-x86_64-linux-gnu.so)  
```
