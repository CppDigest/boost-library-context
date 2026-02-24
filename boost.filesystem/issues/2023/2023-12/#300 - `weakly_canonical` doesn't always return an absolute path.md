# #300 - `weakly_canonical` doesn't always return an absolute path [Closed]

> Username: Pesa  
> Created at: 2023-12-30 04:42:55 UTC  
> Updated at: 2024-01-07 21:36:58 UTC  
> Closed at: 2024-01-04 20:35:35 UTC  
> Url: https://github.com/boostorg/filesystem/issues/300  

The documentation of `weakly_canonical` says:  
  
> Let `head` be the path composed of the leading elements of `p` that exist and `tail` — from the rest of `p`. [...] Calls [canonical(head, base)](https://www.boost.org/doc/libs/1_84_0/libs/filesystem/doc/reference.html#canonical) or [canonical(head, base, ec)](https://www.boost.org/doc/libs/1_84_0/libs/filesystem/doc/reference.html#canonical), appends `tail` to the returned path using `operator/`. The result is then normalized and returned.  
  
From what I understand, this implies that the returned path will always be absolute, because `canonical()` returns a canonical path (which is absolute by definition), and appending `tail` to it should not turn an absolute path back into a relative path.  
  
However, there seems to be a case where the above isn't true. Consider the following test program:  
  
```cpp  
int main()  
{  
    std::cout << boost::filesystem::weakly_canonical("foo/bar") << "\n"            // (1)  
              << boost::filesystem::canonical("") << "\n"                          // (2)  
              << boost::filesystem::weakly_canonical("existing/foo/bar") << "\n";  // (3)  
}  
```  
  
In (1), `foo` does not exist. In (3), `existing` is an existing directory but `existing/foo` does not exist. With Boost 1.83.0, and assuming it's run from `/root`, the program will print:  
  
```  
"foo/bar"  
"/root"  
"/root/existing/foo/bar"  
```  
  
I would expect (1) to print `"/root/foo/bar"`, because:  
* `head` is empty and `canonical("")` returns the current working directory as an absolute path (`/root`), as seen in (2);  
* `tail` is `foo/bar`, and appending it to the result of the previous point should produce `/root/foo/bar`.  
  
Note also the inconsistency with case (3), where the first component of the input path exists, and the path returned by `weakly_canonical` is indeed absolute, as expected.

---

## Comment 1

> Username: Pesa  
> Created at: 2023-12-31 20:15:53 UTC  
> Url: https://github.com/boostorg/filesystem/issues/300#issuecomment-1873031385  

It is worth noting that the libstdc++ implementation of `std::filesystem` (tested with GCC 13.2.0) exhibits the same behavior as `boost::filesystem` in case (1). So maybe this behavior is intended and the only issue here is the Boost documentation being ambiguous/misleading in this corner case.  
  
I really dislike the inconsistency between (1) and (3) though. Or even worse, between  
```cpp  
weakly_canonical("foo/bar")  
```  
and  
```cpp  
weakly_canonical("./foo/bar")  
```

---

## Comment 2

> Username: Lastique  
> Created at: 2024-01-04 12:48:08 UTC  
> Url: https://github.com/boostorg/filesystem/issues/300#issuecomment-1877043812  

From the standard definition of `weakly_canonical`, it does look like the first case should be made absolute, relative to the current directory:  
  
http://eel.is/c++draft/fs.op.weakly.canonical#1  
http://eel.is/c++draft/fs.op.canonical#1  
http://eel.is/c++draft/fs.op.absolute  
  
I think, this is a genuine bug in Boost.Filesystem and libstdc++. Please, also report it to gcc developers.

---

## Comment 3

> Username: Pesa  
> Created at: 2024-01-05 19:51:05 UTC  
> Url: https://github.com/boostorg/filesystem/issues/300#issuecomment-1879183729  

> From the standard definition of `weakly_canonical`, it does look like the first case should be made absolute, relative to the current directory:  
>   
> http://eel.is/c++draft/fs.op.weakly.canonical#1 http://eel.is/c++draft/fs.op.canonical#1 http://eel.is/c++draft/fs.op.absolute  
  
To be honest, I'm not quite sure how to interpret that... the standard wording for `weakly_canonical` says "if any". Does that mean that `canonical` is *not* called at all if there are no leading elements that exist? Or that `canonical` is called with an empty path in that case? That's unclear to me.  
  
Another related observation: `std::filesystem::canonical("")` throws `filesystem_error` with GCC/libstdc++.

---

## Comment 4

> Username: Pesa  
> Created at: 2024-01-05 22:00:58 UTC  
> Url: https://github.com/boostorg/filesystem/issues/300#issuecomment-1879305652  

https://gcc.gnu.org/bugzilla/show_bug.cgi?id=113246

---

## Comment 5

> Username: Lastique  
> Created at: 2024-01-05 23:54:33 UTC  
> Url: https://github.com/boostorg/filesystem/issues/300#issuecomment-1879393743  

> To be honest, I'm not quite sure how to interpret that... the standard wording for `weakly_canonical` says "if any". Does that mean that `canonical` is *not* called at all if there are no leading elements that exist? Or that `canonical` is called with an empty path in that case?  
  
The latter.  
  
> Another related observation: `std::filesystem::canonical("")` throws `filesystem_error` with GCC/libstdc++.  
  
This is incorrect. `canonical` definition in the standard says it should return an absolute path with no dot or dot-dot elements that refer to the same file as `absolute("")`, which means `canonical("")` should return `current_path()` (given that the current path always exists).

---

## Comment 6

> Username: Pesa  
> Created at: 2024-01-06 00:12:35 UTC  
> Url: https://github.com/boostorg/filesystem/issues/300#issuecomment-1879446400  

> This is incorrect. `canonical` definition in the standard says it should return an absolute path with no dot or dot-dot elements that refer to the same file as `absolute("")`,   
  
Yes, this is true.  
  
> which means `canonical("")` should return `current_path()`  
  
Hmm, how did you reach that conclusion? is `""` the same as the current path? `equivalent(path(""), path("."))` returns false.

---

## Comment 7

> Username: jwakely  
> Created at: 2024-01-06 00:34:50 UTC  
> Url: https://github.com/boostorg/filesystem/issues/300#issuecomment-1879459966  

> > To be honest, I'm not quite sure how to interpret that... the standard wording for `weakly_canonical` says "if any". Does that mean that `canonical` is _not_ called at all if there are no leading elements that exist? Or that `canonical` is called with an empty path in that case?  
>   
> The latter.  
  
That would be nonsensical, because ...  
   
> > Another related observation: `std::filesystem::canonical("")` throws `filesystem_error` with GCC/libstdc++.  
>   
> This is incorrect. `canonical` definition in the standard says it should return an absolute path with no dot or dot-dot elements that refer to the same file as `absolute("")`, which means `canonical("")` should return `current_path()` (given that the current path always exists).  
  
[fs.op.canonical] very clearly says "`!exists(p)` is an error".  Since `exists("")` is always false, that means `canonical("")` is an error. I don't know how you conclude that it should return an absolute path for anything.  
  
`canonical("")` throws, and `canonical("", ec)` reports an error and returns an empty path. So it would be useless for `weakly_canonical` to call `canonical` with an empty path, because it means `weakly_canonical` would also always fail (either throwing, or returning an empty path as soon as `canonical("", ec)` fails).

---

## Comment 8

> Username: jwakely  
> Created at: 2024-01-06 00:39:24 UTC  
> Url: https://github.com/boostorg/filesystem/issues/300#issuecomment-1879462087  

Or to put it another way, the requirement for canonical(p) to return "an absolute path with no dot or dot-dot elements that refer to the same file as absolute(p)" doesn't hold if it reports an error. If it reports an error, it throws (so doesn't return anything) or it returns an empty path. That seems very clear to me.

