# Registry Specification
**Version 1.3.0**

## Introduction

An OwlPlug Registry is a single remote resource providing a list of available Packages. 

## Specification

### Version

The Owlplug Registry Specification is versioned using [Semantic Versioning 2.0.0](https://semver.org/spec/v2.0.0.html) semver.

The `major`.`minor` portion of the semver (for example `3.0`) designate the Registry Specification feature set. Typically, *`.patch`* versions address errors in this document, not the feature set. Tooling which supports Registry Specification 3.0 should be compatible with all 3.0.* versions. The patch version should not be considered by tooling, making no distinction between `3.0.0` and `3.0.1` for example.

Subsequent minor version releases of the Specification should not interfere with tooling developed to a lower minor version and same major version. Thus a hypothetical `3.1.0` specification should be usable with tooling designed for `3.0.0`.

## Format

A Registry endpoint serve a JSON document. All field names in the specification are **case-sensitive**.
The root object is a [Registry](#Registry) object.

### Example

```json

```

## Schema

### Registry

Document root object. Provides general information about the registry and carries a list of products.

Field Name | Type | Description
---|:---:|---
name | `string` | **REQUIRED**. The name of the registry. 255 characters max.
url | `string` | **REQUIRED**. An url to access registry or developer information. This is not necessarily the endpoint URL. 255 characters max.
schemaVersion | `string` | **REQUIRED**. This string must be the [semantic version number](https://semver.org/spec/v2.0.0.html) of the [OwlPlug Registry Specification](#versions) that the document uses. 255 characters max.
packages | `object` | **REQUIRED**. An associative array with package id string as key and [Package](#Package) as value. Can be empty.

### Package

A Package is basically an Audio Plugin product. For example, `Serum`, `Massive`, `Sylenth` are 3 distinct packages. Each Package contains a list of [PackageVersion](#PackageVersion)

Field Name | Type | Description
---|:---:|---
slug | `string` | **REQUIRED**. Unique lower case and hyphen-separated identifier. 255 characters max and must match `^[a-z0-9]+(?:[a-z0-9]+)*$`. Examples: `wobbleizer`, `my-plugin`, `an-other-slug`.  
latestVersion | `string` | **REQUIRED**. The name of the product/plugin. 255 characters max.
versions | `object` | **REQUIRED**. An associative array with version string as key and [PackageVersion](#PackageVersion) as value. Can be empty.


### PackageVersion

A PackageVersion is a released version for a [Package](#Package). For example `Serum 1.3.5.7` and `Serum 1.3.6.3` are two PackageVersion for the `Serum` Package.
Each PackageVersion conains a list of [Bundle](#Bundle)

Field Name | Type | Description
---|:---:|---
name | `string` | **REQUIRED**. The name of the product/plugin. 255 characters max.
creator | `string` | **REQUIRED**. Product creator / manufacturer name. 255 characters max.
license | `string` | Distribution license. Free field but keep it short. Use the [Github keywords](https://docs.github.com/en/github/creating-cloning-and-archiving-repositories/creating-a-repository-on-github/licensing-a-repository) like `gpl-3.0`, `mit`, `...` as most as possible for consistency. 255 character max.
type | `string` | **REQUIRED**. Must be `instrument` for VSTi or `effect` for VST/VSTfx or `unknown`. 255 characters max.
stage | `string` | Can be `beta` for an early release, `demo` for a limited release or `release` for stable build. 255 characters max.
pageUrl | `string` | **REQUIRED**. The plugin page url. 255 characters max.
donateUrl | `string` | Creator support/donation URL. 255 characters max.
screenshotUrl | `string` | **REQUIRED**. Plugin screenshot url. Must be a png file. 255 characters max.
description | `string` | **REQUIRED**. A short plugin description. 1000 characters max.
version | `string`| Plugin version. Version will be applied to all bundles if not overloaded.
technicalUid | `string` | Plugin unique id. Uid will be applied to all bundles if not overloaded.
tags | `array` | An array of [Tag](#Tag). Can be empty.
bundles | `array` | **REQUIRED**. An array of [Bundle](#Bundle). Can be empty.


### Bundle

A bundle is a binary artifact containing a plugin in a specific `format` for one or multiple `targets` environments (Windows, OSX...).
For example, a bundle can contains a vst3 plugin binary for Win64 and OSX environment. 

Field Name | Type | Description
---|:---:|---
name | `string` | **REQUIRED**. The name of the bundle. 255 characters max.
targets | `array` | **REQUIRED**. Array of plugin [Target](#Target). Supported environements by the bundle.
formats | `array` | **REQUIRED**. List of plugins formats in the bundle. Items must be `vst`, `vst3`, `au`, `lv2`, `clap`, `aax` or `unknown`.
format | `string` | *Deprecated*. Use Bundle `formats` field instead.
technicalUid | `string` | Bundle/Plugin unique id. Overload parent *registry.packages[p].versions[v].technicalUid* property.
downloadUrl | `string` | **REQUIRED**. Bundle download url. 255 characters max. Check [Bundle file structure](#Bundle_file_structure)
downloadSha256 | `string` | **REQUIRED**. SHA256 digest for the file download from `downloadUrl`
fileSize | `long` | Size of bundle file in bytes.


### Tag

A Tag is just a simple string describing main features of a Plugin. 255 characters max are allowed for each tag. You can specify any keywords that describes the plugin. OwlPlug will suggest the following list of tags:
```
Ambient,Amp,Analog,Bass,Brass,Compressor,Delay,Distortion,Drum,Equalizer,Filter,Flanger,Gate,Guitar,LFO,Limiter,Maximizer,Monophonic,Orchestral,Organ,Panner,Phaser,Piano,Reverb,Synth,Tremolo,Tube,Vintage
```

### Target

A Target is just a simple string describing main target environments of a plugin. You must use following identifiers:
* Windows: `win-x32`, `win-x64`
* OSX: `mac`
* Linux: `linux-x32`, `linux-x64`, `linux-arm32`, `linux-arm64`

For example, in case of a Win64 bit release, you should specify `win-x64`. If a bundle contains both x64 and x86 windows distributions, you can have an array of target like this `["win-x64", "win-x32"]`. This is **not** a compatibility flag so if you are distributing a Win32 release only, you must **not** specify `win-x64`.

### Bundle file structure

A bundle file must be a zip file. There is no naming convention for the file. A bundle file can be organized in 4 different way:

* Directly
```
plugin.zip/
    ├── plugin.[dll | vst | vst3]
    └── (other files...)
```
* Environment Specific
```
plugin.zip/
    ├── win64
    │    ├── plugin.dll
    │    └── (other required files...)
    └── osx
         ├── plugin.dll
         └── (other required files...)
```
* Directly nested (in a root subfolder)
```
plugin.zip/
    └── plugin
        ├── plugin.[dll | vst | vst3]
        └── (other required files...)
```
* Environment Specific nested (in a root subfolder)
```
plugin.zip/
    └── plugin
        ├── win64
        │    ├── plugin.dll
        │    └── (other required files...)
        └── osx
            ├── plugin.vst
            └── (other required files...)
```
Environment folders names must follow [Target](#Target) convention. Using one of this structure allow OwlPlug to maintain a clean directory tree. If your archive structure don't match, the bundle will be installed as it.
Bundle file must contains plugin file used by the DAW and all required files. It's a common practice to add some docs in bundles subfolders. All `.exe`, `.msi` are not recognized by OwlPlug and cannot be launched for security reasons. 

It's up to you to decide how to organize your plugin distribution. One bundle with different environment targets, or multiple specific bundles. If you are distributing VST 3.6.10+, it's a best practice to distribute only one packaged bundle.

