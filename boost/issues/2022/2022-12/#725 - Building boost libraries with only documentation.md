# #725 - Building boost libraries with only documentation [Closed]

> Username: denzor200  
> Created at: 2022-12-25 11:26:29 UTC  
> Updated at: 2023-03-07 10:37:22 UTC  
> Closed at: 2023-03-07 10:37:22 UTC  
> Url: https://github.com/boostorg/boost/issues/725  

I just need to build a doc and to view it locally. I don't need to build the whole boost.  
How can I do it, is there any tutorials?  
  
I have tried this:  
-`b2 --hash tools`  
-`cd doc`  
-`b2`  
And then I saw this:  
```  
/mnt/d/repos/boost/tools/build/src/build/feature.jam:327: in validate-feature from module feature  
error: unknown feature "<format>"  
/mnt/d/repos/boost/tools/build/src/build/feature.jam:361: in expand-subfeatures-aux from module feature  
/mnt/d/repos/boost/tools/build/src/build/feature.jam:422: in feature.expand-subfeatures from module feature  
/mnt/d/repos/boost/tools/build/src/build/property.jam:572: in property.translate from module property  
/mnt/d/repos/boost/tools/build/src/build/property-set.jam:507: in property-set.create-from-user-input from module property-set  
/mnt/d/repos/boost/tools/build/src/build/property-set.jam:562: in property-set.refine-from-user-input from module property-set  
/mnt/d/repos/boost/tools/build/src/build/project.jam:752: in class@project-attributes.set from module object(project-attributes)@8616  
/mnt/d/repos/boost/tools/build/src/build/project.jam:1151: in project from module project-rules  
Jamfile.v2:13: in modules.load from module Jamfile</mnt/d/repos/boost/doc>  
/mnt/d/repos/boost/tools/build/src/build/project.jam:372: in load-jamfile from module project  
/mnt/d/repos/boost/tools/build/src/build/project.jam:64: in load from module project  
/mnt/d/repos/boost/tools/build/src/build/project.jam:142: in project.find from module project  
/mnt/d/repos/boost/tools/build/src/build-system.jam:618: in load from module build-system  
/mnt/d/repos/boost/tools/build/src/kernel/modules.jam:294: in import from module modules  
/mnt/d/repos/boost/tools/build/src/kernel/bootstrap.jam:135: in module scope from module  
```  
  
Also I tried this:  
-`b2 --hash tools`  
-`cd libs\json\doc`  
-`b2`  
And then I saw this:  
```  
Performing configuration checks  
  
    - default address-model    : 64-bit (cached) [1]  
    - default architecture     : x86 (cached) [1]  
    - symlinks supported       : yes (cached)  
WARNING: Unable to construct ./_reference-dir/sources.dox of type DOXYFILE with these properties: <abi>sysv <address-model>64 <architecture>x86 <asynch-exceptions>off <binary-format>elf <context-impl>fcontext <coverage>off <debug-symbols>on <deduced-address-model>64 <deduced-architecture>x86 <define>BOOST_ALL_NO_LIB=1 <doxygen.doxproc.index>no <doxygen.processor>xsltproc <doxygen:param>ABBREVIATE_BRIEF= <doxygen:param>ALIASES=esafe="@par Exception Safety" <doxygen:param>AUTOLINK_SUPPORT=NO <doxygen:param>CLASS_DIAGRAMS=NO <doxygen:param>DISTRIBUTE_GROUP_DOC=YES <doxygen:param>EXAMPLE_PATTERNS= <doxygen:param>EXPAND_ONLY_PREDEF=YES <doxygen:param>EXTRACT_ALL=YES <doxygen:param>EXTRACT_LOCAL_CLASSES=NO <doxygen:param>EXTRACT_PRIVATE=YES <doxygen:param>FILE_PATTERNS= <doxygen:param>GENERATE_HTML=NO <doxygen:param>GENERATE_XML=YES <doxygen:param>INLINE_INFO=NO <doxygen:param>INLINE_INHERITED_MEMB=YES <doxygen:param>JAVADOC_AUTOBRIEF=YES <doxygen:param>MACRO_EXPANSION=YES <doxygen:param>PREDEFINED=\  
            BOOST_FORCEINLINE \  
            BOOST_JSON_CLASS_DECL \  
            BOOST_JSON_DECL \  
            BOOST_JSON_DOCS \  
            BOOST_JSON_PUBLIC \  
            BOOST_SYMBOL_VISIBLE \  
            "BOOST_JSON_INLINE_VARIABLE(v, t)=constexpr t v;" \  
            "BOOST_JSON_NODISCARD=[[nodiscard]]" \  
            "BOOST_JSON_NS_BEGIN=namespace boost { namespace json {" \  
            "BOOST_JSON_NS_END=}}" <doxygen:param>PROJECT_BRIEF=JSON Library <doxygen:param>PROJECT_NAME=JSON <doxygen:param>SHOW_FILES=NO <doxygen:param>SHOW_INCLUDE_FILES=NO <doxygen:param>SHOW_NAMESPACES=NO <doxygen:param>SHOW_USED_FILES=NO <doxygen:param>SORT_MEMBERS_CTORS_1ST=YES <doxygen:param>SORT_MEMBER_DOCS=NO <doxygen:param>XML_OUTPUT=_reference-dir <exception-handling>on <extern-c-nothrow>off <format>html <hardcode-dll-paths>true <host-os>linux <implicit-dependency>object(notfile-target)@9922 <include>../../.. <inlining>off <install-dependencies>off <link>shared <local-visibility>hidden <optimization>off <os>LINUX <pch>on <preserve-test-targets>on <profiling>off <python-debugging>off <relevant>define:<relevant>toolset <relevant>link:<relevant>toolset <response-file>auto <rtti>on <runtime-debugging>on <runtime-link>shared <stdlib>native <strip>off <suppress-import-lib>false <symlink-location>project-relative <tag>@Jamfile</mnt/d/repos/boost>%Jamfile</mnt/d/repos/boost>.tag <target-os>linux <testing.execute>on <threadapi>pthread <threading>multi <toolset-gcc:version>9 <toolset>gcc <user-interface>console <variant>debug <vectorize>off <visibility>hidden <warnings-as-errors>off <warnings>on <xsl:param>boost.defaults=Boost  
WARNING: Considered these as possible generators:  
error: no generators were found for type 'DOXYFILE'  
error: and the requested properties  
error: make sure you've configured the needed tools  
See https://www.bfgroup.xyz/b2/manual/release/index.html#bbv2.overview.configuration  
To debug this problem, try the --debug-generators option.  
```  
Doxygen and xsltproc have already installed.

---

## Comment 1

> Username: sdarwin  
> Created at: 2022-12-25 12:20:26 UTC  
> Url: https://github.com/boostorg/boost/issues/725#issuecomment-1364673135  

The process should be similar to what you posted, such as:  
```  
cd libs\json\doc  
b2  
```  
However various prerequisites need to be in place.   I recently wrote some scripts that include all those steps to build documentation for a boost library, see https://github.com/boostorg/release-tools/tree/develop/build_docs

---

## Comment 2

> Username: denzor200  
> Created at: 2023-03-07 10:37:22 UTC  
> Url: https://github.com/boostorg/boost/issues/725#issuecomment-1457937600  

@sdarwin thanks!
