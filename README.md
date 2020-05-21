
## FAQ
1. On Macosx, the first time you execute ```./PotreeConverter -h```, you might encounter an error message below:
    > terminate called after throwing an instance of ‘std::runtime_error’ what(): locale::facet::_S_create_c_locale name not valid
    
    Add the following system variable to you *.bash_profile* can resolve the problem:
    > export LC_ALL="C"  
    > source .bash_profile 

2. The executable file *PotreeConverter* will be generated in `build/PotreeeConverter/`. It should be copied into `master/PotreeConverter/` for use, because when you convert a las/laz with `-p pageName` parameter, the converter needs to use the files in `master/PotreeConverter/resources` to generate the html file.

## other problems （以下问题是在linux上出现的）
    1： 在编译本项目的时候需要GCC（版本至少是4.9）的支持。如果版本较低请先升级gcc，升级过程参加[linux升级gcc](https://blog.csdn.net/weixin_42090356/article/details/90678158)
    
    2:  在编译的过程中如果出现了 namespace fs = std::filesystem 这样的的错误，请切换到release的PotreeConverter 1.7 PRE版本。
    
    3:  在编译的过程中如果出现了 -std=c++14 这样的错误，请给cmake 命令增加参数  -DCMAKE_CXX_COMPILER=/usr/local/bin/g++ (在安装的gcc的路径中寻找)。
   
    编译lastools请使用 cmake -DCMAKE_BUILD_TYPE=Release .. -DCMAKE_CXX_COMPILER=/usr/local/bin/g++ 

    编译PotreeConverter请使用cmake -DCMAKE_BUILD_TYPE=Release DCMAKE_C_COMPILER=/usr/local/gcc-9.0.1/bin/g++ -DCMAKE_CXX_COMPILER=/usr/local/gcc-9.0.1/bin/g++ -DLASZIP_INCLUDE_DIRS=~/data/potree/LAStools/LASzip/dll -DLASZIP_LIBRARY=~/data/potree/LAStools/LASzip/build/src/liblaszip.so ..
    
    4： 如果出现 write->write(render->getPoint());   class GenericValue{ 这样的错误，请参见[解决办法](https://github.com/potree/PotreeConverter/issues/382)，修改方法如下：
        修改PotreeWriter.cpp文件中的额298行，修改为 Point point = render->getPoint(); write->write(point);