# #196 - Functions fail when used inside docker container in Jetson modules (ARM) [Closed]

> Username: Andyalevy  
> Created at: 2021-06-17 19:55:21 UTC  
> Updated at: 2021-06-17 20:52:50 UTC  
> Closed at: 2021-06-17 20:52:50 UTC  
> Url: https://github.com/boostorg/filesystem/issues/196  

When trying to run a project using functions from the filesystem project, in a container running inside a jetson machine, functions such as `exists` or `is_empty` fail with the following error: `boost::filesystem::status: Function not implmented: `  
  
It was done on a Jetson Xavier AGX flashed with Jetpack 4.5. Using boost 1.76 and GCC 7. The base image for the docker image was: nvcr.io/nvidia/l4t-base:r32.5.0

---

## Comment 1

> Username: Lastique  
> Created at: 2021-06-17 20:52:50 UTC  
> Url: https://github.com/boostorg/filesystem/issues/196#issuecomment-863555939  

Most likely duplicates https://github.com/boostorg/filesystem/issues/172.
