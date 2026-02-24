# #81 update boost install to include natvis files [Closed]

> Username: cmazakas  
> Created at: 2025-03-25 17:38:14 UTC  
> Updated at: 2025-09-05 14:52:26 UTC  
> Closed at: 2025-09-05 14:52:26 UTC  
> Url: https://github.com/boostorg/cmake/pull/81  

Probe the interfaces sources of a library target and manually patch the target's properties such that the natvis files are installed and probably linked against for downstream targets.  
  
Partially closes https://github.com/boostorg/unordered/issues/307  
  
For pure CMake builds, it seems like natvis files aren't loaded into the solution unless the end-target has the natvis file as a source file, which is only possible when they're carried transitively as an interface source.  
  
This code enforces the largely existing idiom of using `extra/boost_<lib>.natvis` but we can maybe massage the regex to be more accommodating of other libraries like JSON and URL.

---

## Comment 1

> Username: pdimov  
> Created_at: 2025-08-29 17:54:04 UTC  
> Url: https://github.com/boostorg/cmake/pull/81#issuecomment-3237787937  

This dumps the `extra/` directory into `/usr/share` directly, which doesn't sit well with me. It should probably go into `/usr/share/boost_libname`, possibly even `/usr/share/boost_libname-1.89.0`.  
  
Otherwise libraries can easily install their files on top of each other if they have the same name.

---

## Review 2 [Commented]

> Username: k3DW  
> Created_at: 2025-09-04 02:59:36 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/cmake/pull/81#pullrequestreview-3183204704  

📁 include/BoostInstall.cmake

```diff
 240 | # Installs a single target
 218 |- # boost_install_target(TARGET target VERSION version [HEADER_DIRECTORY directory])
 241 |+ # boost_install_target(TARGET target VERSION version [HEADER_DIRECTORY directory] [EXTRA_DIRECTORY directory])
```

> Username: k3DW  
> Created_at: 2025-09-04 02:35:56 UTC  
> Updated_at: 2025-09-04 02:59:36 UTC  
> Url: https://github.com/boostorg/cmake/pull/81#discussion_r2320660869  

The `cmake_parse_arguments` call below is missing `EXTRA_DIRECTORY`

---

📁 include/BoostInstall.cmake

```diff
 574 |+ 
 575 |+     get_filename_component(__EXTRA_DIRECTORY "${__EXTRA_DIRECTORY}" ABSOLUTE)
 576 |+     install(DIRECTORY "${__EXTRA_DIRECTORY}/" DESTINATION "${CMAKE_INSTALL_DATADIR}")
```

> Username: k3DW  
> Created_at: 2025-09-04 02:46:46 UTC  
> Updated_at: 2025-09-04 02:59:36 UTC  
> Url: https://github.com/boostorg/cmake/pull/81#discussion_r2320672013  

I'm also concerned with dumping the "extra" directory directly into `CMAKE_INSTALL_DATADIR`. I'd suggest changing this current `if` statement to only normalize `__EXTRA_DIRECTORY`  
```cpp  
if(__EXTRA_DIRECTORY)  
  get_filename_component(__EXTRA_DIRECTORY "${__EXTRA_DIRECTORY}" ABSOLUTE)  
end()  
```  
  
Then inside the `foreach` below, adding the `install` call right before the call to `boost_install_target`  
```cpp  
if(__EXTRA_DIRECTORY AND NOT BOOST_SKIP_INSTALL_RULES AND NOT CMAKE_SKIP_INSTALL_RULES)  
  install(DIRECTORY "${__EXTRA_DIRECTORY}/" DESTINATION "${CMAKE_INSTALL_DATADIR}/${target}-${__VERSION}")  
endif()  
boost_install_target(...)  
```

---

📁 include/BoostInstall.cmake

```diff
 223 |+     foreach(src IN LISTS sources)
 224 |+ 
 225 |+       set(natvis_file "${extradir}/${lib}.natvis")
```

> Username: k3DW  
> Created_at: 2025-09-04 02:50:46 UTC  
> Updated_at: 2025-09-04 02:59:36 UTC  
> Url: https://github.com/boostorg/cmake/pull/81#discussion_r2320676053  

Given that we're only looking for natvis files that match this exact name, could we emit a warning if we encounter a natvis file that doesn't match? It would be nice to mitigate against the contributor/maintainer making this mistake  
  
Or maybe some other Boost tool should lint the library repos to maintain consistency. Then this suggestion would be worthwhile in that place

---

📁 include/BoostInstall.cmake

```diff
 227 |+       if("${src}" STREQUAL "${natvis_file}" OR "${src}" STREQUAL "$<BUILD_INTERFACE:${natvis_file}>")
 228 |+ 
 229 |+         message("successfully patching natvis for: ${lib}, with: ${natvis_file}")
```

> Username: k3DW  
> Created_at: 2025-09-04 02:52:02 UTC  
> Updated_at: 2025-09-04 02:59:36 UTC  
> Url: https://github.com/boostorg/cmake/pull/81#discussion_r2320677461  

Is this print just meant for development? It doesn't match the style of the other `message` calls in this file, and I'm not sure it's worth keeping

---

📁 include/BoostInstall.cmake

```diff
 228 |+ 
 229 |+         message("successfully patching natvis for: ${lib}, with: ${natvis_file}")
 230 |+         set_target_properties(${lib} PROPERTIES INTERFACE_SOURCES "$<BUILD_INTERFACE:${natvis_file}>;$<INSTALL_INTERFACE:${CMAKE_INSTALL_DATADIR}/${lib}.natvis>")
```

> Username: k3DW  
> Created_at: 2025-09-04 02:56:36 UTC  
> Updated_at: 2025-09-04 02:59:36 UTC  
> Url: https://github.com/boostorg/cmake/pull/81#discussion_r2320682344  

If you go with the change to the install directory, then you should probably forward along the `version` argument to this function, and use `${CMAKE_INSTALL_DATADIR}/${lib}-${version}/${lib}.natvis`


---

## Comment 3

> Username: cmazakas  
> Created_at: 2025-09-05 14:52:26 UTC  
> Url: https://github.com/boostorg/cmake/pull/81#issuecomment-3258634735  

@pdimov I'm handing this change off to Braden to take it all the way. @k3DW let me know if you need any help along the way, but hopefully everything is relatively self-explanatory.

---
