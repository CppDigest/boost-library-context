# #19 - Clang-cl flags -Wmicrosoft-unqualified-friend warnings [Open]

> Username: rubenvb  
> Created at: 2019-11-22 09:48:10 UTC  
> Updated at: 2019-11-22 09:48:56 UTC  
> Url: https://github.com/boostorg/bimap/issues/19  

In Boost 1.70 (and current master, but I don't have the line numbers handy), these lines spawn a warning:  
> unqualified friend declaration referring to type outside of the nearest enclosing namespace is a Microsoft extension; add a nested name specifier  
map_view_iterator.hpp:  
87, 138, 174, 215, 252, 293, 329  
  
It seems to me that simply adding a `::boost::iterators::` qualification will fix this issue. See e.g. https://kod.acikkaynak.gov.tr/libreoffice/core/commit/aef6da021da33f099f99ed633814b8771ad4abda which seems to "fix" the warning locally by disabling the warning.  
  
You can try Clang-cl by installing the CLang-cl options in the VS2019 installer, and e.g. running CMake with `-T"ClangCL"`, or specifying the compiler manually using `-DCMAKE_CXX_COMPILER=path/to/clang-cl.exe`. You can also just use LLVM's version of clang-cl, it seems to be identical. Just make sure you have either VS2017 or VS2019 installed.
