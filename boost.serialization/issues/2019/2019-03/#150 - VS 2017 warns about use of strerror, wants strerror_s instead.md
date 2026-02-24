# #150 - VS 2017 warns about use of strerror, wants strerror_s instead [Closed]

> Username: HDembinski  
> Created at: 2019-03-20 07:40:16 UTC  
> Updated at: 2019-09-20 19:22:41 UTC  
> Closed at: 2019-09-20 19:22:41 UTC  
> Url: https://github.com/boostorg/serialization/issues/150  

On AppVeyor with the Visual Studio 2017 image, I get the following warning:  
  
c:\projects\boost\libs\serialization\src\basic_xml_grammar.ipp(197): warning C4996: 'strerror': This function or variable may be unsafe. Consider using strerror_s instead. To disable deprecation, use _CRT_SECURE_NO_WARNINGS. See online help for details.  
  
I propose to add this define to build/Jamfile.v2 to avoid the warning, which seems unwarranted here as far as I can see.