---

## Comment 9

> Username: Pesa  
> Created at: 2024-01-06 02:18:05 UTC  
> Url: https://github.com/boostorg/filesystem/issues/300#issuecomment-1879500214  

> > This is incorrect. `canonical` definition in the standard says it should return an absolute path with no dot or dot-dot elements that refer to the same file as `absolute("")`, which means `canonical("")` should return `current_path()` (given that the current path always exists).  
>   
> [fs.op.canonical] very clearly says "`!exists(p)` is an error". Since `exists("")` is always false, that means `canonical("")` is an error.  
  
Right, that's a good point. I think I got confused by the fact that Boost's implementation of `canonical("")` returns the current path instead of raising an error. Even Boost's [reference documentation](https://www.boost.org/doc/libs/1_84_0/libs/filesystem/doc/reference.html#canonical) says that `!exists(p)` is an error, so it looks like the bug here is in `canonical()` (and the documentation of `weakly_canonical()` could be clarified).

---

## Comment 10

> Username: Pesa  
> Created at: 2024-01-06 02:27:43 UTC  
> Updated at: 2024-01-06 18:31:17 UTC  
> Url: https://github.com/boostorg/filesystem/issues/300#issuecomment-1879503347  

For the record, libc++ has the same bug, it returns the current path for `canonical("")`.  
EDIT: reported as https://github.com/llvm/llvm-project/issues/77170  
  
