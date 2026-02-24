# #45 Fix read_symlink with windows junctions points [Closed]

> Username: edgare  
> Created at: 2017-06-05 16:48:26 UTC  
> Updated at: 2020-05-02 17:08:07 UTC  
> Closed at: 2020-05-02 17:08:07 UTC  
> Url: https://github.com/boostorg/filesystem/pull/45  

On windows `is_symlink` considers both symbolic links and junction points to be symlinks. However `read_symlink` assumes a symbolic sink, which results in reading from bad offsets in the `REPARSE_DATA_BUFFER` struct.

---

## Comment 1

> Username: Lastique  
> Created_at: 2020-05-02 17:08:07 UTC  
> Url: https://github.com/boostorg/filesystem/pull/45#issuecomment-622984769  

Should be fixed by https://github.com/boostorg/filesystem/pull/100.

---
