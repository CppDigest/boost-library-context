# #144 - 'make_offsets' Unresolved Symbol Issue in msvc143 [Closed]

> Username: shixin2013  
> Created at: 2023-03-22 01:13:06 UTC  
> Updated at: 2023-09-18 13:50:24 UTC  
> Closed at: 2023-09-18 13:50:24 UTC  
> Url: https://github.com/boostorg/mpi/issues/144  

On Windows, when using the shared MPI library with MSVC 143, a link error occurs for the 'scatterv' function. The error message indicates that 'scatterv' is calling 'make_offsets', but the definition for 'make_offsets' cannot be found.  
  
To reproduce this issue, simply invoke any version of the 'scatterv' function using the shared library. It's worth noting that Boost MPI test cases pass because they use the static library. I have tested the latest libraries from both vcpkg (with MSMPI) and from a custom compilation (with Intel MPI), and the issue persists in both cases.  
  
As a potential solution, I found that adding the export directive BOOST_MPI_DECL before the 'make_offsets' declaration in 'offsets.hpp' resolves this issue. However, I have limited knowledge of this library's design and am unsure if this is the proper approach. Please advise on whether this solution is appropriate or if there is an alternative fix.

---

## Comment 1

> Username: aminiussi  
> Created at: 2023-03-22 16:31:35 UTC  
> Url: https://github.com/boostorg/mpi/issues/144#issuecomment-1479896804  

should be fine in develop.  
  
Cheers

---

## Comment 2

> Username: yurybura  
> Created at: 2023-09-10 14:33:43 UTC  
> Url: https://github.com/boostorg/mpi/issues/144#issuecomment-1712830678  

@aminiussi Your change results in a compilation error:  
```  
boost/mpi/detail/offsets.hpp(36): error C2059: syntax error: '__declspec(dllimport)'  
```  
Full logs are [here](https://dev.azure.com/vcpkg/c1ee48cb-0df2-4ab3-8384-b1df5a79fe53/_apis/build/builds/93897/artifacts?artifactName=failure%20logs%20for%20x64-windows&api-version=7.1&%24format=zip).  
  
I've fixed this error with the patch:  
[ports/boost-mpi/fix-build-with-msvc.patch](https://github.com/microsoft/vcpkg/blob/dfb3a992671b01571a56a317e320636228b1669d/ports/boost-mpi/fix-build-with-msvc.patch)

---

## Comment 3

> Username: OscarDibert  
> Created at: 2023-09-10 18:42:27 UTC  
> Url: https://github.com/boostorg/mpi/issues/144#issuecomment-1712908599  

Hi, thanks for the fix. I'm currently not able to check it but if you do not hear from me by Tuesday please ping me again.
