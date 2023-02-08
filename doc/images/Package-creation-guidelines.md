# Package creation guidelines

This page describes rules for creating package manifest in the owlplug-registry repository. You must follow this rules if you are creating package using the CLI or updating files by hand.

Audio plugins delivered by the owlplug-registry are described using the YAML format. Multiple YAML files are used to describe **Package**, **PackageVersion** and **Bundles** entities.
* A **Package** is an abstract concept for a plugin.
* A **PackageVersion** is an abstract concept for a plugin release version.
* A **Bundle** is a set of binaries for a particular package version.

```
                                                                         
   +-----------+ 1      1,n  +----------------+ 1      1,n +--------+    
   |  Package  |-------------| PackageVersion |------------| Bundle |    
   +-----------+             +----------------+            +--------+    
                                                                         
```


For example, [dropsnorz/wobbleizer](https://github.com/OwlPlug/owlplug-registry/blob/master/registry/dropsnorz/wobbleizer/) is a **Package**,  [dropsnorz/wobbleizer/v2.3.0.2](https://github.com/OwlPlug/owlplug-registry/blob/master/registry/dropsnorz/wobbleizer/2.3.0.2/package.yaml) is a **PackageVersion**.
This package version contains 3 different **Bundles**:
* A `vst-win64` bundle containing the plugin as `VST` and targeting `win64` arch
* A `vst-win32` bundle containing the plugin as `VST` and targeting `win32` arch
* An `au-osx` bundle containing the plugins as `AU` and targeting `osx` arch


## Choose a `groupId` and a `packageId` for your package

All packages are identified by a `groupId` and a `packageId`. It should be lowercase and can contains dashes.

* `groupId` : Company, name of creator, brand.
* `packageId` : Package name / identifier

These properties will be used to generate a unique slug identifier for a package : `<groupId>/<packageId>`.


## Create or Update a `package.yaml` manifest in the registry

Package version manifest must be stored on the following path from the repository root: `./registry/<groupId>/<packageId>/<version>/package.yaml`
* `<groupId>` : Package groupId
* `<packageId>` : Package identifier
* `<version>` : Package version (using SemVer format)

 
## Package and Bundles properties guidelines

This section describes rules that must be followed when creating or updating properties defined in `package.yaml` file on the registry.

All properties are described in details in the [Registry Specification]().

**package.name**
* 30 characters maximum

**package.homepage**
* URL must be https

**package.screenshotUrl**
* URL must be https
* Image must be png or jpg.
* Image size must not be more than 1000x1000px 
* Image file size must not be more than 600ko (Use optimizer like [tinypng](https://tinypng.com/))
* Image should not contains inappropriate content

**bundle.downloadUrl**
* URL must be https
* Binaries hosted behind URL should not be updated (link to download the latest version will be refused)
* URL domain must be the package official website (`package.homepage`) or a subdomain, `github.com` or a subdomain, `owlplug.com` or a sub-domain.
* The file behind URL must be a `zip` file
* Avoid domain redirects
