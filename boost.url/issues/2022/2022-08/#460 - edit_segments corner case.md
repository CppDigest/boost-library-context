# #460 - edit_segments corner case [Closed]

> Username: alandefreitas  
> Created at: 2022-08-26 17:02:49 UTC  
> Updated at: 2022-09-20 23:00:22 UTC  
> Closed at: 2022-09-20 23:00:22 UTC  
> Url: https://github.com/boostorg/url/issues/460  

And we probably have a problem with `edit_segments` we can't fix very easily. Where we have  
  
```cpp  
if( has_authority() ||  
        abs_hint == -1)  
        abs = is_path_absolute();  
```  
  
this is not completely correct, because `has_authority()` is not a sufficient condition. If `abs_hint` is `0`, `has_authority()` is `true`, and the input string is `""`, then an absolute path needs to become relative. For instance:  
  
url: `http://www.boost.org` (`abs_hint==0` and `has_authority()==true`)  
  
then we should set absolute path to `false` when `set_path("")`, even though it sets it to `true` in all other cases.  
  
That came up during the tests. For instance, when resolving  
  
`"http://a/b/c/d;p?q"` + `"//g"` we should get `"http://g"`  
  
we get `"http://g/"` depending on the order of the operations, which is not right.   
  
This is not so easy to fix because `edit_segments` no longer has access to the original string at this point so we need still need to discuss how to fix that. I fixed the problem at a higher level for now.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2022-08-26 22:40:54 UTC  
> Url: https://github.com/boostorg/url/issues/460#issuecomment-1229018808  

maybe we can add a data member to `any_segments_iter`?

---

## Comment 2

> Username: alandefreitas  
> Created at: 2022-08-26 23:52:58 UTC  
> Url: https://github.com/boostorg/url/issues/460#issuecomment-1229046860  

Yes. Or some `bool is_empty` to `edit_segments`.

---

## Comment 3

> Username: vinniefalco  
> Created at: 2022-09-11 23:37:43 UTC  
> Url: https://github.com/boostorg/url/issues/460#issuecomment-1243068817  

Are these still problems? I think I might have fixed them.

---

## Comment 4

> Username: alandefreitas  
> Created at: 2022-09-12 23:16:17 UTC  
> Updated at: 2022-09-13 20:27:03 UTC  
> Url: https://github.com/boostorg/url/issues/460#issuecomment-1244697875  

I ran a few tests and the behavior is still there. I'm starting to think the old behavior might be correct. I think this issue should become a small discussion on the formal semantics of `set_path` and, in particular, `set_path( "" )`:  
  
```cpp  
// issue 460  
url u( "https://www.boost.org");  
u.set_path("");  
assert(u.buffer() == "https://www.boost.org/");  
u.set_path_absolute(false);  
assert(u.buffer() == "https://www.boost.org");  
```  
  
This combination of `u.set_path("");` and `u.set_path_absolute(false);` is somewhat analogous to a "`remove_path(...)`" that doesn't exist. And this problem came up in one of the PRs about relative/absolute paths, where we remove equivalent components and that's how paths are "removed".  
  
I'm not sure `u.set_path("");` should make the path absolute though. The behavior of `set_path` seems misleading because, in the grammar, the initial `/` is part of the `path`.  
  
I see 3 options we could document as the formal semantics of `set_path`:  
  
1. Strict behavior: `set_path(string_view s)` expects a valid `path`:  
    - `url u("http://www.boost.org"); u.set_path("");` generates `"http://www.boost.org"`.  
    - `url u("http://www.boost.org"); u.set_path("/");` generates `"http://www.boost.org/"`.  
    - `url u("http://www.boost.org"); u.set_path("x");` fails because the slash is missing.  
    - `url u("http://www.boost.org"); u.set_path("/x");` generates `"http://www.boost.org/x"`.  
2. Behavior I expected when writing the resolve PR: `set_path(string_view s)` expects a valid `path`. A slash is prepended if required:  
    - `url u("http://www.boost.org"); u.set_path("");` generates `"http://www.boost.org"`.  
    - `url u("http://www.boost.org"); u.set_path("/");` generates `"http://www.boost.org/"`.  
    - `url u("http://www.boost.org"); u.set_path("x");` generates `"http://www.boost.org/x"`.  
    - `url u("http://www.boost.org"); u.set_path("/x");` generates `"http://www.boost.org/x"`.  
3. Current behavior: `set_path(string_view s)` uses `s` as `path` if `!u.has_authority()` and prepends a slash if `has_authority() && !s.starts_with('/')`:  
    - `url u("http://www.boost.org"); u.set_path(""); u.set_absolute_path(false);` generates `"http://www.boost.org"`.  
    - `url u("http://www.boost.org"); u.set_path("");` generates `"http://www.boost.org/"`.  
    - `url u("http://www.boost.org"); u.set_path("/");` generates `"http://www.boost.org/"`.  
    - `url u("http://www.boost.org"); u.set_path("x");` generates `"http://www.boost.org/x"`.  
    - `url u("http://www.boost.org"); u.set_path("/x");` generates `"http://www.boost.org/x"`.

