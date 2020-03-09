# install-pytorch
This document describes how to install the pytorch 1.4 on the Windows platform from source.

## Prerequisites
If you are installing from source, prepare the following environments:
 * C++14 compiler. We highly recommend installing an Anaconda environment. For more information, see https://www.anaconda.com/distribution/#download-section.
 * Visual Studio and VC++ 2017.
 * The NVIDIA® Tools Extension SDK(NVTX), as a part of CUDA distributive. For more information, see https://docs.nvidia.com/gameworks/content/gameworkslibrary/nvtx/nvidia_tools_extension_library_nvtx.htm
 * PyTorch for Jetson platform, which is optional. For more instructions, see https://devtalk.nvidia.com/default/topic/1049071/jetson-nano/pytorch-for-jetson-nano/.
 
## Installation
### Install Dependencies
`conda install numpy ninja pyyaml mkl mkl-include setuptools cmake cffi typing`  
Install the `typing` component whose python version is less than 3.5.   

Currently VS 2017, VS 2019 and Ninja are supported as the generator of CMake. If `ninja.exe` is detected in `PATH`, then Ninja will be used as the default generator, otherwise it will use VS 2017.  
If Ninja is selected as the generator, the latest MSVC which is newer than VS 2015 (14.0) will get selected as the underlying toolchain if you have Python > 3.5, otherwise VS 2015 will be selected so you'll have to activate the environment.    
If the version of CMake is 3.14.2 or below and the VS 2019 is installed, even if you specify VS 2017 as the generator, VS 2019 will get selected as the generator.

### Get the Pytorch Source
 1 Download the Pytorch package.  
 `git clone --recursive https://github.com/pytorch/pytorch`  
 2 Optionally, update the existing checkout files.  
 `git submodule sync`  
 `git submodule update --init --recursive`
 
 ### Install the Pytorch
  1 Install Visual Studio. We recommend the version 15.3.3 with the toolset 14.11.  
  
  2 Run the installation once again and check the corresponding checkbox under **Individual components**/**Compilers, build tools, and runtimes** to install Visual C++. We recommend the version 15.4 with the toolset 14.11.  
  If the version of Visual Studio 2017 is lesser than 15.3.3, please update Visual Studio 2017 to the latest version and install VC++ 2017 version 15.4 v14.11 toolset.  
  
  3 Install NVTX onto already installed CUDA by running CUDA installation once again and checking the corresponding checkbox.  
  CUDA and MSVC have strong version dependencies. For more information, see the [CUDA and MSVC Dependency](#cuda-and-msvc-dependency) for a reference.
  
  4 Open the command console.  
  `cmd`  
  
  5 Optionally, add the following commands if you need Python 2.7. Ignore them if you use Python 3.  
  `set MSSdk=1`  
  `set FORCE_PY27_BUILD=1`  
  
  6 Optionally, change Cmake generator to VS 2019 if you want to build with VS 2019 generator.  
  `set CMAKE_GENERATOR=Visual Studio 16 2019`   
  
  7 Optionally, disable Ninja if it is detected.  
  `set USE_NINJA=OFF`  
  
  8 Optionally, if you use Python 3.5, make sure the version of CMake is 3.12 or above when you use the Visual Studio generator and override the underlying toolset used by Ninja and Visual Studio with CUDA.  
  ```
  set CMAKE_GENERATOR_TOOLSET_VERSION=14.11  
  set DISTUTILS_USE_SDK=1  
  for /f "usebackq tokens=*" %i in (`"%ProgramFiles(x86)%\Microsoft Visual Studio\Installer\vswhere.exe" -version [15^,16^) -products * -latest -property installationPath`) do call "%i\VC\Auxiliary\Build\vcvarsall.bat" x64 -vcvars_ver=%CMAKE_GENERATOR_TOOLSET_VERSION%
  ```
  
  9 Optionally, override the cuda host compiler.  
  ```
  set CUDAHOSTCXX=C:\Program Files (x86)\Microsoft Visual Studio\2017\Enterprise\VC\Tools\MSVC\14.11.25503\bin\HostX64\x64\cl.exe
  ```

**Results**  
`python setup.py install`

## CUDA and MSVC Dependency
CUDA and MSVC have strong version dependencies, so even if you use VS 2017 / 2019, you will get build errors like `nvcc fatal : Host compiler targets unsupported OS`.
So install the corresponding VS toolchain in the table below and specify the toolset during activation.
| CUDA version | Newest supported VS version                             |
| ------------ | ------------------------------------------------------- |
| 9.0 / 9.1    | Visual Studio 2017 Update 4 (15.4) (`_MSC_VER` <= 1911) |
| 9.2          | Visual Studio 2017 Update 5 (15.5) (`_MSC_VER` <= 1912) |
| 10.0         | Visual Studio 2017 (15.X) (`_MSC_VER` < 1920)           |
| 10.1         | Visual Studio 2019 (16.X) (`_MSC_VER` < 1930)           |
