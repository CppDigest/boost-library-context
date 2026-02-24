# #270 - shell support for v2 [Open]

> Username: klemens-morgenstern  
> Created at: 2022-08-30 09:18:29 UTC  
> Updated at: 2023-03-05 18:14:54 UTC  
> Url: https://github.com/boostorg/process/issues/270  

The `shell` support isn't ported to `v2` yet. It might be a good idea to support this.  
  
On windows we can just pass the shell to `CreateProcess` on posix [wordexp](https://linux.die.net/man/3/wordexp) should to the trick, in combination with search_path.  
  
The usage would be   
  
```cpp  
v2::process my_proc(v2::shell("echo foo bar"), ...);  
```

---

## Comment 1

> Username: klemens-morgenstern  
> Created at: 2022-08-30 09:22:22 UTC  
> Url: https://github.com/boostorg/process/issues/270#issuecomment-1231401182  

If `process_group`s (#259) are available it would also be possible to add a DSL for shell commands.  
  
```cpp  
using namespace v2::dsl;  
v2::process_group grp{  
    ctx,   
    v2::shell("cat secret-stuff") | v2::shell("grep banking") > "/dev/null"  
};  
  
```

---

## Comment 2

> Username: ghost  
> Created at: 2023-03-05 04:34:26 UTC  
> Updated at: 2023-03-05 04:35:01 UTC  
> Url: https://github.com/boostorg/process/issues/270#issuecomment-1454987566  

wordexp is not implemented on OpenBSD because OpenBSD devs consider it a security risk. I would recommend using this NetBSD port for OpenBSD: https://github.com/ibara/libwordexp

---

## Comment 3

> Username: klemens-morgenstern  
> Created at: 2023-03-05 04:41:55 UTC  
> Url: https://github.com/boostorg/process/issues/270#issuecomment-1454988633  

it's not available as a third-party dependency?

---

## Comment 4

> Username: ghost  
> Created at: 2023-03-05 18:14:53 UTC  
> Url: https://github.com/boostorg/process/issues/270#issuecomment-1455163667  

It might be. I'll check as soon as I have access to openbsd again.
