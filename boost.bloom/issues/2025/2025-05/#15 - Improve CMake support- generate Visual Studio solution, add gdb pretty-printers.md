# #15 - [peer-review] Improve CMake support: generate Visual Studio solution, add gdb pretty-printers [Closed]

> Username: Becheler  
> Created at: 2025-05-31 11:06:00 UTC  
> Updated at: 2025-06-24 18:18:34 UTC  
> Closed at: 2025-06-24 18:18:34 UTC  
> Url: https://github.com/boostorg/bloom/issues/15  

## Description  
  
Vinnie Falco (May 22) noted that Boost.Bloom’s current CMake setup makes it difficult to generate a Visual Studio solution and suggested providing a gdb pretty-printer analogous to the existing natvis file:  
- > “When I first looked at the library I was not able to use its CMakeLists.txt to create a proper Visual Studio solution and project files containing the header files of the library so that I can browse the code, and projects containing the tests so I can set breakpoints and inspect the behavior of the implementation at run-time. However, Mohammad kindly provided a patch and thus the version of the library I am reviewing is slightly different, as it contains this additional commit:  https://github.com/ashtum/bloom/commit/e119a3c868757104d929b21b5a0d06266d60b66f    
- > Although it is not a requirement, I do wish more libraries submitted for review had these additions to the CMakeLists.txt as my skills at compiling and running things outside of Visual Studio are incredibly limited (or rather, non-existent). I am far more likely to submit a review for a library that I can interact with in the Visual Studio IDE with a minimum of effort.”  
- > “The inclusion of the Visual Studio visualizer (‘natvis file’) is a very nice touch and an indicator of quality. Hopefully we will see the corresponding gdb pretty printer. Or perhaps someone might contribute it.”  
  
## Suggestion Actions  
  
1. **Enhance CMakeLists.txt for Visual Studio Integration**    
   - Ensure that running `cmake -G "Visual Studio 16 2019" ..` (or similar) generates a complete solution with:    
     - A project for the main `boost::bloom` headers and sources, allowing IDE navigation and IntelliSense.    
     - A project for unit tests/examples, so users can set breakpoints and debug test cases directly in Visual Studio.    
   - Verify that any added CMake targets are cross-platform-friendly (e.g., do not break Unix/Mac build flows).  
  
2. **Document CMake Usage Examples**    
   - Add a documentation section showing how to invoke CMake for both Visual Studio and Unix/Mac (e.g., `cmake -S . -B build -G "Visual Studio 16 2019"` and `cmake -S . -B build && cmake --build build`).   
   - Provide any necessary tips or prerequisites (e.g., minimum CMake version, generator names, build configurations).  
  
3. **Add or Solicit a gdb Pretty-Printer**    
   - Mirror the existing natvis file by providing a `gdb` Python script (e.g., `bloom_printer.py`) that:    
     - Prints `boost::bloom::filter` internals in a human-readable format.    
     - Displays key fields (e.g., bit array length, hash function parameters) when debugging in `gdb`.    
   - Include instructions in the documentation on how to register the pretty-printer (e.g., `source /path/to/bloom_printer.py` in `.gdbinit`).    
   - If no in-house contributor is available, add a “Help Wanted” note in the README or open a separate issue asking the community for contributions.  
  
## Desired Outcome  
  
- [ ] Verify CI Builds Across All Platforms  
- [ ] Update CI configuration (e.g., GitHub Actions, Azure Pipelines) to:    
     - [ ] Confirm Visual Studio builds succeed (e.g., “Build on Windows – VS2019”).    
     - [ ] Run tests under both Visual Studio and Unix/Mac.

---

## Comment 1

> Username: joaquintides  
> Created at: 2025-06-13 08:24:52 UTC  
> Url: https://github.com/boostorg/bloom/issues/15#issuecomment-2969517528  

* I don't think providing a VS-specific CMakeLists.txt is common practice in Boost.  
* CMake-based testing implemented:  
  * [Added test/CMakeLists.txt](https://github.com/joaquintides/bloom/commit/4a5023d3f233e3448c671494202686c4ce327396)  
  * [Added CMake-based testing to GHA](https://github.com/joaquintides/bloom/commit/97bf8f8e9343477a9bde7aeaaf5af6b00ae30307)  
* A GDB pretty-printer has been requested to @k3DW, who's an expert on the matter, but this won't probably be ready in time for Boost 1.89.

---

## Comment 2

> Username: Becheler  
> Created at: 2025-06-13 12:09:11 UTC  
> Url: https://github.com/boostorg/bloom/issues/15#issuecomment-2970192879  

Thanks — the CMake-based testing approach will definitely be helpful 👍🏽  
I also take not you're not recommending the Visual Studio–specific CML, which I intuitively agree with; introducing IDE-specific logic into a general-purpose library could easily lead to future maintenance issues.  
I'll leave the issue open while we wait for the GDB pretty-printer.

---

## Comment 3

> Username: k3DW  
> Created at: 2025-06-13 13:14:50 UTC  
> Url: https://github.com/boostorg/bloom/issues/15#issuecomment-2970362920  

I know I was tagged about the pretty-printers and not about the CMake, but I would also be willing to add some CMake such that it sets up the Visual Studio project in a nice way. I know this is done for certain Boost libraries, and this is also something I do in my own code, which mostly involves setting up the `FOLDER` property.  
  
Otherwise yes, I will write the GDB pretty-printer, aiming to complete it before the 1.89 cutoff.

---

## Comment 4

> Username: Becheler  
> Created at: 2025-06-24 18:18:21 UTC  
> Url: https://github.com/boostorg/bloom/issues/15#issuecomment-3001454367  

I will close this issue since it's taken care of. Please mention it once the gen pretty printer and VsC stuff are completed ☺️
