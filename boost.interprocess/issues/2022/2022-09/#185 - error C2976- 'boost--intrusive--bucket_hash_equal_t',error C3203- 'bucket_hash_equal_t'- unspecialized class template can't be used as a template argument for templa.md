# #185 - error C2976: 'boost::intrusive::bucket_hash_equal_t',error C3203: 'bucket_hash_equal_t': unspecialized class template can't be used as a template argument for template parameter 'DeriveFrom', expected a real type on MSVC [Closed]

> Username: nnfdnkns  
> Created at: 2022-09-26 10:05:18 UTC  
> Updated at: 2022-11-11 05:54:26 UTC  
> Closed at: 2022-11-11 05:54:26 UTC  
> Url: https://github.com/boostorg/interprocess/issues/185  

**Issue description:**  
Found Boost\libs\interprocess build error C2976 ,error C3203 in MSVC, this issue can be reproduced on [boostorg/boost@b5639bd](https://github.com/boostorg/boost/commit/b5639bd38cb199fec61d99d490f51486428444ac). Could you please take a look?  
  
**Reproduce steps:**  
1.git clone -c core.autocrlf=true --recursive ​https://github.com/boostorg/boost.git F:\gitP\boostorg\boost  
2.open a VS 2019 x64 command prompt and browse to F:\gitP\boostorg\boost  
3..\bootstrap  
4..\b2 headers variant=release --build-dir=..\out\amd64rel address-model=64   
5..\b2 variant=release --build-dir=..\out\amd64rel address-model=64  
6..\b2 -j16 variant=release --build-dir=..\out\amd64rel libs\interprocess\test address-model=64   
  
**ErrorMessage:**  
error C2976: 'boost::intrusive::bucket_hash_equal_t': too few template arguments  
error C3203: 'bucket_hash_equal_t': unspecialized class template can't be used as a template argument for template parameter 'DeriveFrom', expected a real type  
...failed compile-c-c++ F:\gitP\boostorg\boost\out\amd64rel\boost\bin.v2\libs\interprocess\test\iunordered_set_index_allocation_test.test\msvc-14.2\release\threading-multi\iunordered_set_index_allocation_test.obj...  
  
**log**  
[test.log.58.txt](https://github.com/boostorg/interprocess/files/9943703/test.log.58.txt)

---

## Comment 1

> Username: nnfdnkns  
> Created at: 2022-11-05 11:57:04 UTC  
> Url: https://github.com/boostorg/interprocess/issues/185#issuecomment-1304507916  

Could you please take a look?Thank you!

---

## Comment 2

> Username: igaztanaga  
> Created at: 2022-11-08 20:33:35 UTC  
> Url: https://github.com/boostorg/interprocess/issues/185#issuecomment-1307793212  

I can't reproduce this problem with current develop code, the iunordered_set_index_allocation_test tests looks ok.
