# #319 - Cannot find function filesystem::copy_file in Boost version 1.85.0 [Closed]

> Username: shaakirag  
> Created at: 2024-07-31 20:29:28 UTC  
> Updated at: 2024-07-31 21:40:02 UTC  
> Closed at: 2024-07-31 21:40:01 UTC  
> Url: https://github.com/boostorg/filesystem/issues/319  

Hi,  
  
I am not able to find the function filesystem::copy_file in **Boost version 1.85.0**. I have tried version 1.84.0 as well, and could not find it either. Currently, I am using version 1.81.0 with no trouble.  
  
Here is how I compile Boost onto my Ubuntu 20.04 machine:  
  
```  
cd /opt && wget https://archives.boost.io/release/1.85.0/source/boost_1_85_0.tar.gz  
cd /opt && tar -zxvf boost_1_85_0.tar.gz  
cd /opt/boost_1_85_0 && ./bootstrap.sh  
cd /opt/boost_1_85_0 && ./b2 install -j`grep -c ^processor /proc/cpuinfo`  
ldconfig  
rm -f /opt/boost_1_85_0.tar.gz  
```  
Compiler version:  
![image](https://github.com/user-attachments/assets/0fb2bb7d-fbbb-43fe-adab-e868053d062b)  
  
When I compile version 1.81.0, I am able to run the following:  
  
`boost::filesystem::copy_file(src_filename, filename, boost::filesystem::copy_options::overwrite_existing);`  
  
Both src_filename and filename are HDF5 files.  
  
However, when I compile 1.85.0 and run the same program, I see the following error:  
  
![image](https://github.com/user-attachments/assets/a1d85462-6dfb-4ecd-90d3-1a0009463b00)  
  
What could be the cause of this issue? Is there an additional step required to install the filesystem library correctly?

---

## Comment 1

> Username: mclow  
> Created at: 2024-07-31 21:27:50 UTC  
> Url: https://github.com/boostorg/filesystem/issues/319#issuecomment-2261492885  

Based on 30 seconds of looking, it appears that `boost::filesystem::copy_file` is defined in libs/filesystem/src/operations.cpp, which means that you have to compile that file and link against it (or link against the filesystem library that `b2` should build.

---

## Comment 2

> Username: Lastique  
> Created at: 2024-07-31 21:40:01 UTC  
> Url: https://github.com/boostorg/filesystem/issues/319#issuecomment-2261507983  

It seems, either you have not recompiled the entire project with Boost 1.85.0 or you are including headers from an older Boost version. `boost::filesystem::detail::copy_file` in 1.85.0 has the third argument of type `boost::filesystem::copy_options`, not `unsigned int`. It was `unsigned int` in older Boost versions.  
  
Make sure you're using headers and linking to Boost.Filesystem library version 1.85.0, not some other version e.g. that you have installed in the system. This must be the case for the whole project.
