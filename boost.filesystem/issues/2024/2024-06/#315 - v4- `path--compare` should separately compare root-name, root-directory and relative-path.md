# #315 - v4: `path::compare` should separately compare root-name, root-directory and relative-path [Open]

> Username: Lastique  
> Created at: 2024-06-29 16:04:38 UTC  
> Updated at: 2024-06-29 16:04:38 UTC  
> Url: https://github.com/boostorg/filesystem/issues/315  

Currently, v3 and v4 perform path comparison element-wise, using iteration over path elements with iterators. This means that if one of the paths is missing root-name or root-directory, the algorithm may compare root-name or root-directory against a filename in the other path.  
  
v4 should implement std::filesystem algorithm \[fs.path.compare\], which separately compares root-names, root-directories and then the elements of `relative_path()`.
