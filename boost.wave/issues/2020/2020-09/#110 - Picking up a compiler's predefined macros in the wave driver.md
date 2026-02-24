# #110 - Picking up a compiler's predefined macros in the wave driver [Open]

> Username: eldiener  
> Created at: 2020-09-04 02:09:10 UTC  
> Updated at: 2020-09-04 16:31:09 UTC  
> Url: https://github.com/boostorg/wave/issues/110  

A number of compilers have commands to output the predefined macros for the compiler regardless of any source file and just depending on the command line to the compiler. The output is always of the form for each line:  
  
`#define SOME_MACRO SOME_EXPANSION`  
  
It would be nice if the Wave driver program could just pick up a file of these predefined macros as is, using its @filepath syntax for the file, and process it as predefined macros, rather than having to transform each define from the form above to the wave acceptable form of:  
  
`-P SOME_MACRO=SOME_EXPANSION`  
  
I understand that I can use some program or regex to transform the compiler's output file to the form which the wave driver accepts, but for convenience sake it would be nice if the wave driver accepted the first form above.

---

## Comment 1

> Username: jefftrull  
> Created at: 2020-09-04 03:03:00 UTC  
> Url: https://github.com/boostorg/wave/issues/110#issuecomment-686872460  

Have you tried using the `-F` option to read in the file containing the definitions?

---

## Comment 2

> Username: eldiener  
> Created at: 2020-09-04 05:11:34 UTC  
> Url: https://github.com/boostorg/wave/issues/110#issuecomment-686911161  

Thank you for notifying me about the -F option. That actually worked but since the file produced by a compiler also includes Wave predefined macros I get the message from the wave driver when it reads in the file:  
  
`warning: this predefined name may not be redefined: __STDC__`  
  
and wave does not process the file I am passing to it to expand macros. Should it not continue anyway and do its expansion ?

---

## Comment 3

> Username: jefftrull  
> Created at: 2020-09-04 05:16:31 UTC  
> Url: https://github.com/boostorg/wave/issues/110#issuecomment-686912808  

`__STDC__` is one of the special macros that is predefined through a different path, as Wave is currently designed. There are only a handful of them, starting around [here](https://github.com/boostorg/wave/blob/465fe2291f33232589f64cf4127da238ff942d38/include/boost/wave/util/cpp_macromap_predef.hpp#L215) - can you remove them first?

---

## Comment 4

> Username: hkaiser  
> Created at: 2020-09-04 13:05:22 UTC  
> Url: https://github.com/boostorg/wave/issues/110#issuecomment-687131932  

We could make it so that it is possible to redefine the basic macros in a file included using `-F` as your use case is probably the most likely one for using this option. Another alternative would be to have a new command line option for this.

---

## Comment 5

> Username: eldiener  
> Created at: 2020-09-04 16:31:09 UTC  
> Url: https://github.com/boostorg/wave/issues/110#issuecomment-687254593  

Either case would be fine. If I manually remove the defines for `__STDC__, __STDC_VERSION__, and __STDC_HOSTED__` from the -F file, Wave does work fine. But as you anticipated it would be much easier if Wave allowed me to redefine the basic macros since the -F file can be generated on the fly for many compilers as the compiler's predefined macros.
