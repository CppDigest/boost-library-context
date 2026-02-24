# #154 When specifying --color_output=no, don't output color codes [Closed]

> Username: toonetown  
> Created at: 2018-08-30 19:42:35 UTC  
> Updated at: 2018-09-03 19:56:29 UTC  
> Closed at: 2018-09-03 19:56:29 UTC  
> Url: https://github.com/boostorg/test/pull/154  

As described in https://github.com/boostorg/test/issues/149, specifying `--color_output=no` still will output the color codes, it just outputs the codes for "normal" output.  
  
A better approach would be if the user disables color output completely, the codes should not be included at all.  This PR attempts to do that.

---

## Comment 1

> Username: raffienficiaud  
> Created_at: 2018-09-03 19:56:29 UTC  
> Url: https://github.com/boostorg/test/pull/154#issuecomment-418184978  

Thanks, merged to develop.

---
