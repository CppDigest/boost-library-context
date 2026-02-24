# #122 - Linking to bcrypt creates problems (i.e. for CMake users) [Closed]

> Username: pdimov  
> Created at: 2021-11-02 17:52:26 UTC  
> Updated at: 2022-02-04 20:23:45 UTC  
> Closed at: 2022-02-04 20:09:03 UTC  
> Url: https://github.com/boostorg/uuid/issues/122  

As reported in https://github.com/boostorg/boost_install/issues/54, using UUID from a CMake project requires one to manually link to bcrypt on Windows. That's because the CMake config files define `BOOST_ALL_NO_LIB`, which disables autolinking not just to Boost libraries (which is the intent), but to bcrypt.lib as well.  
  
In addition, since UUID is a header-only library, it has no CMake target to which code could link in order to inherit the dependency to bcrypt.  
  
If we're intending to keep linking to bcrypt, one option here is to create a dummy compiled library, whose only purpose would be to expose a Boost::uuid target in CMake, which would then link to bcrypt.

---

## Comment 1

> Username: Lastique  
> Created at: 2021-11-02 21:38:21 UTC  
> Url: https://github.com/boostorg/uuid/issues/122#issuecomment-958186895  

I'm not the maintainer, but linking with bcrypt is essential. The alternative is wincrypt, but it is deprecated and I don't think is universally available.  
  
I also don't think introducing a dummy compiled library is an adequate solution, as this creates a bunch of other problems. For example, it affects packaging and downstream dependencies for no good reason.  
  
IMHO, this should be solved entirely in CMake and headers. One option is stop defining `BOOST_ALL_NO_LIB`. Another is ignore it for linking non-Boost libraries.

---

## Comment 2

> Username: pdimov  
> Created at: 2021-11-02 22:37:56 UTC  
> Url: https://github.com/boostorg/uuid/issues/122#issuecomment-958304651  

Relying on autolink to hide the problem doesn't seem correct; it doesn't work for GCC. But assuming we don't care about GCC, we've had the discussion of whether BOOST_ALL_NO_LIB should apply to non-Boost libraries more than once now, without any results.  
  
I wouldn't be in support of enabling autolink for Boost libraries when using the CMake config files; this causes nothing but trouble (even if it does help to hide errors occasionally.)

---

## Comment 3

> Username: Lastique  
> Created at: 2021-11-02 22:58:51 UTC  
> Url: https://github.com/boostorg/uuid/issues/122#issuecomment-958346443  

Yes, enabling auto-link is just a workaround, something we could consider for 1.78. The proper solution is to find a way to generate CMake config files for header-only libraries while keeping them header-only.

---

## Comment 4

> Username: Leon0402  
> Created at: 2022-01-09 16:28:00 UTC  
> Url: https://github.com/boostorg/uuid/issues/122#issuecomment-1008330813  

> he proper solution is to find a way to generate CMake config files for header-only libraries while keeping them header-only  
  
I would love to see this, so I can use Boost::uuid and others to correctly link all targets. In case of other libraries like Boost libraries, I also need to link other boost libraries explicitly like filesystem, which is confusing.   
  
Should this maybe tracked in a separate issue? Perhaps in boost_install?

---

## Comment 5

> Username: jeking3  
> Created at: 2022-01-25 21:47:50 UTC  
> Url: https://github.com/boostorg/uuid/issues/122#issuecomment-1021640403  

I'm guessing someone has a workaround for this, for now - it might be useful to share that to help others in the same situation.

---

## Comment 6

> Username: pdimov  
> Created at: 2022-01-25 23:55:43 UTC  
> Url: https://github.com/boostorg/uuid/issues/122#issuecomment-1021719483  

I changed the CMake config files to only define the corresponding library-specific _NO_LIB macro instead of `BOOST_ALL_NO_LIB`, which takes care of this and similar issues on compilers that support autolinking. (https://github.com/boostorg/boost_install/commit/258f7866c1cb1e4d1b3aebd0c4db594d373c686c)

---

## Comment 7

> Username: jeking3  
> Created at: 2022-02-04 20:06:07 UTC  
> Url: https://github.com/boostorg/uuid/issues/122#issuecomment-1030311321  

@pdimov is there anything that needs to be done here as a result of that?

---

## Comment 8

> Username: pdimov  
> Created at: 2022-02-04 20:09:03 UTC  
> Url: https://github.com/boostorg/uuid/issues/122#issuecomment-1030313309  

I don't think we can do anything in practice, short of switching to a compiled library, which is unacceptable for many. So, closing.

---

## Comment 9

> Username: jeking3  
> Created at: 2022-02-04 20:23:45 UTC  
> Url: https://github.com/boostorg/uuid/issues/122#issuecomment-1030322859  

Thanks, worked hard to keep this header-only.
