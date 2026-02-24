# #65 - Linker/visibility warnings on macOS for boost log 1.68.0 [Closed]

> Username: ghost  
> Created at: 2018-11-30 02:05:05 UTC  
> Updated at: 2018-11-30 20:13:47 UTC  
> Closed at: 2018-11-30 08:18:29 UTC  
> Url: https://github.com/boostorg/log/issues/65  

I'm trying to compile boost 1.68.0 from source for a project on macOS and I'm getting a [pile of linker warnings](https://pastebin.com/DgtpYHdN). The warnings go away when I compile my project with `-fvisibility=hidden` (as expected) however, my project is multi-module and the library dependent on boost must itself be visible to a few other libraries and executables.  
  
What's going on here? Is there a way to address these errors?  
  
Here's my configure and build commands:  
  
```  
./bootstrap.sh  
      --prefix=some/prefix/in/my/build/folder  
      --with-libraries=filesystem  
      --with-libraries=locale  
      --with-libraries=log  
      --with-libraries=system  
      --with-libraries=thread  
  
./b2 install  
      cxxstd=17 # because boost_system barfs when trying to link cxx17 code  
      link=shared # I've also tried static  
      variant=release  
      threading=multi  
      runtime-link=shared # I even tried static here just to see what would happen  
```  
  
And I'm linking these libraries:  
`boost_system, boost_thread, boost_filesystem, boost_locale, boost_log_setup, boost_log`

---

## Comment 1

> Username: Lastique  
> Created at: 2018-11-30 08:18:29 UTC  
> Url: https://github.com/boostorg/log/issues/65#issuecomment-443126509  

Boost.Log is always built with hidden visibility. As far as I can tell, the warning says that Boost.Log code uses direct access to its symbols, so they won't be overridden at run time, which is the intention.  
  
My advice would be to use hidden visibility everywhere, including your modules. Mark the symbols you need to export explicitly, with default visibility attributes or pragmas.

---

## Comment 2

> Username: ghost  
> Created at: 2018-11-30 17:55:49 UTC  
> Updated at: 2018-11-30 18:14:08 UTC  
> Url: https://github.com/boostorg/log/issues/65#issuecomment-443285721  

Is there any particular reason I _don't_ need to do this with a package manager-installed binary of Boost.Log? I only started seeing this when I started compiling Boost.Log myself.

---

## Comment 3

> Username: Lastique  
> Created at: 2018-11-30 19:00:47 UTC  
> Url: https://github.com/boostorg/log/issues/65#issuecomment-443304963  

Sorry, I have no idea. I don't use Mac OS and I don't know how the packages are built there.  
  
The first Boost.Log release which had hidden visibility enabled was 1.59, so if your system packages are older, they were likely compiled with default visibility.

---

## Comment 4

> Username: Lastique  
> Created at: 2018-11-30 19:18:44 UTC  
> Url: https://github.com/boostorg/log/issues/65#issuecomment-443310033  

Also, you might want to ensure that you're not linking with shared library when you build against the system libraries. In that case you probably won't get the warnings.

---

## Comment 5

> Username: ghost  
> Created at: 2018-11-30 20:13:46 UTC  
> Url: https://github.com/boostorg/log/issues/65#issuecomment-443325874  

I was using 1.66.0 previously installed through homebrew, so I don't know why that's different given what you've said. I think I've got enough info to sort out how my project needs to build given the visibility requirements, though, and thanks 👍
