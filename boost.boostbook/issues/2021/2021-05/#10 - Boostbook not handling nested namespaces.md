# #10 - Boostbook not handling nested namespaces [Closed]

> Username: eldiener  
> Created at: 2021-05-20 13:27:04 UTC  
> Updated at: 2024-05-13 15:26:03 UTC  
> Closed at: 2024-05-13 15:26:03 UTC  
> Url: https://github.com/boostorg/boostbook/issues/10  

When building doxygen reference documentation with the Quickbook ->  BoostBook -> html path of generating docs in a doc jamfile, the documentation for the top-level namespace entities is correctly generated but the documentation for nested namespace entities is not occurring. The output shows that doxygen is correctly generating the XML elements for the nested namepace but when running xsltproc to transform these elements to BoostBook format the nested namespace XML information is lost, and the final reference documentation, based on doxygen, does not contain the nested namespace entities. If I just run doxygen directly to produce documentation, the nested namespace entities are correctly generated and shown in the doxygen output.  
  
I first posted this as an issue for Boost Build at https://github.com/bfgroup/b2/issues/18, but was told there it is a BoostBook issue. I have attached a file which illustrates this issue for an "example" Boost library.  
[example.zip](https://github.com/boostorg/boostbook/files/6516236/example.zip)

---

## Comment 1

> Username: eldiener  
> Created at: 2021-05-25 16:13:11 UTC  
> Url: https://github.com/boostorg/boostbook/issues/10#issuecomment-848011765  

Any chance someone will look at this issue, or is this just beyond a current maintainer's ability to fix this bug ?

---

## Comment 2

> Username: Lastique  
> Created at: 2021-05-26 19:43:38 UTC  
> Url: https://github.com/boostorg/boostbook/issues/10#issuecomment-849068097  

This seems to be an intended feature controlled by the [`boost.doxygen.detailns`](https://github.com/boostorg/boostbook/blob/aa3d1d676ce2d921dc820bbea9ed38a39a279402/xsl/doxygen/doxygen2boostbook.xsl#L31) parameter. You need to pass it to the `doxygen` rule in the Jamfile, (e.g. `<xsl:param>boost.doxygen.detailns=aux`). The parameter denotes the namespace with implementation details and is `detail` by default.

---

## Comment 3

> Username: Lastique  
> Created at: 2024-05-13 15:26:03 UTC  
> Url: https://github.com/boostorg/boostbook/issues/10#issuecomment-2107993834  

Closing, as answered above.