Interestingly, it returns an absolute path for `weakly_canonical("does-not-exist")`, which is the behavior I was hoping for...

---

## Comment 11

> Username: Lastique  
> Created at: 2024-01-06 09:21:09 UTC  
> Url: https://github.com/boostorg/filesystem/issues/300#issuecomment-1879605086  

`canonical` returns "A path that refers to the same file system object as `absolute(p)`." `absolute` "Composes an absolute path referencing the same file system location as `p` according to the operating system ([fs.conform.os]).`  
  
If `p` is an empty path, it refers to the current directory and therefore always exists. `absolute("")` returns `current_path()` on POSIX systems, per http://eel.is/c++draft/fs.op.absolute#7.  
  
Therefore `canonical("")` should succeed and return `current_path()`. Consequently, `weakly_canonical("")` should also return `current_path()` and `weakly_canonical("a/b")` where "a" doesn't exist should return `current_path() / "a/b"`.  
  
> `equivalent(path(""), path("."))` returns `false`.  
  
Yes, because "" and "." are not equivalent. "" refers to the current directory, and "." refers to the pseudo-file "." within the current directory.

---

## Comment 12

> Username: Lastique  
> Created at: 2024-01-06 10:55:39 UTC  
> Url: https://github.com/boostorg/filesystem/issues/300#issuecomment-1879636558  

> If `p` is an empty path, it refers to the current directory and therefore always exists.  
  
The "therefore always exists" part might not be true on POSIX, apparently, as one can delete the directory that is current for another process. This cannot be done on Windows, so there the current directory is guaranteed to exist.  
  
That is besides the point though, as we're not talking of a case when someone deletes the current directory behind out back.

---

## Comment 13

> Username: ChrisDenton  
> Created at: 2024-01-06 13:35:49 UTC  
> Updated at: 2024-01-06 13:38:15 UTC  
> Url: https://github.com/boostorg/filesystem/issues/300#issuecomment-1879689486  

> `canonical` returns "A path that refers to the same file system object as `absolute(p)`." `absolute` "Composes an absolute path referencing the same file system location as `p` according to the operating system ([fs.conform.os]).`  
  