---

## Comment 5

> Username: vinniefalco  
> Created at: 2022-09-13 13:35:05 UTC  
> Updated at: 2022-09-13 13:35:20 UTC  
> Url: https://github.com/boostorg/url/issues/460#issuecomment-1245423571  

There's no authority here:  
```  
url u( "www.boost.org" )  
```  
  
All you did was set a relative path whose only segment is "www.boost.org" :)  
  
Was this your intent?

---

## Comment 6

> Username: alandefreitas  
> Created at: 2022-09-13 17:37:35 UTC  
> Url: https://github.com/boostorg/url/issues/460#issuecomment-1245730670  

You're right. I fixed the examples.

---

## Comment 7

> Username: vinniefalco  
> Created at: 2022-09-13 18:55:54 UTC  
> Url: https://github.com/boostorg/url/issues/460#issuecomment-1245833356  

Well here are some general principles:  
  
1. we should preserve whether or not the path is absolute, unless the user is explicitly changing it  
  
2. we should avoid functions that can "fail" on the "wrong" user input (i.e. no "fails because the slash is missing")  
  
3. when we have a choice of correct outputs we should lean towards whatever would be already normalized  
  
And finally, when an authority is present the path is always absolute.  
  
Given these principles and facts what do you think is the best proposed resolution?

---

## Comment 8

> Username: alandefreitas  
> Created at: 2022-09-13 20:39:17 UTC  
> Url: https://github.com/boostorg/url/issues/460#issuecomment-1245931989  

OK. So we do have to move from behavior 3 to 2.  
  
1. Behavior 3 fails requirement 1.  
2. Behavior 1 fails requirement 2.  
3. All behaviors pass requirement 3.  
  
So this is the current behavior (3):  
  
```cpp  
        url u("http://www.boost.org");  
        assert( !u.is_path_absolute() );  
        u.set_path("");  
        assert( u.buffer() == "http://www.boost.org/" );  
        assert( u.is_path_absolute() );  
````  
  
and this is what it should be:  
  
```cpp  
        url u("http://www.boost.org");  
        assert( !u.is_path_absolute() );  
        u.set_path("");  
        assert( u.buffer() == "http://www.boost.org" );  
        assert( !u.is_path_absolute() );  
````

---

## Comment 9

> Username: alandefreitas  
> Created at: 2022-09-13 22:12:13 UTC  
> Url: https://github.com/boostorg/url/issues/460#issuecomment-1246002185  

Considering our recent discussion on slack and the evidence from the RFC, here is a summary of the best behavior:  
  
- `is_path_absolute` remains equivalent to `encoded_path().starts_with('/')`  
- `set_path("")` clears the path (behavior 2)  
- `remove_path` can also be considered correct  
  
So there is a list of quotes that support that:  
  
- an authority with an empty path is neither absolute nor relative, or   
- an empty path is not absolute, or   
- the absolute/relative distinction only makes sense for relative references  
  
> If a URI contains an authority component, then the path component must either be empty or begin with a slash ("/") character.   
  
It doesn't say anything about these paths being absolute or relative  
  
> `var absolute = path.substring(0, 1) === '/';` (from URI.js)   
  
`encoded_path().starts_with('/')` was also considered more intuitive in URI.js  
  
> A relative reference that does not begin with a slash character is termed a relative-path reference.   
  
The difference between absolute and relative is also in terms of `encoded_path().starts_with('/')`  
      
> Forward slash in URI/URL specification is a separator between the host (or host+port) part and the path. [ref](https://superuser.com/questions/1119385/is-a-terminating-forward-slash-technically-appropriate-when-specifying-a-url)  
  
Empty path is an absent path, which will be considered `""`  
  
> If the base URI has a defined authority component and an empty path, then return a string consisting of "/" concatenated with the reference's path   
  
Empty path is not defined as absolute or relative. "empty path" only.  
      
> In addition, a URI reference (Section 4.1) may be a relative-path reference  
  
The distinction is only made for relative references  
  
> A relative reference that begins with a single slash character is termed an absolute-path reference. A relative reference that does not begin with a slash character is termed a relative-path reference.  
  
Also `encoded_path().starts_with('/')`  
  
> from #general: right, a path that is absent and a path that is empty are the same. it's the only portion of the URL that can be so. All other portions can be present and empty and that's different from absent. (URLs require non-empty scheme). scheme://:@:/?#. Empty username, password, host, port, query and fragment.  
  
So path could be considered absent when empty. Empty is neither absolute nor relative.  
  
Also some practical problems that justify the behavior above:  
  
Practical problem 1: `u1.set_encoded_path(u2.encoded_path())` could change the result of `is_path_absolute()` otherwise  
  
Practical problem 2: `segments` would also need the context otherwise  
  
Practical problem 3: other behaviors are much harder to explain

---

## Comment 10

> Username: alandefreitas  
> Created at: 2022-09-13 22:16:48 UTC  
> Url: https://github.com/boostorg/url/issues/460#issuecomment-1246004926  

Related issue: #543
