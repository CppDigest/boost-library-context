# #91 Update deprecated uses of boost/bind.hpp, replace standard function wrappers with boost::bind [Open]

> Username: Lastique  
> Created at: 2020-03-03 22:31:44 UTC  
> Updated at: 2024-04-07 21:02:55 UTC  
> Url: https://github.com/boostorg/program_options/pull/91  

`boost/bind.hpp` generates deprecation warnings about global placeholder argument keywords. This PR updates to `boost/bind/bind.hpp` and namespaced keywords.  
  
Also, this PR replaces standard function object wrappers, which were removed from C++20, with `boost::bind`.  
  
Additionally, in a separate commit, this PR trims trailing spaces in the C++ source files. You can see the diff of the first commit to view the effective changes without whitespace changes.

---

## Review 1 [Commented]

> Username: pdimov  
> Created_at: 2024-04-07 16:28:01 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/program_options/pull/91#pullrequestreview-1985126560  

📁 src/cmdline.cpp

```diff
 230 |             style_parsers.push_back(
 231 |-                 boost::bind(&cmdline::handle_additional_parser, this, _1));
 231 |+                 boost::bind(&cmdline::handle_additional_parser, this, boost::placeholders::_1));
```

> Username: pdimov  
> Created_at: 2024-04-07 16:28:01 UTC  
> Url: https://github.com/boostorg/program_options/pull/91#discussion_r1555006204  

Why not `using namespace boost::placeholders;` here as well?

> Username: Lastique  
> Created_at: 2024-04-07 16:37:18 UTC  
> Url: https://github.com/boostorg/program_options/pull/91#discussion_r1555007551  

There's just one placeholder, so qualifying the namespace doesn't take much space. I can change it to a `using` directive, if you like.

> Username: Lastique  
> Created_at: 2024-04-07 17:00:52 UTC  
> Url: https://github.com/boostorg/program_options/pull/91#discussion_r1555011242  

Actually, those qualifications are no longer needed. I've removed them.

> Username: pdimov  
> Created_at: 2024-04-07 17:56:52 UTC  
> Url: https://github.com/boostorg/program_options/pull/91#discussion_r1555019976  

I don't understand; why are they not needed?

> Username: Lastique  
> Created_at: 2024-04-07 21:02:55 UTC  
> Url: https://github.com/boostorg/program_options/pull/91#discussion_r1555086196  

There was https://github.com/boostorg/program_options/pull/92 that was merged. It added `using namespace boost::placeholders;` directives at the top of the files, which made explicit namespace qualifications in this PR no longer needed.  
  
This PR is still useful as it removes `#include <boost/bind.hpp>` in one place and removes the usage of `std::bind1st` in another.


---
