# #64 Create CMakeLists.txt [Closed]

> Username: ken-matsui  
> Created at: 2020-12-14 09:13:53 UTC  
> Updated at: 2021-11-07 01:47:24 UTC  
> Closed at: 2021-05-29 17:30:15 UTC  
> Url: https://github.com/boostorg/property_tree/pull/64  

I created CMakeLists.txt on the root, test, and examples directories to make this library managed by CMake.  
  
## Installation  
```shell  
$ mkdir build && cd build  
build/$ cmake ..  
build/$ make  
build/$ make install  
```  
  
## Running Tests  
```shell  
$ mkdir build && cd build  
build/$ cmake .. -DBUILD_TESTING=ON  
build/$ make  
build/$ make test  
```  
  
## Running Examples  
```shell  
$ mkdir build && cd build  
build/$ cmake .. -DBUILD_EXAMPLES=ON  
build/$ make  
build/$ make example-${EXAMPLE_NAME}  
```

---

## Comment 1

> Username: madmongo1  
> Created_at: 2021-05-29 17:30:15 UTC  
> Url: https://github.com/boostorg/property_tree/pull/64#issuecomment-850869421  

Many thanks for this. Resolved in another commit.

---
