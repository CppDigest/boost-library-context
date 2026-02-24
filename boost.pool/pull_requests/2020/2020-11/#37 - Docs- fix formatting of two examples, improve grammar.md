# #37 Docs: fix formatting of two examples, improve grammar [Merged]

> Username: eli-b  
> Created at: 2020-11-30 16:52:38 UTC  
> Updated at: 2020-12-02 17:25:02 UTC  
> Merged at: 2020-12-02 17:25:02 UTC  
> Closed at: 2020-12-02 17:25:02 UTC  
> Url: https://github.com/boostorg/pool/pull/37  

I really tried to generate the html files, but after figuring out that I need quickbook and figuring out how to install it, I'm getting the following error:  
```  
/home/eli/pool/doc/jamfile.v2:118: Unescaped special character in argument <format>pdf:<xsl:param>boost.url.prefix=I:/boost-sandbox/guild/pool/libs/pool/doc/html  
/home/eli/pool/doc/jamfile.v2:46: in modules.load  
*** argument error  
* rule doxygen ( target : sources + : requirements * : default-build * : usage-requirements * )  
* called with: ( autodoc :  : <doxygen:param>WARNINGS=YES <doxygen:param>WARN_LOGFILE=AutoDoxywarnings.log <doxygen:param>RECURSIVE=NO <doxygen:param>EXTRACT_ALL=NO <doxygen:param>HIDE_UNDOC_MEMBERS=YES <doxygen:param>EXTRACT_PRIVATE=NO <doxygen:param>MACRO_EXPANSION=YES <doxygen:param>EXPAND_ONLY_PREDEF=YES <doxygen:param>PREDEFINED="BOOST_PREVENT_MACRO_SUBSTITUTION=" "BOOST_STATIC_CONSTANT(t,v)=static const t v" "BOOST_DOXYGEN=1" <xsl:param>boost.doxygen.reftitle=Boost.Pool C++ Reference )  
* missing argument sources  
/home/eli/Downloads/boost_1_74_0/tools/build/src/tools/doxygen.jam:627:see definition of rule 'doxygen' being called  
/home/eli/Downloads/boost_1_74_0/tools/build/src/build/project.jam:372: in load-jamfile  
/home/eli/Downloads/boost_1_74_0/tools/build/src/build/project.jam:64: in load  
/home/eli/Downloads/boost_1_74_0/tools/build/src/build/project.jam:142: in project.find  
/home/eli/Downloads/boost_1_74_0/tools/build/src/build/targets.jam:453: in find-really  
/home/eli/Downloads/boost_1_74_0/tools/build/src/build/targets.jam:475: in class@project-target.find  
/home/eli/Downloads/boost_1_74_0/tools/build/src/build-system.jam:724: in load  
/home/eli/Downloads/boost_1_74_0/tools/build/src/kernel/modules.jam:295: in import  
/home/eli/Downloads/boost_1_74_0/tools/build/src/kernel/bootstrap.jam:139: in boost-build  
/home/eli/Downloads/boost_1_74_0/boost-build.jam:17: in module scope  
```  
  
I'd humbly suggest making it easier to contribute documentation fixes.

---
