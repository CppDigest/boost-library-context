# #78 - One test fails to compile due to errors C2976 C3203 C2516 C2039 C3646 C4430 C2873 C2131 with latset reversion on boost master branch [Closed]

> Username: nnfdnkns  
> Created at: 2022-09-27 02:41:13 UTC  
> Updated at: 2022-11-11 05:54:04 UTC  
> Closed at: 2022-11-11 05:54:04 UTC  
> Url: https://github.com/boostorg/intrusive/issues/78  

**Environment:**  
VS2019+Windows Server 2019  
  
**Issue description:**  
Found \boost\libs\intrusive build errors C2976 C3203 C2516 C2039 C3646 C4430 C2873 C2131 in MSVC, this issue can be reproduced on [boostorg/boost@b5639bd](https://github.com/boostorg/boost/commit/b5639bd38cb199fec61d99d490f51486428444ac). Could you please take a look?  
  
**Reproduce steps:**  
1.git clone -c core.autocrlf=true --recursive ​https://github.com/boostorg/boost.git F:\gitP\boostorg\boost  
2.open a VS 2019 x64 command prompt and browse to F:\gitP\boostorg\boost  
3..\bootstrap  
4..\b2 headers variant=release --build-dir=..\out\amd64rel address-model=64  
5..\b2 variant=release --build-dir=..\out\amd64rel address-model=64  
6..\b2 -j16 variant=release --build-dir=F:\gitP\boostorg\boost\out\amd64rel libs\intrusive\test address-model=64  
  
**ErrorMessage:**  
.\boost/intrusive/hashtable.hpp(1599): error C2976: 'boost::intrusive::bucket_hash_equal_t': too few template arguments  
 .\boost/intrusive/hashtable.hpp(1793): error C3203: 'bucket_hash_equal_t': unspecialized class template can't be used as a template argument for template parameter 'DeriveFrom', expected a real type  
 .\boost/intrusive/hashtable.hpp(1760): error C2516: 'DeriveFrom': is not a legal base class  
 .\boost/intrusive/hashtable.hpp(1820): error C2039: 'key_equal': is not a member of 'boost::intrusive::hashtable_size_wrapper<int,SizeType,false>'  
.\boost/intrusive/hashtable.hpp(1820): error C3646: 'key_equal': unknown override specifier  
.\boost/intrusive/hashtable.hpp(1820): error C4430: missing type specifier - int assumed. Note: C++ does not support default-int  
.\boost/intrusive/hashtable.hpp(1885): error C2873: 'priv_clear_buckets': symbol cannot be used in a using-declaration  
'boost::intrusive::hashtable_size_wrapper<boost::intrusive::hashdata_internal<ValueTraits,VoidOrKeyOfValue,VoidOrKeyHash,VoidOrKeyEqual,BucketTraits,SizeType,1>,SizeType,true>'  
 .\boost/intrusive/hashtable.hpp(2214): error C3646: 'const_siterator': unknown override specifier  
 .\boost/intrusive/hashtable.hpp(2214): error C4430: missing type specifier - int assumed. Note: C++ does not support default-int  
 .\boost/intrusive/hashtable.hpp(2276): error C2039: 'bucket_overhead': is not a member of 'boost::intrusive::hashtable_size_wrapper<boost::intrusive::hashdata_internal<ValueTraits,VoidOrKeyOfValue,VoidOrKeyHash,VoidOrKeyEqual,BucketTraits,SizeType,1>,SizeType,true>'  
 .\boost/intrusive/hashtable.hpp(2276): error C2065: 'bucket_overhead': undeclared identifier  
 .\boost/intrusive/hashtable.hpp(2276): error C2131: expression did not evaluate to a constant  
 .\boost/intrusive/hashtable.hpp(2214): error C2039: 'const_siterator': is not a member of   
 libs\intrusive\test\custom_bucket_traits_test.cpp(125): error C2660: 'boost::intrusive::hashdata_internal<ValueTraits,VoidOrKeyOfValue,VoidOrKeyHash,VoidOrKeyEqual,BucketTraits,SizeType,5>::cend': function does not take 0 arguments  
  
**log:**  
[test.log](https://github.com/boostorg/intrusive/files/9651004/test.log)  
[test.log.59.txt](https://github.com/boostorg/intrusive/files/9651027/test.log.59.txt)

---

## Comment 1

> Username: nnfdnkns  
> Created at: 2022-11-03 09:06:00 UTC  
> Url: https://github.com/boostorg/intrusive/issues/78#issuecomment-1301812801  

Could you please take a look? thanks!
