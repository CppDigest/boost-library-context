# #36 refactor name_generator to have a configurable hash algorithm [Merged]

> Username: jeking3  
> Created at: 2017-09-25 23:14:01 UTC  
> Updated at: 2017-10-18 19:50:54 UTC  
> Merged at: 2017-10-10 13:01:44 UTC  
> Closed at: 2017-10-10 13:01:45 UTC  
> Url: https://github.com/boostorg/uuid/pull/36  

and provide backwards compatibility for sha1, and also added  
md5 to complete the RFC 4122 spec implementation  
  
as such, boost::uuids::name_generator is deprecated, however  
still defined to use the same implementation in previous boost  
releases, and name_generator_sha1 as well as name_generator_md5  
now exist, in preparation for whatever will replace sha1.  
  
to properly test the new header structure, I took the bjam  
rule from winapi to isolate each header as an include and make sure it  
has no dependencies on other headers to be included first,  
and was able to remove a few test files that became unnecessary.  
  
Appveyor: https://ci.appveyor.com/project/jeking3/uuid/build/1.0.59-develop  
Travis: https://travis-ci.org/jeking3/uuid/builds/280110745  
  
This fixes #26

---

## Review 1 [Changes requested]

> Username: Lastique  
> Created_at: 2017-09-26 08:48:27 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/uuid/pull/36#pullrequestreview-65138711  

📁 include/boost/uuid/name_generator.hpp

```diff
  14 |+ #include <boost/cstdint.hpp>
  15 | #include <boost/static_assert.hpp>
  16 |+ #include <boost/uuid/detail/md5.hpp>
```

> Username: Lastique  
> Created_at: 2017-09-26 08:41:45 UTC  
> Updated_at: 2017-10-09 01:33:25 UTC  
> Url: https://github.com/boostorg/uuid/pull/36#discussion_r140991209  

Please, avoid including unnecessary code. I would suggest moving `basic_name_generator` to a separate (new) header and leaving only `name_generator` in name_generator.hpp. The other typedefs with their respective includes can go in their new headers.  
  
Another way to do this is create a name_generator directory with the headers, each header defining one component, and making name_generator.hpp include all headers from that directory. The headers in the name_generator directory can be named after the components they define.  
  
The point is to allow users to include only the bits they need and avoid the rest. No one will use the MD5-based generator, so this is dead code 99% of the time and only slows down compilation and adds potential problems with compilation.


📁 include/boost/uuid/sha1.hpp

```diff
  27 |- } // namespace std
  12 |+ #if defined(__GNUC__) || defined(_MSC_VER)
  13 |+ #pragma message("This header is deprecated, use boost/uuid/detail/sha1.hpp instead.")
```

> Username: Lastique  
> Created_at: 2017-09-26 08:44:34 UTC  
> Updated_at: 2017-10-09 01:33:25 UTC  
> Url: https://github.com/boostorg/uuid/pull/36#discussion_r140991864  

I think, the message should say that the header is implementation detail.

> Username: jeking3  
> Created_at: 2017-09-26 14:29:02 UTC  
> Updated_at: 2017-10-09 01:33:25 UTC  
> Url: https://github.com/boostorg/uuid/pull/36#discussion_r141073823  

Given sha1 and seed_rng are already in the detail namespace, am I required to provide this mechanism?  I believe the answer is no, but I want to make the transition smooth because I suspect there are many projects that rely on uuid sha1 just for the sha1 implementation.

> Username: Lastique  
> Created_at: 2017-09-26 14:39:42 UTC  
> Updated_at: 2017-10-09 01:33:25 UTC  
> Url: https://github.com/boostorg/uuid/pull/36#discussion_r141077164  

I don't think relying on Boost.UUID just for the SHA1 implementation is reasonable. Especially given that it is an implementation detail. That said, the header is in the public directory, so I still would give it a deprecation period. My comment was just about the message, which currently suggests to continue using the header at the new location while I think it should make clear that people shouldn't be using it in the first place.

> Username: jeking3  
> Created_at: 2017-09-26 18:23:01 UTC  
> Updated_at: 2017-10-09 01:33:25 UTC  
> Url: https://github.com/boostorg/uuid/pull/36#discussion_r141141773  

I found that,  
  
Boost.Compute is including uuid/sha1: ``compute/include/boost/compute/detail/sha1.hpp``  
Boost.Beast has another sha1 implementation: ``beast/include/boost/beast/core/detail/sha1.hpp``  
  
Usually when there is more than one available, it's time to start looking into consolidating.  This is why I asked the question about something like Boost.Hash or Boost.Crypto to hold commonly used things like this.  The pushback was that area usually has a lot of specialized functions and it would be better to leave it out.  So for now I guess we have two SHA1 implementations.  Boost.Compute technically needs one of its own so it does not rely on uuid/detail, or we make a more common library for it.


📁 doc/uuid.html

```diff
 729 |+ </tr>
 730 |+ <tr>
 731 |+ 	<td><tt>void G::get_digest(typename G::digest_type&)</tt></td>
```

> Username: Lastique  
> Created_at: 2017-09-26 08:46:36 UTC  
> Updated_at: 2017-10-09 01:33:25 UTC  
> Url: https://github.com/boostorg/uuid/pull/36#discussion_r140992354  

Replace `&` with `&amp;`.


---

## Comment 2

