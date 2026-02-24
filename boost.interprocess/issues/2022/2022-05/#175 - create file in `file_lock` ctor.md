# #175 - [feature request] create file in `file_lock` ctor [Open]

> Username: timblechmann  
> Created at: 2022-05-27 01:04:49 UTC  
> Updated at: 2022-08-01 19:24:14 UTC  
> Url: https://github.com/boostorg/interprocess/issues/175  

the `file_lock` ctor throws if the file does not exist. it would be nice to be able to have a signature that creates the file automatically if it doesn't exist. currently the ctor uses `open_existing_file`, maybe it could be extended by a boolean argument to use `create_or_open_file` if the users wants that semantics.  
  
thoughts?

---

## Comment 1

> Username: igaztanaga  
> Created at: 2022-07-16 21:23:40 UTC  
> Url: https://github.com/boostorg/interprocess/issues/175#issuecomment-1186296295  

What is the advantage of doing that inside `file_lock` instead of doing it externally?

---

## Comment 2

> Username: timblechmann  
> Created at: 2022-08-01 19:24:14 UTC  
> Url: https://github.com/boostorg/interprocess/issues/175#issuecomment-1201618686  

* only a single system call is required (`open` or `create_file` take care of the rest), so it should be slightly faster  
* it reduces the risk of race conditions when multiple processes may create the file or delete the file. `open` / `create_file` is be atomic on the filesystem level, but once people start using library wrappers like `filesystem::exists()`, creating a file with `ofstream`, change permissions etc a layman developer will easily run into unintended race conditions
