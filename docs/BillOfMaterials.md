Bill of Materials
=================

The bill of materials (bom) is a list of artifacts produced by the build.

## Generating a new bom

A new bill of materials is generated on each build and placed in `artifacts/bom.$(Version).xml`.

In addition, this bill of materials can be produced without building the repo by running `build.ps1 -bom`.

## Format

The bill of materials is an XML file. Here is a minimal example of a bom.

```xml
<Build Id="(commithash)+Release+Signed+123" Created="12/1/2017 5:10:06 PM +00:00">

    <Artifacts Category="ship">
        <Artifact Id="Microsoft.Extensions.DependencyInjection.2.0.0-rtm-123.nupkg" Type="NuGetPackage"
            Signed="true"
            PackageId="Microsoft.Extensions.DependencyInjection"
            Version="2.0.0-rtm-123" />
        <Artifact Id="Microsoft.AspNetCore.2.0.0-rtm-123.nupkg" Type="NuGetPackage"
            Signed="true"
            PackageId="Microsoft.AspNetCore"
            Version="2.0.0-rtm-123" />
        <Artifact Id="Microsoft.AspNetCore.2.0.0-rtm-123.symbols.nupkg" Type="NuGetSymbolsPackage"
            Signed="true"
            PackageId="Microsoft.AspNetCore"
            Version="2.0.0-rtm-123" />
        <Artifact Id="aspnetcore-store-2.0.0-rtm-123-win-x64.zip" Type="ZipArchive"
            Signed="true"
            RuntimeIdentifier="win-x64"
            Version="2.0.0-rtm-123" />
        <Artifact Id="aspnetcore-store-2.0.0-rtm-123-linux-x64.deb" Type="DebianPackage"
            Signed="false"
            RuntimeIdentifier="linux-x64"
            Version="2.0.0-rtm-123" />
    </Artifacts>

    <Artifacts Category="noship">
        <Artifact Id="Experimental.1.0.0-alpha.123.nupkg" Type="NuGetPackage"
            PackageId="Experimental"
            Version="1.0.0-alpha.123+commithash:123456" />
        <Artifact Id="Microsoft.AspNetCore.Tests.zip" Type="TestBundle"
            RuntimeFramework="netcoreapp2.0"
            RuntimeFrameworkVersion="2.0.3" />
    </Artifacts>

    <Dependencies>
        <Link Source="Microsoft.Extensions.DependencyInjection.2.0.0-rtm-123.nupkg" Target="Microsoft.Extensions.Common.2.0.0.nupkg" />
        <Link Source="Microsoft.AspNetCore.2.0.0-rtm-123.nupkg" Target="Newtonsoft.Json.9.0.1.nupkg" />
        <Link Source="Microsoft.AspNetCore.2.0.0-rtm-123.nupkg" Target="Microsoft.Extensions.DependencyInjection.2.0.0-rtm-123.nupkg" />
        <Link Source="aspnetcore-store-2.0.0-rtm-123-linux-x64.deb" Target="Microsoft.Extensions.DependencyInjection.2.0.0-rtm-123.nupkg" />
        <Link Source="aspnetcore-store-2.0.0-rtm-123-linux-x64.deb" Target="Microsoft.AspNetCore.2.0.0-rtm-123.nupkg" />
    </Dependencies>

</Build>
```

### Elements

#### `<Build>`

Describes the build output. The root element of the document.

Optional attributes:
  - `Id` - a unique identifier describing this build.
  - `Created` - a timestamp of when the build was created.

#### `<Artifacts>`

Describes a group of artifacts

Optional attributes:
  - `Category` - an arbitrary category name.

#### `<Artifact>`

Describes a file produced by the build.

Required attributes:
  - `Id` - the file name of the artifact. Id MUST BE unique per each bill of materials.
  - `Type` - an arbitrary string describing the semantic type of the artifact.

Optional attributes:
    - Others - any other attributes on the element are considered metadata values about the artifact.

#### `<Dependencies>`

Describes artifact dependencies.

#### `<Link>`

Describes a dependency between two artifacts.

Requried attributes
  - `Source` - the artifact that has a dependency. This artifact MUST BE defined by an `<Artifact>` in the same `<Build>`
  - `Target` - the thing it depends on. This artifact MAY OR MAY NOT be described in this `<Build>`.
    It might have been produced in a previous build or be produced by a partner team.

### Artifact types

These are some common artifact types produced by the build.

  - NuGetPackage
  - NuGetSymbolsPackage
  - DebianPackage
  - RedhatPackage
  - ZipArchive
  - TarGzArchive
  - TextFile
  - TestBundle

### Artifact categories

Tools may use the category information to define how they handle different files.

These are common categories for artifacts used in aspnet/Universe.

  - ship - used for files that will be published to NuGet.org, microsoft.com, or other official publication locations
  - shipoob - used for files that will be distributed in other mechanism.
  - noship - these files should not be published to partner teams or released publically. This may include test artifacts.
