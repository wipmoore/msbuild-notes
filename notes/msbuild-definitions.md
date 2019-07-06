- Targets
- Properties
- Items
- Tasks
- Invocation
- Conditionals
- Dependencies
- Imports
- Evaluation order


## Targets

A target is an ordered group of tasks, where a task is a specific action that you want to apply.  The order in which a target is evaluated can be defined in multiple ways but a target will only be evaluated once.  Targets with inputs and outputs can be defined to build incrementally so that their they are only evaluated if the output does not exist of if the input is newer than than the expected output.


A target can be defined within an msbuild definition file, they must have a name property:

```
<Project xmlns="https://schemas.microsoft.com/developers/msbuild/2003">

    <Target Name="HelloWorld">
        <Message Text="Hello World 1' />
        <Message Text="Hello World 2' />
    </Target>

</Project>
```

Targets can be defined in a separate file and imported into the project:

```

-- Custom.targets

<Project xmlns="http://schemas.microsoft.com/developer/msbuild/2003">

    <Target Name="CustomTarget">
        <Message Text="Custom Target" />
    </Target>
</Project>


-- main.proj

<Project xmlns="http://schemas.microsoft.com/developer/msbuild/2003">

    <Import Project=".\Custom.targets" />

    <Target Name="SecondTarget">
        <Message Text="Second Target" />
    </Target>

    <Target Name="FirstTarget">
        <Message Text="First Target" />
    </Target>

</Project>


```

Conditions can be defined on a target:

```
<Project xmlns="http://schemas.microsoft.com/developer/msbuild/2003">

    <PropertyGroup>
        <DoFirst>false</DoFirst>
    </PropertyGroup>


    <Target
        Name="First"
        Condition="'$(DoFirst)' == 'true'" >

        <Message Text="First Target" />
    </Target>

</Project>

```


When you run msbuild without specifying the target it will run the first target defined in the project.

```
<Project xmlns="http://schemas.microsoft.com/developer/msbuild/2003">

    <Target Name="SecondTarget">
        <Message Text="Second Target" />
    </Target>

    <Target Name="FirstTarget">
        <Message Text="First Target" />
    </Target>

</Project>

-- SecondTarget will be run

```

```
<Project xmlns="http://schemas.microsoft.com/developer/msbuild/2003">

    <Import Project=".\Custom.targets" />

    <Target Name="SecondTarget">
        <Message Text="Second Target" />
    </Target>

    <Target Name="FirstTarget">
        <Message Text="First Target" />
    </Target>

</Project>


-- The first target in Custom.targets will be called

```

The DefaultTargets project attribute can be used to define the targets to run of none have been set on the command line:

``` 
<Project 
    xmlns="http://schemas.microsoft.com/developer/msbuild/2003"
    DefaultTargets="Second" >

    <Target Name="First" >
        <Message Text="First Target" />
    </Target>

    <Target Name="Second" >
        <Message Text="Second Target" />
    </Target>

</Project>

```

Targets can be extended by creating a new target and tagging it with a BeforeTargets or AfterTargets attribute.  This technique is intended to be used when you want to extend an existing target that you do not have access to its definition or in a way that is scoped:

```

<Project xmlns="http://schemas.microsoft.com/developer/msbuild/2003">

    <Target
        Name="BeforeMain"
        BeforeTargets="Main" >
        <Message Text="Before Main" />
    </Target>

    <Target
        Name="AfterMain"
        AfterTargets="Main" >
        <Message Text="After Main" />
    </Target>

    <Target Name="Main">
        <Message Text="Main Target" />
    </Target>

</Project>


msbuild /t:Main 

# This will result in BeforeMain -> Main -> AfterMain

```

You can specify that a Target depends on other targets, which will result in the dependencies being evaluated if they have not all ready been at the time the dependent target is supposed to be evaluated.  The dependencies will only be evaluated if the target passes any evaluation conditions that have been applied to it:

```

<Project xmlns="http://schemas.microsoft.com/developer/msbuild/2003">

    <Target
        Name="First"
        DependsOnTargets="Second" >

        <Message Text="First Target" />
    </Target>

    <Target
        Name="Second" >

        <Message Text="Second Target" />
    </Target>

</Project>

```

Support for Incremental builds is provided so that targets that have outputfiles that are up to date with respect to their corresponding input files are not executed.

```

<Project xmlns="http://schemas.microsoft.com/developer/msbuild/2003">

    <ItemGroup>
        <BackupSourceFiles Include="*.txt"/>
    </ItemGroup>

    <Target 
        Name="Backup"
        Inputs="@(BackupSourceFiles)"
        Outputs="@(BackupSourceFiles->'%(Filename)%(Extension).bak'">

        <Copy 
            SourceFiles="@(BackupSourceFiles)" 
            DestinationFiles="@(BackupSourceFiles->'%(Filename)%(Extension).bak')" />

    </Target>

</Project>


# Only files that have a backup file with an older date than the source will be backed up

```

Msbuild has some default build target files, they can be found in the $(MSBuildToolsPath).

```

<Project xmlns="http://schemas.microsoft.com/developer/msbuild/2003">


    <Target Name="Main">
        <Message Text="Tools Path : $(MSBuildToolsPath)" />
    </Target>

</Project>

```

Msbuild has many built in tasks that can be included in targets, documentation on these can be found at:

 - https://docs.microsoft.com/en-us/visualstudio/msbuild/msbuild-task-reference?view=vs-2019 

### References

 - https://docs.microsoft.com/en-us/visualstudio/msbuild/target-element-msbuild?view=vs-2019


## Properties

Are a named value defined within a property group within an msbuild project. They are reference by wrapping the property name in $().  

```
<Project xmlns="https://schemas.microsoft.com/developer/msbuild/2003">

    ...

    <PropertyGroup>
        <Optimization>false</Optimization>
    </PropertyGroup>

    ...

</Project>
```

The value of a property can be updated within a project using conditional statements:

```
<Project xmlns="http://schemas.microsoft.com/developer/msbuild/2003">

    <PropertyGroup>
        <TargetFrameworkVersion>v4.7</TargetFrameworkVersion>
        <DefaultFrameworkVersion>v4.7</DefaultFrameworkVersion>
        <DefaultFrameworkUsed>true</DefaultFrameworkUsed>
    </PropertyGroup>


    <PropertyGroup Condition="'$(TargetFrameworkVersion)' != '$(DefaultFrameworkVersion)'">
        <DefaultFrameworkUsed>false</DefaultFrameworkUsed>
    </PropertyGroup>


    <Target Name="FrameworkDetails">
        <Message Text="$(TargetFrameworkVersion)" />
        <Message Text="$(DefaultFrameworkUsed)" />
    </Target>

</Project>
```

Properties can be set via the command line using a switch.  If set this way the command line value will be the value used for the property regardless of what is set in the build definition.

```
msbuild [myscript] /p:build_configurations="test5;test6;test7"
```

### References

 - https://docs.microsoft.com/en-us/visualstudio/msbuild/msbuild-properties?view=vs-2019


## References

- http://www.cazzulino.com/building-like-a-pro-primer.html
