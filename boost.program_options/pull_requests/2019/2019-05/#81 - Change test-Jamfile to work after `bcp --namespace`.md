# #81 Change test/Jamfile to work after `bcp --namespace` [Open]

> Username: pdimov  
> Created at: 2019-05-26 16:04:34 UTC  
> Updated at: 2019-05-26 16:04:34 UTC  
> Url: https://github.com/boostorg/program_options/pull/81  

I'm trying to fix `bcp` to work again and adding tests for it (https://github.com/boostorg/bcp/commit/1025e7e04ba1056e4f47f3ca3eb7d3b52c8d612c), and I'm using `program_options` because it's the only library exercising the `boost-lib` rule from `Jamroot`, which was one of the things that were broken.  
  
`bcp --namespace=foo` renames the libraries from `boost_bar` to `foo_bar`, but it doesn't touch `test/Jamfile`, which still wanted to link to `boost_program_options`. Changing it to refer to `/boost//program_options` instead fixes it.  
  
The manual `<define>BOOST_PROGRAM_OPTIONS_DYN_LINK=1` was unnecessary, because it was supplied by `boost-lib`, and it masked the fact that `boost-lib` was erroneously changed by `bcp` to define `FOO_PROGRAM_OPTIONS_DYN_LINK` instead.

---
