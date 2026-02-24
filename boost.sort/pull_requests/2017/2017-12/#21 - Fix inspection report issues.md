# #21 Fix inspection report issues [Merged]

> Username: zerotypos-found  
> Created at: 2017-12-05 03:11:31 UTC  
> Updated at: 2018-01-22 14:16:43 UTC  
> Merged at: 2017-12-08 03:46:30 UTC  
> Closed at: 2017-12-08 03:46:30 UTC  
> Url: https://github.com/boostorg/sort/pull/21  



---

## Comment 1

> Username: zerotypos-found  
> Created_at: 2017-12-06 14:26:49 UTC  
> Url: https://github.com/boostorg/sort/pull/21#issuecomment-349654797  

This PR fixes a few issues reported in the Boost Inspection Report:  
- Files with non-ASCII characters.  
- Files with tabs.  
  
FYI, you can see the inspection report by  
  
- Go to http://www.boost.org/  
- Click "Development" at the right menu bar  
- Click "Testing" at the right menu bar  
- Then, there is "Inspection Reports".  
  
However, the [above report](http://boost.cowic.de/rc/docs-inspect-develop.html) is not updated frequently and is a bit outdated.  
  
To get the latest inspection report, you can make the report by yourself using the [inspect tool](http://www.boost.org/tools/inspect/index.html):  
  
- Run `b2` at `BOOST_ROOT/tools/inspect`.  
- Then, the `inspect` tool is built (and placed somewhere).  
- Run `inspect` and pass it the path to boost root's directory as a command-line argument (e.g. `./inspect "$BOOST_ROOT" > inspect.html`).

---
