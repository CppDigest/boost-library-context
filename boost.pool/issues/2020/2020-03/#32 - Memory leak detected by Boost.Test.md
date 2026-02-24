# #32 - Memory leak detected by Boost.Test [Closed]

> Username: HenryAWE  
> Created at: 2020-03-14 04:24:46 UTC  
> Updated at: 2020-05-30 04:12:20 UTC  
> Closed at: 2020-05-30 04:12:20 UTC  
> Url: https://github.com/boostorg/pool/issues/32  

## Description  
I'm implementing an ECS framework and it use the boost::pool_allocator  
But the Boost.Test reports that there is a memory leak.  
  
Piece of my code  
``` c++  
template <typename Key, typename T>  
using MapType = std::map<  
    Key, T,  
    std::less<std::int32_t>,  
    boost::pool_allocator<std::pair<const Key, T>>  
>;  
std::vector<MapType<std::int32_t, std::shared_ptr<component::BaseComponent>>> m_components;  
```  
and the output of the test (wrapped by the CTest)  
```  
Test project D:/repo/SubterraneanRose/build  
Constructing a list of tests  
Done constructing a list of tests  
Updating test list for fixtures  
Added 0 tests to meet fixture requirements  
Checking test dependency graph...  
Checking test dependency graph end  
test 6  
    Start 6: TestECS  
  
6: Test command: D:\repo\SubterraneanRose\build\source\player\test\ecs_test\ecs_test.exe  
6: Test timeout computed to be: 10000000  
6: Running 3 test cases...  
6:  
6: *** No errors detected  
6: Detected memory leaks!  
6: Dumping objects ->  
6: {6435} normal block at 0x000001F55103CB30, 528 bytes long.  
6:  Data: <@  Q            > 40 CB 03 51 F5 01 00 00 00 00 00 00 00 00 00 00  
6: {6434} normal block at 0x000001F551065230, 1808 bytes long.  
6:  Data: <hR Q    0R Q    > 68 52 06 51 F5 01 00 00 30 52 06 51 F5 01 00 00  
6: Object dump complete.  
1/1 Test #6: TestECS ..........................   Passed    0.11 sec  
  
The following tests passed:  
        TestECS  
  
100% tests passed, 0 tests failed out of 1  
  
Total Test time (real) =   0.16 sec  
```  
  
However, after I removed the allocator from the template arguments of the MapType,  
Boost.Test says that everything worked as normal  
  
## My Environment  
OS: Windows 10 1803  
Compiler: VS2017  
Boost version: 1.70.0

---

## Comment 1

> Username: HenryAWE  
> Created at: 2020-05-30 04:12:20 UTC  
> Url: https://github.com/boostorg/pool/issues/32#issuecomment-636272622  

It disappeared after building the project in release mode.
