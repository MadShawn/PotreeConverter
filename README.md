# Potree Converter

Master: [![Build Status](https://travis-ci.org/potree/PotreeConverter.svg?branch=master)](https://travis-ci.org/potree/PotreeConverter)
Develop: [![Build Status](https://travis-ci.org/potree/PotreeConverter.svg?branch=develop)](https://travis-ci.org/potree/PotreeConverter)

Builds a potree octree from las, laz, binary ply, xyz or ptx files.

## Downloads

* [Source Code and windows 64bit releases](https://github.com/potree/PotreeConverter/releases)

## Dependencies

* [lastools(LASzip)](https://github.com/LAStools/LAStools) or [fork of lastools with cmake for LASzip](https://github.com/m-schuetz/LAStools)

## Build

### linux / gcc 4.9


lastools (from fork with cmake)

```
cd ~/dev/workspaces/lastools
git clone https://github.com/m-schuetz/LAStools.git master
cd master/LASzip
mkdir build
cd build
cmake -DCMAKE_BUILD_TYPE=Release ..
make

```

PotreeConverter

```
cd ~/dev/workspaces/PotreeConverter
git clone https://github.com/potree/PotreeConverter.git master
cd master
mkdir build
cd build
cmake -DCMAKE_BUILD_TYPE=Release -DLASZIP_INCLUDE_DIRS=~/dev/workspaces/lastools/master/LASzip/dll -DLASZIP_LIBRARY=~/dev/workspaces/lastools/master/LASzip/build/src/liblaszip.so ..
make

# copy ./PotreeConverter/resources/page_template to your binary working directory.

```

### OS X

Same as the linux instructions above, except:

1. Give cmake absolute paths to the LASzip tools you just built. (Otherwise make might not be able to find them)
2. LASZip library will be called `liblaszip.dylib`, not `liblaszip.so `
3. C++ compiler should be set to gcc implicitly instead of clang

```
...

cmake -DCMAKE_BUILD_TYPE=Release 
    -DLASZIP_INCLUDE_DIRS=[ABSOLUTE_PATH_TO_LASTOOLS]/master/LASzip/dll 
    -DLASZIP_LIBRARY=[ABSOLUTE_PATH_TO_LASTOOLS]/master/LASzip/build/src/liblaszip.dylib 
    -DCMAKE_C_COMPILER=/usr/local/Cellar/gcc/[version 6.0+]/bin/gcc-6 
    -DCMAKE_CXX_COMPILER=/usr/local/Cellar/gcc/[version 6.0+]/bin/g++-6 
    ..
make

```

### Windows / Microsoft Visual Studio 2019:

Use the cmd command line. Power Shell might now work.

lastools

```
cd D:/dev/workspaces/lastools/
git clone https://github.com/m-schuetz/LAStools.git master
cd master/LASzip
mkdir build
cd build
cmake ../
```

PotreeConverter

```
# make sure you've got these environment variables set with your directory structure
set LASZIP_INCLUDE_DIRS=C:\dev\workspaces\lastools\master\LASzip\dll
set LASZIP_LIBRARY=C:\dev\workspaces\lastools\master\LASzip\build\src\Release\laszip.lib

# checkout PotreeConverter
cd D:/dev/workspaces/PotreeConverter
git clone https://github.com/potree/PotreeConverter.git master
cd master
mkdir build
cd build

# Visual Studio project
cmake -DLASZIP_INCLUDE_DIRS=%LASZIP_INCLUDE_DIRS% -DLASZIP_LIBRARY=%LASZIP_LIBRARY%  ../

# copy ./PotreeConverter/resources/page_template to your binary working directory.

```

## PotreeConverter Usage

Converts las files to the potree file format.
You can list multiple input files. If a directory is specified, all files
inside the directory will be converted.

Options:


```
$ PotreeConverter -h                                      
  -i [ --source ]                        input files
  -h [ --help ]                          prints usage
  -p [ --generate-page ]                 Generates a ready to use web page with the given name.
  -o [ --outdir ]                        output directory
  -s [ --spacing ]                       Distance between points at root level. Distance halves each level.
  -d [ --spacing-by-diagonal-fraction ]  Maximum number of points on the diagonal in the first level (sets spacing). spacing = diagonal value
  -l [ --levels ]                        Number of levels that will be generated. 0: only root, 1: root and its children, ...
  -f [ --input-format ]                  Input format. xyz: cartesian coordinates as floats, rgb: colors as numbers, i: intensity as number
  --color-range
  --intensity-range
  --output-format                        Output format can be BINARY, LAS or LAZ. Default is BINARY
  -a [ --output-attributes ]             can be any combination of RGB, INTENSITY and CLASSIFICATION. Default is RGB.
  --scale                                Scale of the X, Y, Z coordinate in LAS and LAZ files.
  --aabb                                 Bounding cube as "minX minY minZ maxX maxY maxZ". If not provided it is automatically computed
  --incremental                          Add new points to existing conversion
  --overwrite                            Replace existing conversion at target directory
  --source-listing-only                  Create a sources.json but no octree.
  --projection                           Specify projection in proj4 format.
  --list-of-files                        A text file containing a list of files to be converted.
  --source                               Source file. Can be LAS, LAZ, PTX or PLY
  --title                                Page title
  --description                          Description to be shown in the page.
  --edl-enabled                          Enable Eye-Dome-Lighting.
  --show-skybox
  --material                             RGB, ELEVATION, INTENSITY, INTENSITY_GRADIENT, RETURN_NUMBER, SOURCE, LEVEL_OF_DETAIL
```

Examples:

    # convert data.las and generate web page.
    ./PotreeConverter.exe C:/data.las -o C:/potree_converted -p pageName

    # generate compressed LAZ files instead of the default BIN format.
    ./PotreeConverter.exe C:/data.las -o C:/potree_converted --output-format LAZ

    # convert all files inside the data directory
    ./PotreeConverter.exe C:/data -o C:/potree_converted

    # first, convert with custom bounding box and then append new_data.las afterwards.
    # points in new_data MUST fit into bounding box!
    ./PotreeConverter.exe C:/data -o C:/potree_converted -aabb "-0.748 -2.780 2.547 3.899 1.867 7.195"
    ./PotreeConverter.exe C:/new_data.las -o C:/potree_converted --incremental

	# tell the converter that coordinates are in a UTM zone 10N projection. Also, create output in LAZ format
	./PotreeConverter.exe C:/data -o C:/potree_converted -p pageName --projection "+proj=utm +zone=10 +ellps=GRS80 +datum=NAD83 +units=m +no_defs" --overwrite --output-format LAZ

	# using a swiss projection. Use http://spatialreference.org/ to find projections in proj4 format
	./PotreeConverter.exe C:/data -o C:/potree_converted -p pageName --projection "+proj=somerc +lat_0=46.95240555555556 +lon_0=7.439583333333333 +k_0=1 +x_0=600000 +y_0=200000 +ellps=bessel +towgs84=674.4,15.1,405.3,0,0,0,0 +units=m +no_defs" --overwrite


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
