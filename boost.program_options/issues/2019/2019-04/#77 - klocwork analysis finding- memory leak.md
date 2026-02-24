# #77 - klocwork analysis finding: memory leak [Open]

> Username: mingxiaoh  
> Created at: 2019-04-03 08:21:09 UTC  
> Updated at: 2019-04-03 13:29:36 UTC  
> Url: https://github.com/boostorg/program_options/issues/77  

Problem Description:  
 44 int parse_cmd_options(int argc, char **argv) {  
 45   po::options_description desc{"Options"};  
 46   desc.add_options()  
 47     ("help", "Convolution Options:")  
 48     ("mb,n", po::value<int>(&mb), "Batch size")  
  
klocwork report: Memory leak. Dynamic memory stored in 'po::value<int> ( &sw)' allocated through function 'value<int>' at line 48 is lost at line 46   
  
The boost version we used is 1.69  
Any comments for this issue?
