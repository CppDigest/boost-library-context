# #41 - BoostConfig: Many target-dependent generator expressions evaluate to empty or wrong values. [Closed]

> Username: jjELT  
> Created at: 2020-04-22 07:06:27 UTC  
> Updated at: 2022-07-11 11:08:15 UTC  
> Closed at: 2022-07-11 11:08:15 UTC  
> Url: https://github.com/boostorg/boost_install/issues/41  

This is a copy of the [Gitlab Kitware Issue 20611](https://gitlab.kitware.com/cmake/cmake/-/issues/20611), which applies to both **FindBoost** and **BoostConfig**  
  
Maybe you want to deal with it differently?!:  
  
# Background  
  
I am linking to the **shared variant** of Boost::log and would like to copy the shared (.dll / .so) and import (.lib, .dll.a / .a) library files to the output folder using [target dependent generator expressions](https://cmake.org/cmake/help/latest/manual/cmake-generator-expressions.7.html#target-dependent-queries) in an post-build command like so:  
  
```  
# Add a post-build command to copy all shared and import libraries  
add_custom_command(TARGET ${TARGET_NAME} POST_BUILD  
	# Command to copy the shared library.  
	COMMAND "${CMAKE_COMMAND}" -E copy_if_different "$<TARGET_FILE:${ITERATED_COMPONENT}>" "${LIBRARY_OUTPUT_DIRECTORY}"  
	# Command to copy the import library.  
	COMMAND "${CMAKE_COMMAND}" -E copy_if_different "$<TARGET_LINKER_FILE:${ITERATED_COMPONENT}>" "${ARCHIVE_OUTPUT_DIRECTORY}" )  
```  
  
# Problem  
  
Many of the properties listed in [target dependent generator expressions](https://cmake.org/cmake/help/latest/manual/cmake-generator-expressions.7.html#target-dependent-queries) evaluate to empty or wrong (as far as my understanding goes) values. This is the case for both FindBoost and BoostConfig, i.e. no matter if setting `Boost_NO_BOOST_CMAKE=ON` or `Boost_NO_BOOST_CMAKE=OFF`.  
  
Here is a list of some of the values the generator expressions evaluate to:  
  
```  
TARGET_NAME_IF_EXISTS: Boost::log  
TARGET_FILE: <BOOST_ROOT>/lib/shared/libboost_log-mgw93-mt-d-x64-1_72.dll.a  
TARGET_FILE_BASE_NAME: Boost::log  
TARGET_FILE_PREFIX:   
TARGET_FILE_SUFFIX:   
TARGET_FILE_NAME: libboost_log-mgw93-mt-d-x64-1_72.dll.a  
TARGET_FILE_DIR: <BOOST_ROOT>/lib/shared  
TARGET_LINKER_FILE: <BOOST_ROOT>/lib/shared/libboost_log-mgw93-mt-d-x64-1_72.dll.a  
TARGET_LINKER_FILE_BASE_NAME: Boost::log  
TARGET_LINKER_FILE_PREFIX:   
TARGET_LINKER_FILE_SUFFIX:   
TARGET_LINKER_FILE_NAME: libboost_log-mgw93-mt-d-x64-1_72.dll.a  
TARGET_LINKER_FILE_DIR: <BOOST_ROOT>/lib/shared  
```  
  
and what I would have expected:  
  
```  
TARGET_NAME_IF_EXISTS: Boost::log  
TARGET_FILE: <BOOST_ROOT>/lib/shared/libboost_log-mgw93-mt-d-x64-1_72.dll  
TARGET_FILE_BASE_NAME: boost_log-mgw93-mt-d-x64-1_72  
TARGET_FILE_PREFIX: lib  
TARGET_FILE_SUFFIX: .dll  
TARGET_FILE_NAME: libboost_log-mgw93-mt-d-x64-1_72.dll  
TARGET_FILE_DIR: <BOOST_ROOT>/lib/shared  
TARGET_LINKER_FILE: <BOOST_ROOT>/lib/shared/libboost_log-mgw93-mt-d-x64-1_72.dll.a  
TARGET_LINKER_FILE_BASE_NAME: boost_log-mgw93-mt-d-x64-1_72  
TARGET_LINKER_FILE_PREFIX: lib  
TARGET_LINKER_FILE_SUFFIX: .dll.a  
TARGET_LINKER_FILE_NAME: libboost_log-mgw93-mt-d-x64-1_72.dll.a  
TARGET_LINKER_FILE_DIR: <BOOST_ROOT>/lib/shared  
```  
  
That way I could easily copy the files.  
  
Also the [limited availability of string-manipulation generator expressions as already discussed](https://discourse.cmake.org/t/how-to-remove-substring-suffix-using-generator-expressions/701/4) prevents me from quickly replacing the `CMAKE_IMPORT_LIBRARY_SUFFIX` with the `CMAKE_SHARED_LIBRARY_SUFFIX`.  
  
# Workaround  
  
I thought of a solution, which is not very pretty, but it works:  
  
```  
foreach(CONFIG_TYPE Debug Release)  
	get_target_property(LIBRARY_FULL_PATH_${CONFIG_TYPE} Boost::log "LOCATION_${CONFIG_TYPE}")  
	get_filename_component(LIBRARY_DIRECTORY_${CONFIG_TYPE} ${LIBRARY_FULL_PATH_${CONFIG_TYPE}} "DIRECTORY")  
	get_filename_component(LIBRARY_NAME_${CONFIG_TYPE} ${LIBRARY_FULL_PATH_${CONFIG_TYPE}} "NAME_WE")						  
endforeach()   
  
# Add a post-build command to copy all shared and import libraries  
add_custom_command(TARGET ${TARGET_NAME} POST_BUILD  
	# Command to copy the shared library.  
	COMMAND "${CMAKE_COMMAND}" -E copy_if_different "$<IF:$<CONFIG:Debug>,${LIBRARY_DIRECTORY_Debug}/${LIBRARY_NAME_Debug},${LIBRARY_DIRECTORY_Release}/${LIBRARY_NAME_Release}>${CMAKE_SHARED_LIBRARY_SUFFIX}" "${LIBRARY_OUTPUT_DIRECTORY}"  
	# Command to copy the import library.  
	COMMAND "${CMAKE_COMMAND}" -E copy_if_different "$<IF:$<CONFIG:Debug>,${LIBRARY_DIRECTORY_Debug}/${LIBRARY_NAME_Debug},${LIBRARY_DIRECTORY_Release}/${LIBRARY_NAME_Release}>${CMAKE_IMPORT_LIBRARY_SUFFIX}" "${ARCHIVE_OUTPUT_DIRECTORY}" )  
```  
  
There is no such things as `${LIBRARY_DIRECTORY_$<CONFIG>}`, right? I get this error:  
>  Invalid character ('<') in a variable name  
  
Is there a way to go around this? I tried this without success:  
```  
set(LIBRARY_NAME_GENEX LIBRARY_NAME_$<CONFIG>)	  
...  
COMMAND "${CMAKE_COMMAND}" -E echo $<GENEX_EVAL:${LIBRARY_NAME_GENEX}> # 'LIBRARY_NAME_Debug'  
COMMAND "${CMAKE_COMMAND}" -E echo $<GENEX_EVAL:${${LIBRARY_NAME_GENEX}}> # '' (empty string)  
...  
```
