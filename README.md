# CMake-VSCode-Tutorial

Template and short tutorial on how to setup an integrated environment for VS Code and CMake for C/C++ programming (with Doxygen and ClangFormat ready to use too!)


## 1. Overview

The following is a CMake C++ Template for use with VS Code. It has been designed around Windows and the MSVC compiler, but would work perfectly with other compilers and on a Linux/Mac installation too (I assume Linux users would know how to e.g. install a compiler and configure their paths correctly). It covers installation for the MSVC and MinGW compilers, extension setup, how to use the template, and some basic CMake fundamentals to help you transition to VS Code and CMake.

The template requires very little CMake knowledge (as I have little myself!) which makes it great for mimicking an IDE environment such as Visual Studio (which I will use as a "baseline" when setting up the template and configuring settings). In fact, this template is specifically designed for those who are moving from a fully-fledged IDE such as Visual Studio, to VS Code (although this obviously isn't a necessity). It may also help those transitioning from more integrated programming languages such as Python.

A couple steps need to be followed in order to get C++, CMake and VS Code working smoothly, however I guarantee you that if you follow the instructions below step-by-step, starting from scratch, you'll end up with an awesome build environment to program in C++ in the great editor that is VS Code. The template of course is also configurable and highly modular (see section 7. Further Enhancements). Nevertheless, I hope that this tutorial can be of help to you, and lets get started!


## 2. Prerequisites

- Install CMake itself from https://cmake.org/, and ensure that its "bin" directory is added to your "Path" environment variable (see https://docs.microsoft.com/en-us/previous-versions/office/developer/sharepoint-2010/ee537574(v=office.14 on how to do so). This may be done directly in the CMake installer.

- Install the "CMake Tools", "CMake" and "CPP-Tools" extensions from the VS Code marketplace. An optional (but highly recommended) extension is also the "Project Templates" extension, available at https://marketplace.visualstudio.com/items?itemName=cantonios.project-templates. This will allow you to easily create new projects from this template, which actually uses placeholder variables for things such as the project name. A clean install for all of these extensions is also recommended.

- Install the compiler of your choice:
    - MSVC: The easiest way to install the MSVC compiler toolchain is to install the latest version of Visual Studio Community Edition. Ticking the "Desktop development with C++" workload in the installer should download everything required. Make sure to install this in the default location, otherwise you should manually add the relevant directories to the Path environment variable.
    - MinGW-w64 (GCC for Windows): Download this compiler from http://winlibs.com/. Ensure to download the latest version (without LLVM). This is because debugging across x86 and x64 versions with the same gdb executable has only recently become supported. You will have to manually add the "bin" directories of this download to your Path environment variable. If you download both the x86 and x64 versions, you should do this for both.


## 3. First-time Setup

1. If you have installed the "Project Templates" extension, clone this repository directly into that extension's "Templates Folder" (should be "AppData\Roaming\Code\User\ProjectTemplates" by default, but can find this directory by opening VS Code and running the command "Project: Open Templates Folder".) If you have not installed the extension, clone it somewhere suitable. Rename the repository as you like (I call mine "CPP-Empty" as I also have e.g. a "CPP-QtWidgets" template). In the .gitignore of this repository, remove the comment for .vscode, and delete the README.md file.

2. Open VS Code inside a new empty folder which will be used for initial testing and setup. Again, if using the Project Templates extension, run the command "Project: Create Project from Template", choose the template, and enter in the variables for your project (i.e. what you would like for ProjectName). This should copy the template over to the current folder but with the project name filled in. If not using this extension, simple manually copy the folder over and find-and-replace these variables such as "#{ProjectName}" to enter the project name. This process will also be how you will setup new projects in the future.

3. I recommend manually editing the names of CMake Tools' "kits" (kits = compiler toolchains). The reason behind this is that the template uses the names of these kits as the names of the subdirectories under the build directory, to allow for multiple toolchains. First, we need to run "CMake: Configure". When the CMake Tools extension asks which kit to use, choose "Scan for kits". If MSVC was installed correctly, or MinGW/bin was added to the Path variable correctly, at least one compiler toolchain or "kit" should be located. Open the command palette, type "Edit User-Local CMake Kits", and hit enter. Now rename the kits to your liking. Note that you can also delete any unnecessary kits, which I have done for MSVC. These can easily be rescanned in later. I recommend the following:
    - MSVC: Delete the kits which have a "toolset" variable different to your own computer's architecture i.e. if you are working on a 64-bit computer, delete both kits with "host=x86". I have named mine to "MSVC-2019-x64" and "MSVC-2019-x86" respectively.
    - MinGW-w64: I have named my kits "MinGW-9.0.0-x64" and "MinGW-9.0.0-x86" respectively.


## 4. Keyboard Shortcuts and Build Commands

- We are using CMake Tools to run the necessary CMake commands that generates our MSVC Solution/MinGW makefile, and VS Code to debug our target. Although CMake Tools does offer debugging functionality, it seems the VS Code debugging works perfectly, is slightly more robust and offers more functionality (e.g. debugging using the integrated terminal). I have therefore removed the CMake Tools shortcuts for both debug and run (i.e. "CMake: Debug" and "CMake: Run Without Debugging"). Some shortcuts I have are:

CMake: Build All Projects ==================== Ctrl + Shift + B

CMake: Build ============================ Ctrl + B

CMake: Cancel Build ======================= Ctrl + Backspace

CMake: Compile Active File ================== Ctrl + F7

CMake: Debug ========================== not used
    
CMake: Run Without Debugging ============== not used
    
Debug: Start Debugging =================== F5
    
Debug: Stop =========================== Shift + F5
    
Project: Create Project from Template =========== Ctrl + Alt + N


## 5. Project Creation Workflow

After mentioning the above, the following is the typical workflow to create a new project with the template extension:
- Create a new folder and name it MyProjectName
- Open the folder in VS Code
- Call the command "Project: Create Project from Template" and enter MyProjectName
- Run the "CMake: Configure" command, selecting the kit you would like to initially use
- Add code to src/main.cpp
- Run with Debug: Start Debugging and have fun!


## 6. Project structure and CMake Basics

- With CMake, each project is essentially managed by a CMakeLists.txt file. This file has been set up to be as beginner-friendly and configurable as possible. It contains two lists of files called "SOURCES" and "HEADERS". In this file, you need to manually add the source files and header files each time they are added to your project, as indicated. Although this may seem like a step backwards from IDEs that do this automatically, it is generally how CMake projects are managed, as they are IDE agnostic. The file has also been setup so that you can add definitions, include paths and compiler options, although I recommend vector-of-bool's videos on "How to CMake Good" on YouTube for anything more complicated.

- Adding CMake depencies can sometimes work, and sometimes not. I have added a section in the CMakeLists.txt file for this at the bottom. The typical way (and how I have set up my environment) is to add an environment variable "CMAKE_PREFIX_PATH" which points to a folder containing all your external libraries. CMake allows you to find these dependencies using a find_package call, which is typically something like find_package(Qt5 COMPONENTS Widgets REQUIRED). You can then also link against these packages once they are found using target_link_libraries(${LOCAL_PROJECT_NAME} ...). This structure differs from library to library. Note that the name of the folder of the library inside CMAKE_PREFIX_PATH matters: CMake will look inside the CMAKE_PREFIX_PATH folder for other directories e.g. starting with "Qt5". Be sure to name your folders appropriately.

- The relevant build files are produced in a seperate "build tree" which can be considered "parallel" to the source tree. This may be different from IDE setups. The template produces sub-directories's for each kit inside the "build" directory. A further two subdirectories for "debug" and "release" are also generated, and then the general CMake build tree follows from there. This includes a folder for each sub-project, and a "bin" directory in each sub-project with the relevant executable. I am not sure if this layout is standard CMake practice or not, but it prevents rebuilding each time you switch configurations, and is what I consider the "Visual Studio" standard.

- Although this setup only contains one project, it is fairly easy to create multiple sub-projects and the equivalent of a "Solution" with multiple projects in Visual Studio. This will simply mean adding sub-folders to CMake using add_subdirectory(). These sub-folders will generally have their own CMakeLists.txt file and source files, and the hierarchy can extend as long as you'd like. Again, I'd recommend watching vector-of-bool's videos further before continuing with something like this.


## 7. Things to keep in mind

It should be noted that, once a project has been created, most of the configurations related to CMake can be made through the toolbar at the bottom of VS Code. This includes whether CMake is building in Debug or Release mode, the current kit being used, the target being built etc.

It should also be noted that, each time a change has been made to a CMakeLists.txt file, CMake should be re-configured. This should be done automatically through the extension when you save the file.

Lastly, the launch.json file contains two different launch configurations: one for MSVC and one for GDB. Be sure the launch configuration you have selected in VS Code matches the kit selection in CMake.


## 8. Doxygen and ClangFormat

This project has been setup to allow for easy creation of documentation files using Doxygen. Although this is a separate tutorial on its own, all one should know is that the "docs" folder contains a file indicating how this documentation should be generated using software Doxygen, which seems to be the industry standard for C++ documentation. This file is referenced in the VS Code settings.json file. This allows for integration with the Doxygen VS Code extension.

A .clang-format file is also provided. This contains my own personal formatting settings for use with an indentation and code formatter called ClangFormat. This formatter is bundled with the VS Code CPP extension and I highly recommend using it. To use ClangFormat instead of the default formatter, simply change "C_Cpp: Formatting" under preferences in VS Code to clangFormat. Also ensure formatting is enabled for a specific action, such as when you save a file.


## 9. Future Enhancements

There are plenty enhancements that can be made to this template. The first thing I would like is the ability for source files to be automatically added to the relevent CMakeLists.txt file when they are added to the project. I'm sure this could be accomplish with a simple VS Code extension. I consider this feature to be standard for IDEs and hope to potentially write an extension for it in the future.

I am sure there are other additions that could be made too, but I believe this template has been refined multiple times and so I hope it has a solid foundation for others to use and build upon. I'm happy for any criticism and suggestions. My email is gvcallen@gmail.com.

## 10. Conclusion

That's it! I hoped you find this useful in some way. As mentioned, if you have any additions, suggestions, or bug-fixes, feel free to message/email me or submit a pull request.

vector-of-bool also has a github.io at https://vector-of-bool.github.io/docs/vscode-cmake-tools/getting_started.html with more help on the CMake Tools extension, which I definitely recommend reading if you get the chance.

Happy coding!
