# #225 - Build was Killed during compilation on intel2017.1 compiler [Open]

> Username: AndreyShtyrov  
> Created at: 2018-12-06 11:20:48 UTC  
> Updated at: 2018-12-06 11:20:48 UTC  
> Url: https://github.com/boostorg/boost/issues/225  

I hace excuted this command in order to compile on intel compiler  
compiler enviroment had been added  
from <boost root>/tools/build  
./bootstrap.sh  
./b2 install --prefix=$HOME/local/BOOST  
export PATH=$HOME/local/BOOST/bin:$PATH  
cd ../../  
b2 address-model=64 toolset=intel stage  
  
Error message  
remark #11074: Inlining inhibited by limit max-size  
remark #11074: Inlining inhibited by limit max-total-size  
remark #11076: To get full report use -opt-report=3 -opt-report-phase ipo  
Killed
