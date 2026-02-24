# #529 - Boost create_directories debug. [Open]

> Username: jaskarannagi19  
> Created at: 2021-06-25 09:02:10 UTC  
> Updated at: 2021-06-25 09:02:10 UTC  
> Url: https://github.com/boostorg/boost/issues/529  

#0  0x00007ffff6cc47b4 in boost::filesystem::path::filename() const () from /home/jaskaran/self-msbai-demo/self_developbranch/intu/packages/naoqi-sdk-2.1.4.13-linux64/lib/libboost_filesystem.so.1.62.0  
#1  0x00007ffff6cbefd8 in boost::filesystem::detail::create_directories(boost::filesystem::path const&, boost::system::error_code*) ()  
   from /home/jaskaran/self-msbai-demo/self_developbranch/intu/packages/naoqi-sdk-2.1.4.13-linux64/lib/libboost_filesystem.so.1.62.0  
#2  0x00007ffff7b93bde in boost::filesystem::create_directories (p=...)  
    at /home/jaskaran/self-msbai-demo/self_developbranch/intu/packages/naoqi-sdk-2.1.4.13-linux64/include/boost/filesystem/operations.hpp:566  
#3  0x00007ffff6fe7162 in DataCache::Initialize (this=0x555555613d48, a_CachePath="./cache/image_classifier/", maxCacheSize=104857600, maxCacheAge=0, a_Extention=".zip")  
    at /home/jaskaran/self-msbai-demo/self_developbranch/intu/lib/cpp-sdk/src/utils/DataCache.cpp:50  
#4  0x00007ffff7c70e3b in ImageClassifier::OnStart (this=0x555555613ca0) at /home/jaskaran/self-msbai-demo/self_developbranch/intu/src/classifiers/ImageClassifier.cpp:98  
#5  0x00007ffff7c6594a in ClassifierManager::AddClassifier (this=0x555555612010, a_spClassifier=..., a_bOverride=false)  
    at /home/jaskaran/self-msbai-demo/self_developbranch/intu/src/classifiers/ClassifierManager.cpp:126  
#6  0x00007ffff7c65224 in ClassifierManager::Start (this=0x555555612010) at /home/jaskaran/self-msbai-demo/self_developbranch/intu/src/classifiers/ClassifierManager.cpp:44  
#7  0x00007ffff7b67fe0 in SelfInstance::Start (this=0x7fffffffdb70) at /home/jaskaran/self-msbai-demo/self_developbranch/intu/src/SelfInstance.cpp:348  
#8  0x00007ffff7b68716 in SelfInstance::Run (this=0x7fffffffdb70) at /home/jaskaran/self-msbai-demo/self_developbranch/intu/src/SelfInstance.cpp:402  
#9  0x00007ffff7b93460 in SelfMain (argc=1, argv=0x7fffffffdf58) at /home/jaskaran/self-msbai-demo/self_developbranch/intu/src/SelfMain.cpp:301  
#10 0x000055555558005c in main (argc=1, argv=0x7fffffffdf58) at /home/jaskaran/self-msbai-demo/self_developbranch/intu/src/main.cpp:35  
  
  
For some reason boost is throwing error for creating directories. Using boost version 1.62 cannot upgrade version higher than that  
  
Please help !!
