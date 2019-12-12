# Analyzers for Unity

![Build status on Windows](https://github.com/microsoft/Microsoft.Unity.Analyzers/workflows/CI-Windows/badge.svg)
![Build status on macOS](https://github.com/microsoft/Microsoft.Unity.Analyzers/workflows/CI-macOS/badge.svg)

This project is intended to provide to Visual Studio a better understanding of Unity projects by adding new Unity-specific diagnostics or by removing general C# diagnostics that do not apply to Unity projects. 

Check out the [list of analyzers and suppressors](doc/index.md) defined in this project.

If you have an idea for a best practice for Unity developers to follow, please open an [issue](https://github.com/microsoft/Microsoft.Unity.Analyzers/issues/new?template=Feature_request.md) with the description.

# Prerequisites
For building and testing, you'll need the .NET Core SDK. We recommend using the **.NET Core SDK Version 3.1.100 (LTS)** or later.

This project is using the `DiagnosticSuppressor` API to conditionally suppress reported compiler/analyzer diagnostics. This API is available starting with **Visual Studio 2019 16.3** or **Visual Studio for Mac 8.3**. On Windows, you'll need the _Visual Studio extension development_ workload installed to build a VSIX to use and debug the project in Visual Studio.

For unit-testing, we require Unity to be installed. We recommend using the latest LTS version for that.

# Building and testing

Everything can be compiled and deployed using the command line:

Compiling the main project only:
`dotnet build .\src\Microsoft.Unity.Analyzers`

Unit-Testing:
`dotnet test .\src\Microsoft.Unity.Analyzers.Tests`

Compiling all projects and deploying analyzers/suppressors as a VSIX extension into the Visual Studio Experimental Instance:
`msbuild .\src\Microsoft.Unity.Analyzers.sln`

# Debugging

The easiest way to compile and debug interactively is to use Visual Studio 2019 :
- Load the `Microsoft.Unity.Analyzers.sln` solution.
- Set `Microsoft.Unity.Analyzers.Vsix` as your startup project.
- Hit play (Current Instance) to start debugging an experimental instance of Visual Studio 2019.
- Load any Unity project in the VS experimental instance then put breakpoints in the Analyzer project using the VS main instance.

# Handling duplicate diagnostics 

Starting with **Visual Studio Tools for Unity 4.3.2.0 (or 2.3.2.0 on MacOS)**, we ship and automatically include this set of analyzers/suppressors in all projects generated by Unity (using `<Analyzer Include="..." />` directive).

The downside of this is when trying to debug your own solution is to find yourself with duplicated diagnostics because Visual Studio will load both:
- the project-local analyzer that we release and include automatically, through the `<Analyzer Include="..." />` directive. 
- the VSIX extension you deployed, that will apply analyzers/suppressors to all projects in the IDE.

To disable the project-local analyzer, and keeping a workflow compatible with Unity re-generating project files on all asset changes, you can add the following script in an `Editor` folder of your Unity project to disable all local analyzers loaded with `<Analyzer Include="..." />` directive.

```csharp
using UnityEditor;
using System.Text.RegularExpressions;

public class DisableLocalAnalyzersPostProcessor : AssetPostprocessor
{
	public static string OnGeneratedCSProject(string path, string content)
	{
		return Regex.Replace(content, "(\\<Analyzer)\\s+(Include=\".*Microsoft\\.Unity\\.Analyzers\\.dll\")", "$1 Condition=\"false\" $2");
	}
}
```

# Creating a new analyzer 

To easily create a new analyzer, you can use the following command:

`dotnet run --project .\src\new-analyzer`

This will automatically create source files for the analyzer, associated tests and add resource entries.

# Contributing

This project welcomes contributions and suggestions.  Most contributions require you to agree to a
Contributor License Agreement (CLA) declaring that you have the right to, and actually do, grant us
the rights to use your contribution. For details, visit https://cla.opensource.microsoft.com.

When you submit a pull request, a CLA bot will automatically determine whether you need to provide
a CLA and decorate the PR appropriately (e.g., status check, comment). Simply follow the instructions
provided by the bot. You will only need to do this once across all repos using our CLA.

This project has adopted the [Microsoft Open Source Code of Conduct](https://opensource.microsoft.com/codeofconduct/).
For more information see the [Code of Conduct FAQ](https://opensource.microsoft.com/codeofconduct/faq/) or
contact [opencode@microsoft.com](mailto:opencode@microsoft.com) with any additional questions or comments.