> Username: Lastique  
> Created_at: 2017-09-26 13:28:08 UTC  
> Url: https://github.com/boostorg/uuid/pull/36#issuecomment-332197819  

On 09/26/17 04:22, James E. King, III wrote:  
> @Lastique I'd appreciate if you could look   
> over my use of Boost.Concept here I'm not sure it's really necessary. I   
> added the definition into the documentation as well and I updated  
  
I didn't use Boost.Concept, so I'm not sure I can comment on the use of it. I do think though that it is better to avoid the overhead from using it, if possible. I wouldn't mind if the concept was defined in a separate header that is not included and not used by default but can be enabled upon user's request. I'm not sure if Boost.Concept offers any tools to that effect.

---

## Comment 3

> Username: jeking3  
> Created_at: 2017-09-26 14:24:49 UTC  
> Url: https://github.com/boostorg/uuid/pull/36#issuecomment-332215394  

@Lastique my guess is I'm not using concept well here, so I will probably remove it since it is documented.  I found all it does is double the compile failures when an implementation is missing something, and the only value-add I could see is forcing const on get_version which is not terribly important.

---

## Comment 4

> Username: jeking3  
> Created_at: 2017-09-26 18:24:34 UTC  
> Url: https://github.com/boostorg/uuid/pull/36#issuecomment-332290546  

I believe I addressed all your concerns from the previous review.

---

## Review 5 [Commented]

> Username: Lastique  
> Created_at: 2017-09-26 18:47:56 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/uuid/pull/36#pullrequestreview-65321738  

📁 include/boost/uuid/detail/md5.hpp

```diff
  94 |+      * The MD5 transformation for all four rounds.
  95 |+      */
  96 |+     #define __MD5_STEP(f, a, b, c, d, x, t, s) \
```

> Username: Lastique  
> Created_at: 2017-09-26 18:47:56 UTC  
> Updated_at: 2017-10-09 01:33:25 UTC  
> Url: https://github.com/boostorg/uuid/pull/36#discussion_r141149129  

Please, rename the macro to follow Boost conventions. No leading underscores, `BOOST_UUID_DETAIL_` prefix in the name to not clash with anything else. Ditto for other macros below.

> Username: jeking3  
> Created_at: 2017-09-26 18:48:32 UTC  
> Updated_at: 2017-10-09 01:33:25 UTC  
> Url: https://github.com/boostorg/uuid/pull/36#discussion_r141149293  

Sure thing.

> Username: jeking3  
> Created_at: 2017-09-26 18:52:53 UTC  
> Updated_at: 2017-10-09 01:33:25 UTC  
> Url: https://github.com/boostorg/uuid/pull/36#discussion_r141150430  

I'll take care of the static private methods in there too.


---

## Comment 6

> Username: jeking3  
> Created_at: 2017-09-26 19:26:54 UTC  
> Url: https://github.com/boostorg/uuid/pull/36#issuecomment-332308816  

Cleaned that up.  I tried turning on ``-Wextra -Werror -pedantic`` but it uncovered a few more things than I wanted to include here, so I'll defer that to another issue.

---

## Comment 7

> Username: jeking3  
> Created_at: 2017-09-26 19:31:29 UTC  
> Url: https://github.com/boostorg/uuid/pull/36#issuecomment-332310041  

Note I still have to use my github account to run the CI builds because they haven't been turned on here yet.

---

## Comment 8

> Username: jeking3  
> Created_at: 2017-09-28 16:25:03 UTC  
> Url: https://github.com/boostorg/uuid/pull/36#issuecomment-332890016  

This one is all set for another round of review.

---

## Review 9 [Commented]

> Username: Lastique  
> Created_at: 2017-09-28 16:36:05 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/uuid/pull/36#pullrequestreview-65922745  

📁 include/boost/uuid/detail/md5.hpp

```diff
  86 |+      * implementation.
  87 |+      */
  88 |+     BOOST_FORCEINLINE MD5_u32plus F(MD5_u32plus x, MD5_u32plus y, MD5_u32plus z) { return ((z) ^ ((x) & ((y) ^ (z)))); }
```

> Username: Lastique  
> Created_at: 2017-09-28 16:36:05 UTC  
> Updated_at: 2017-10-09 01:33:25 UTC  
> Url: https://github.com/boostorg/uuid/pull/36#discussion_r141671088  

I thought you wanted to rename one or two caps letter long functions like this one to avoid clashes with macros, did you not?

> Username: jeking3  
> Created_at: 2017-09-28 18:25:25 UTC  
> Updated_at: 2017-10-09 01:33:25 UTC  
> Url: https://github.com/boostorg/uuid/pull/36#discussion_r141698915  

Sure I can do that too.


---

## Comment 10

> Username: jeking3  
> Created_at: 2017-09-28 18:49:51 UTC  
> Url: https://github.com/boostorg/uuid/pull/36#issuecomment-332929630  

All set for another round of review.  Those weren't macros (F, G, H, H2, I) as I had already converted them to inline functions.  As such, being in the same compilation unit they would take precedence, but I renamed them to use the longer form anyway.

---

## Comment 11

> Username: jeking3  
> Created_at: 2017-10-07 18:56:50 UTC  
> Url: https://github.com/boostorg/uuid/pull/36#issuecomment-334958258  

@Lastique given you previously marked this as "request changes", Id' appreciate it if you could give it one final lookover before I merge it.  Thanks.

---
