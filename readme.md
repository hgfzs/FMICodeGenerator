# About the FMI Code Generator

## What is this FMI/FMU stuff?

FMU stands for _Functional Mockup Unit_ and FMI for _Functional Mockup Interface_. The latter is an industry standard
for simulation model runtime coupling, and basically defines an API and a meta data description file and directory structure for model exchange: see [FMI-Standard webpage](https://fmi-standard.org) for details.

## The Motivation

When you want an efficient FMU slave, there's probably no way around a native C/C++ implementation. However, implementing the C interface functions, the data handling, input/output variable handling and advanced features like **saving and restoring the FMU state** is not so simple and straight-forward.

However, the process of setting up the FMU (core files, `modelDescription.xml`, directory structure) is pretty similar for most projects and can be automated with a configurable code generator - hence this project.

### What FMI Code Generator *is not*

This is not a Software Development Library/Toolkit for accessing/supporting the FMI interface. Look at  the [FMU SDK](https://www.qtronic.de/de/fmusdk.html) if you need something like that.

Actually, the code generated by **FMI Code Generator** hides most of the messy FMI C function interface (including most of the memory management related to instantiating/deleting slave instances, and the state storage stuff) from the typical (engineering) user. Also, the generated code is pretty small, easy to read and comprehend and works cross-platform without any library setup issues.

## The (anticipated) use of the FMI Code Generator

### Step 1: Create a fully working FMU source code template

Creating the barebone of an FMU should be as simple as that:

1. clone this repository
2. run the Python program `scripts/main.py` or `scripts/FMIGeneratorWizard.py`
3. give the relevant information (model name, list of input and output vars, parameters, integration states etc.)

Once the generator has finished, you have a directory structure with fully working FMU source code (including build system files) with matching `modelDescription.xml` that you can build cross-platform with the provided CMake-based build system.

### Step 2: Develop FMU-specific functionality

The generated directory structure contains build system files for CMake and Qt-qmake. With CMake, you can easily generate makefiles for various compilers and development environments. With the pro-files you can directly start developing with Qt Creator (even though the FMU code itself is plain C/C++ code without Qt dependencies).

You can now start to implement your FMU-specific logic (physics, mathematical functions) by opening the `<FmuModelName>.cpp` file within the `<FmuModelName>/src/` subdirectory. The places where your own code is usually placed are marked with **TODO** comments.

Test-compile the source code using the provided build system files.

### Step 3: Generate the FMU and deploy

The template directory structure contains a deployment script/batch file (either `<FmuModelName>/build/deploy.sh` or `<FmuModelName>/build/deploy.bat`).

You may want to adjust the `deploy.sh` script to add copying of own resource files, if needed.

Deployment works as follows (for Linux/Unix/Mac):

```bash
# change into generated directory structure
cd <FmuModelName>/build
# build the FMU in release mode
./build.sh release
# deploy the FMU, e.g. package the FMU in the zipped directory structure
./deploy.sh
```

## System requirements / setup

The script runs with Python 2.7 and 3.x.

#### Linux

Simply install the python packages and pyqt5.

_Ubuntu 16.04 - Python 2.7_

```bash
> sudo apt get install cmake built-essential qt5-default qt5-qmake qtcreator python-pyqt5 pyqt5-dev-tools 
```
The package `pyqt5-dev-tools` contains the scripts `pyuic5` and `pyrcc5` needed for development of the FMIGenerator itself.

_Ubuntu 16.04 - Python 3_

```bash
> sudo apt get install cmake built-essential qt5-default qt5-qmake qtcreator python3 python3-pyqt5 pyqt5-dev-tools 
```

#### Mac

Use homebrew and/or macports to install python and pyqt5 (or alternatively pip).

#### Windows

Things are bit more complicated for Windows. While the code can be compiled (thanks to the CMake build system) using quite a few build chains available on windows, the `batch`-scripts are currently expecting a standard Visual Studio 2015 installation (r`c:\Program Files (x86)\Microsoft Visual Studio 14.0\VC\vcvarsall.bat`) and 7zip must be installed with `7za` in the PATH. Also, `cmake` must be in in the PATH.

If your setup differs from that, edit the files `build_VC_x64.bat` and `deploy.bat` in directory `data/FMI_template/build`.

# Developer Information

## Directory Structure of the FMICodeGenerator repository

    bin                 - batch/shell scripts to simplify/automate FMU generation
    data                - resources and template files
    doc                 - documentation, also includes examples
    examples            - example directory structures (this is what the FMI generator should produce)
    scripts             - the actual python scripts
    scripts/third_party - external library and scripts
    third_party         - external tools like the compliance checker
