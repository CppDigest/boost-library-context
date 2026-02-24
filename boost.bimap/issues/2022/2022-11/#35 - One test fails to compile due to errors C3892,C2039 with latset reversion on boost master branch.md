# #35 - One test fails to compile due to errors C3892,C2039 with latset reversion on boost master branch [Closed]

> Username: nnfdnkns  
> Created at: 2022-11-03 10:21:55 UTC  
> Updated at: 2022-11-03 18:15:42 UTC  
> Closed at: 2022-11-03 18:15:42 UTC  
> Url: https://github.com/boostorg/bimap/issues/35  

**Environment:**  
VS2019+Windows Server 2019  
  
**Issue description:**  
Found \boost\libs\bimap build fails to compile due to errors  C3892,C2039 in MSVC, this issue can be reproduced on https://github.com/boostorg/boost/commit/97f075e. Could you please take a look?  
  
**Reproduce steps:**  
1.git clone -c core.autocrlf=true --recursive ​https://github.com/boostorg/boost.git F:\gitP\boostorg\boost  
2.open a VS 2019 x64 command prompt and browse to F:\gitP\boostorg\boost  
3..\bootstrap  
4..\b2 headers variant=release --build-dir=..\out\amd64rel address-model=64  
5..\b2 variant=release --build-dir=..\out\amd64rel address-model=64  
6..\b2 -j16 variant=release --build-dir=F:\gitP\boostorg\boost\out\amd64rel libs\bimap\test address-model=64 2>&1  
  
**ErrorMessage:**  
libs\bimap\test\compile_fail\test_bimap_mutable_1.cpp(36): error C3892: 'cbm': you cannot assign to a variable that is const  
	Line 177: libs\bimap\test\compile_fail\test_bimap_mutable_3.cpp(35): error C3892: 'bm': you cannot assign to a variable that is const  
	Line 180: libs\bimap\test\compile_fail\test_bimap_mutable_2.cpp(36): error C3892: 'boost::bimaps::container_adaptor::associative_container_adaptor<Base,Iterator,ConstIterator,KeyType,IteratorToBaseConverter,IteratorFromBaseConverter,ValueToBaseConverter,ValueFromBaseConverter,KeyToBaseConverter,boost::mpl::vector1<T>>::find': you cannot assign to a variable that is const  
	Line 204: libs\bimap\test\compile_fail\test_bimap_info_2.cpp(35): error C2039: 'info': is not a member of 'boost::bimaps::relation::mutant_relation<TA,TB,Info,false>'  
	Line 230: .\boost/bimap/views/map_view.hpp(108): error C2039: 'info': is not a member of 'boost::bimaps::relation::structured_pair<TA,TB,Info,boost::bimaps::relation::normal_layout>'  
	Line 266: libs\bimap\test\compile_fail\test_bimap_info_1.cpp(36): error C3892: 'cbm': you cannot assign to a variable that is const  
	Line 313: ...failed testing.capture-outputF:\gitP\boostorg\boost\out\amd64rel\boost\bin.v2\libs\bimap\test\test_bimap_unordered.test\msvc-14.2\release\threading-multi\test_bimap_unordered.run...  
  
**log**  
[test.log.16.txt](https://github.com/boostorg/bimap/files/9927385/test.log.16.txt)

---

## Comment 1

> Username: pdimov  
> Created at: 2022-11-03 18:15:42 UTC  
> Url: https://github.com/boostorg/bimap/issues/35#issuecomment-1302498890  

Should be fixed now.