Hm, but posix specifies that that an empty pathname must not be resolved successfully, e,g, [`realpath`](https://pubs.opengroup.org/onlinepubs/9699919799/functions/realpath.html) returns `ENOENT`.

---

## Comment 14

> Username: jwakely  
> Created at: 2024-01-06 13:47:35 UTC  
> Url: https://github.com/boostorg/filesystem/issues/300#issuecomment-1879692853  

`canonical` clearly says that `!exists(p)` is an error. What else is there to say? There is no need to try and interpret what it means to call `absolute("")` when `canonical("")` is an error. It's an error, it doesn't return the current path. It's an error.

---

## Comment 15

> Username: jwakely  
> Created at: 2024-01-06 13:49:30 UTC  
> Url: https://github.com/boostorg/filesystem/issues/300#issuecomment-1879693244  

Or is your point that `status("")` is _not_ an error?  
  
That doesn't seem like a useful interpretation of the standard, because every time a filesystem operation return `path()` on error, it's actually returning a valid path referring to the current directory. That's not the intent.

---

## Comment 16

> Username: Pesa  
> Created at: 2024-01-06 18:22:28 UTC  
> Url: https://github.com/boostorg/filesystem/issues/300#issuecomment-1879774779  

> `canonical` clearly says that `!exists(p)` is an error. What else is there to say? There is no need to try and interpret what it means to call `absolute("")` when `canonical("")` is an error. It's an error, it doesn't return the current path. It's an error.  
  
FWIW, I agree. The description of `canonical` in the standard is clear and unambiguous in this case.  
  
> Or is your point that `status("")` is _not_ an error?  
>   
> That doesn't seem like a useful interpretation of the standard, because every time a filesystem operation return `path()` on error, it's actually returning a valid path referring to the current directory. That's not the intent.  
  
That would be confusing indeed. Even Boost Filesystem returns false for `exists("")`, so not treating `canonical("")` as an error would be inconsistent.  
  
From @Lastique's comments, it seems that Boost considers `path("")` the same as the current path in some contexts. This may be acceptable for historical reasons in Boost Filesystem v3, but I hope the behavior can be changed to adhere more closely to the C++ standard in Filesystem v4.

---

## Comment 17

> Username: Pesa  
> Created at: 2024-01-06 18:29:11 UTC  
> Url: https://github.com/boostorg/filesystem/issues/300#issuecomment-1879776412  

> > `equivalent(path(""), path("."))` returns `false`.  
>   
> Yes, because "" and "." are not equivalent. "" refers to the current directory, and "." refers to the pseudo-file "." within the current directory.  
  
Interesting... However, `exists("")` is false, therefore that invocation of `equivalent()` should throw per [fs.op.equivalent](https://eel.is/c++draft/fs.op.equivalent#4)

---

## Comment 18

> Username: Lastique  
> Created at: 2024-01-06 19:01:26 UTC  
> Updated at: 2024-01-06 19:11:38 UTC  
> Url: https://github.com/boostorg/filesystem/issues/300#issuecomment-1879784512  

> `canonical` clearly says that `!exists(p)` is an error. What else is there to say?  
  
Ok, I see your point. So according to this, `canonical("")` should fail, and consequently `weakly_canonical("")` and `weakly_canonical("a/b")`, where `a` doesn't exist, should too (because both end up calling `canonical("")`). Is this your argument?  
  
I suppose, I could change `canonical` to work this way, but this would have no practical benefit, other than conformance to the standard wording. In Boost.Filesystem, I have the liberty to deviate from the standard, if it has practical benefits, and this might be such case.  
  
> Or is your point that `status("")` is _not_ an error?  
  
My point is that `canonical("")` should not be an error. The function definition (the Effects paragraph) does not require `p` to exist, but rather requires `absolute(p)` to exist, because that is the path `canonical` actually processes. Consequently, `weakly_canonical("a/b")` should also work and produce a path that is at least lexically canonical[1].  
  
I think, the Remark in `canonical` definition is, perhaps unintentionally, overly restrictive, as it prevents the `weakly_canonical("a/b")` case from succeeding. I think, the note should rather say that "`!exists(absolute(p))` is an error".  
  
> That doesn't seem like a useful interpretation of the standard, because every time a filesystem operation return `path()` on error, it's actually returning a valid path referring to the current directory. That's not the intent.  
  
The returned paths, as well as other values, in case of errors have no meaning - they are simply placeholders because *something* needs to be returned if no exception is thrown. That's the extent of the `path()` semantics in this context.  
  
In general, an empty path is just a relative path, and the usual path resolution rules apply when you call `absolute` on it. I believe, the same should apply to `canonical` and `weakly_canonical` as well, because ultimately those functions are path transformation functions, despite the fact they consult the filesystem during the operation.  
  
[1]: The standard doesn't provide a formal definition of a canonical path, but from `canonical` definition, a canonical path must be (a) absolute, (b) has no dot or dot-dot elements and (c) has no symlink elements. Obviously, `weakly_canonical` cannot ensure (c) for the part of the path that doesn't exist, but (a) and (b) still apply. If (a) and (b) cannot be satisfied for the returned path, `weakly_canonical` should fail. That is, the original behavior, where `weakly_canonical` would produce a relative path without error, is incorrect.

---

## Comment 19

> Username: Lastique  
> Created at: 2024-01-06 19:52:02 UTC  
> Url: https://github.com/boostorg/filesystem/issues/300#issuecomment-1879801230  

One side note. If you test `!exists(p)` in `canonical` implementation, you are resolving `p` twice. Once in `exists`, and another time when you call `absolute(p)`. Generally, relative path resolution should be performed only once, because the current path is volatile and can be changed in another thread. It only makes sense to call `absolute(p)` once and then check its existence and further process in `canonical`.

---

## Comment 20

> Username: jwakely  
> Created at: 2024-01-06 20:21:03 UTC  
> Url: https://github.com/boostorg/filesystem/issues/300#issuecomment-1879818316  

> > `canonical` clearly says that `!exists(p)` is an error. What else is there to say?  
>   
> Ok, I see your point. So according to this, `canonical("")` should fail, and consequently `weakly_canonical("")` and `weakly_canonical("a/b")`, where `a` doesn't exist, should too (because both end up calling `canonical("")`). Is this your argument?  
  
Yes.  
  
>   
> I suppose, I could change `canonical` to work this way, but this would have no practical benefit, other than conformance to the standard wording. In Boost.Filesystem, I have the liberty to deviate from the standard, if it has practical benefits, and this might be such case.  
  
Fair enough. But you said to report a bug to libstdc++, so I'm here pointing out that the standard is clear.  
  
  
> I think, the Remark in `canonical` definition is, perhaps unintentionally, overly restrictive, as it prevents the `weakly_canonical("a/b")` case from succeeding.  
  
No it doesn't. `weakly_canonical` just doesn't need to call `canonical("")`.  
  
> I think, the note should rather say that "`!exists(absolute(p))` is an error".  
  
Please submit an LWG issue if you think that it's wrong. Until then, the standard is clear.  
  
> > That doesn't seem like a useful interpretation of the standard, because every time a filesystem operation return `path()` on error, it's actually returning a valid path referring to the current directory. That's not the intent.  
>   
> The returned paths, as well as other values, in case of errors have no meaning - they are simply placeholders because _something_ needs to be returned if no exception is thrown. That's the extent of the `path()` semantics in this context.  
>   
> In general, an empty path is just a relative path,  
  
In terms of the generic path format it is a relative path, but one that names nothing. Libstdc++ treats it as an invalid argument to operations that should name a file.  
  
> and the usual path resolution rules apply when you call `absolute` on it.   
  
FWIW, with libstdc++ `absolute("")` is also an error. The spec says "Composes an absolute path referencing the same file system location as `p` according to the operating system." On my operating system, `ls ""` gives an error, and so does `opendir("")`. The OS does not consider `""` to be the current directory.  
  
  
>   
> [1]: The standard doesn't provide a formal definition of a canonical path, but from `canonical` definition,  
  
(It used to, but the definition was removed because it wasn't actually used anywhere.)

---

## Comment 21

> Username: jwakely  
> Created at: 2024-01-06 20:27:26 UTC  
> Url: https://github.com/boostorg/filesystem/issues/300#issuecomment-1879819760  

> One side note. If you test `!exists(p)` in `canonical` implementation, you are resolving `p` twice. Once in `exists`, and another time when you call `absolute(p)`.  
  
`absolute(p)` doesn't need to resolve it, at least for POSIX. The libstdc++ implementation is basically:  
  
```  
if (p.empty()) {  
  ec = std::make_error_code(std::errc::invalid_argument);  
  return path();  
}  
return p.is_absolute() ? p : current_path()/p;  
```  
  
  
> It only makes sense to call `absolute(p)` once and then check its existence and further process in `canonical`.  
  
Yes, that's what libstdc++ does.

---

## Comment 22

> Username: Lastique  
> Created at: 2024-01-06 20:34:07 UTC  
> Url: https://github.com/boostorg/filesystem/issues/300#issuecomment-1879821336  

> > I think, the Remark in `canonical` definition is, perhaps unintentionally, overly restrictive, as it prevents the `weakly_canonical("a/b")` case from succeeding.  
>   
> No it doesn't. `weakly_canonical` just doesn't need to call `canonical("")`.  
  
That's not how I'm reading `weakly_canonical` [definition](http://eel.is/c++draft/fs.op.weakly.canonical#1). It explicitly says to compose its result by means of `operator/=` applied to the result of `canonical` call.  
  
> `absolute(p)` doesn't need to resolve it, at least for POSIX. The libstdc++ implementation is basically:  
  
You're calling `current_path` - that's your relative path resolution in `absolute`. Another one is part of the `exists(p)` call in `canonical` (assuming you're following the standard wording). My point is that you should be calling `current_path` exactly once, in `absolute`. Then `canonical` should operate on the absolute path.

---

## Comment 23

> Username: Lastique  
> Created at: 2024-01-06 20:58:26 UTC  
> Url: https://github.com/boostorg/filesystem/issues/300#issuecomment-1879827179  

> But you said to report a bug to libstdc++, so I'm here pointing out that the standard is clear.  
  
It is clear, but it doesn't make libstdc++ implementation correct. I asked to report this to libstdc++ because it exhibited the same behavior as Boost.Filesystem, namely that `weakly_canonical` could return a relative path without error. As I understand, the standard says the call should fail in this case.

---

## Comment 24

> Username: Pesa  
> Created at: 2024-01-06 21:33:54 UTC  
> Url: https://github.com/boostorg/filesystem/issues/300#issuecomment-1879835233  

> > That doesn't seem like a useful interpretation of the standard, because every time a filesystem operation return `path()` on error, it's actually returning a valid path referring to the current directory. That's not the intent.  
>   
> The returned paths, as well as other values, in case of errors have no meaning - they are simply placeholders because _something_ needs to be returned if no exception is thrown. That's the extent of the `path()` semantics in this context.  
  
@Lastique, you're basically saying that when `path()` is used as a function argument it means "current path", and when it's returned from a function it means "no such path" or some other error occurred. That would make the API very error prone IMHO.  
  
> If p is an empty path, it refers to the current directory and therefore always exists  
  
If that's the case, then why is `exists("")` false in Boost Filesystem?

---

## Comment 25

> Username: Lastique  
> Created at: 2024-01-06 22:20:27 UTC  
> Updated at: 2024-01-06 22:37:51 UTC  
> Url: https://github.com/boostorg/filesystem/issues/300#issuecomment-1879847310  

> @Lastique, you're basically saying that when `path()` is used as a function argument it means "current path", and when it's returned from a function it means "no such path" or some other error occurred.  
  
Not every function accepts an empty path as input. In this bug, I'm discussing specifically `weakly_canonical`, as well as `canonical` and `absolute` in relation to it. All three of those can be considered as path transformation functions. The former two use `absolute` to make the input path absolute, and by its means convert empty paths to the current path - because that's how `absolute` is defined. It doesn't mean other functions will accept empty paths.  
  
Also, an empty path returned from a function is *not* an error indication. A set `error_code` is. You should not be using the returned path (or any other value) if the call indicated error by setting the `error_code`. It doesn't really matter what exact value is returned in case of error because it should never be used.  
  
> > If p is an empty path, it refers to the current directory and therefore always exists  
>   
> If that's the case, then why is `exists("")` false in Boost Filesystem?  
  
This was said in relation to `absolute` and `canonical`. That is, `absolute("")` returns `current_path()`, and that exists, when tested by `canonical`.

---

## Comment 26

> Username: Pesa  
> Created at: 2024-01-07 19:43:04 UTC  
> Url: https://github.com/boostorg/filesystem/issues/300#issuecomment-1880152588  

> Also, an empty path returned from a function is _not_ an error indication. A set `error_code` is. You should not be using the returned path (or any other value) if the call indicated error by setting the `error_code`. It doesn't really matter what exact value is returned in case of error because it should never be used.  
  
If the function has an `error_code` parameter, sure, I fully agree. But some functions like `path::parent_path()` or `path::relative_path()` return an empty path to indicate that the requested path does not "make sense", while the current dir is represented by a return value of `"."`. This isn't a problem per se because the caller can easily distinguish between `path()` and `path(".")`, but overall this leads to a somewhat less consistent API when compared with functions that treat `path()` as the current dir.  
  
Btw, thanks for updating the documentation!

---

## Comment 27

> Username: Lastique  
> Created at: 2024-01-07 20:55:53 UTC  
> Url: https://github.com/boostorg/filesystem/issues/300#issuecomment-1880168677  

There's no such thing as a "requested path does not make sense". The function either succeeds, meaning that it returns what it is supposed to return, or fails, in which case either an `error_code` is set or an exception is thrown.  
  
In particular, `path::parent_path` and `path::relative_path` may return an empty path, and this is not an indication of error.

---

## Comment 28

> Username: Pesa  
> Created at: 2024-01-07 21:07:50 UTC  
> Url: https://github.com/boostorg/filesystem/issues/300#issuecomment-1880172181  

> In particular, `path::parent_path` and `path::relative_path` may return an empty path, and this is not an indication of error.  
  
My point is that an empty path returned by those functions does *not* mean "current path", hence the inconsistency.

---

## Comment 29

> Username: Lastique  
> Created at: 2024-01-07 21:36:58 UTC  
> Url: https://github.com/boostorg/filesystem/issues/300#issuecomment-1880183250  

An empty path is an empty path. It does not mean "current path". It *resolves* to the current path by `absolute`. I've said this a number of times already, I'm not sure what's so confusing about it.
