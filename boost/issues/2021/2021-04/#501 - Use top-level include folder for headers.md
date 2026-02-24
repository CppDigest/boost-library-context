# #501 - Use top-level include folder for headers [Open]

> Username: Flamefire  
> Created at: 2021-04-10 08:58:23 UTC  
> Updated at: 2021-04-10 22:24:57 UTC  
> Url: https://github.com/boostorg/boost/issues/501  

Currently `b2 headers` puts all headers under `./boost` and adds the root folder to the include path.  
In e.g. https://github.com/boostorg/config/issues/345 I argued, that only the minimum amount of stuff should be in the include path, i.e. the current practice would allow `#include <libs/config/include/boost/config.hpp>` which is odd, but possible so users might do stuff like that which they are not supposed to.  
Even worse: `#include <install>` now works on systems with case-insensitive filesystems.  
  
This might sound like a stretch, but such a case did appear with `VERSION` or `version` files other projects put into their root folder and added that to the include path which breaks any library prepared for the C++20 header `<version>`, see https://github.com/boostorg/config/issues/345 for more details.  
  
--> I propose to put the headers into `include/boost` and add the `include` folder to the include paths.  
  
I think CMakes FindBoost would still find it, because it considers the include folder already for each searched prefix. For other projects we could still provide a top-level `boost` symlink to `include/boost` but don't use that ourselves anymore, or just drop that.

---

## Comment 1

> Username: mclow  
> Created at: 2021-04-10 22:24:57 UTC  
> Url: https://github.com/boostorg/boost/issues/501#issuecomment-817210338  

You should propose this on the mailing list; it will get you much wider visibility.
