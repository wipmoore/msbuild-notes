# Msbuild product structure 

Is a build tool for managed and native C++ code.


## Toolsets

A toolset is a set of tasks, targets, and tools ( e.g. c# compiler, etc ). Most toolsets can be used to compile against more than one version of the .Net framework and more than one system platform.

Supported toolsets in msbuild 16 ( visual studio 2019 ):

    - 2
    - 3.5
    - 4
    - Current

By default the toolsets are located in the MSBuild\16\(bin|Enterprise) folder.  In Visual Studio 2019 the default toolset is current regardless of the ToolsVersion property in the project definition file.  The /toolsversion command line options can be used to override this behaviour.


## References

- https://docs.microsoft.com/en-us/visualstudio/msbuild/msbuild?view=vs-2019
