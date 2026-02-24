# #397 - Missing formats for --list_content [Open]

> Username: fsimonis  
> Created at: 2023-10-05 10:29:40 UTC  
> Updated at: 2023-10-05 10:29:40 UTC  
> Url: https://github.com/boostorg/test/issues/397  

There are currently 2 formats for `--list_content`:  
  
1. The human-readable format, great visually finding a test  
2. The dot format, great for visualizing the test-suite  
  
Over the years, I found 2 formats to be missing:  
  
### List of test-cases  
  
For simple tools or integration with external test runners like CTest, it would be super useful to have an output listing all testcases in an easy to consume way:  
  
```  
testsuite1/testcase1  
testsuite1/testcase2  
testsuite2/testcase3  
```  
  
Generating this information from the existing formats is rather tedious.  
  
Use cases:  
* Register testcases as separate tests external test runners, such as CTest  
* Writing custom tools that work only on test cases, such as bisection of test cases, running them with GNU parallel, or running them individually in various docker containers.  
* Use fuzzy search to quickly find a testcase and run it `./test --list_content=LIST | fzf | xargs ./test -t`  
  
### Detailed machine-readable output  
  
For advanced tools, a detailed dump of the test tree would be useful.  
JSON would be suitable as it can easily be generated without special libraries.  
  
```json  
{  
  "type": "suite",  
  "name": "testsuite1",  
  "fileName": "...",  
  "fileLine": 21,  
  "labels": [],  
  "children": [  
    {  
      "type": "case",  
      "name": "testcase1",  
      "fileName": "...",  
      "fileLine": 23,  
      "labels": []  
    },  
    {  
      "type": "case",  
      "name": "testcase2",  
      "fileName": "...",  
      "fileLine": 33,  
      "labels": []  
    }  
  ]  
}  
```  
  
The DOT format includes some of this information in a less structured way as most of it is encoded in the label.  
  
Use cases:  
* Custom linters  
* Merging the detailed test setup with external data  
* Intermediate format to generate other custom formats
