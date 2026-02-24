# #41 - List symbols in dynsym (as fall-back) [Closed]

> Username: ingomueller-net  
> Created at: 2020-06-30 10:59:09 UTC  
> Updated at: 2021-05-07 12:25:17 UTC  
> Closed at: 2021-05-07 12:25:17 UTC  
> Url: https://github.com/boostorg/dll/issues/41  

It seems like there are ELF libraries do not have any symbols in the `symtab` table. This is the case for some libraries shipped with my distro ([`libaio1`](https://packages.debian.org/buster/amd64/libaio1/filelist) in Debian Buster to be specific). However, the `dynsym` table does contain a list of exported symbols. I think it would be great if `library_info::symbols` could return those, potentially only as fall-back if the `symtab` table is empty. Currently, it does not return them, not even if I explicitly call `library_info::symbols(".dynsym")`.

---

## Comment 1

> Username: apolukhin  
> Created at: 2021-05-07 12:25:17 UTC  
> Url: https://github.com/boostorg/dll/issues/41#issuecomment-834329361  

Good catch!  
  
Many thanks for the report, fixed in https://github.com/boostorg/dll/commit/9d884adb48581209231655ea2576534a5e0846db
