# Updater
**Updater** is a SFML feature that allows updating game files without any user interaction.

The Updater runs every time the game's activity starts.
Upon loading, the Updater will read `manifest.toml` from app's external storage and `vendor.toml` from APK's assets. If `vendor.toml` file doesn't exist, then the Updater would panic.

### If `manifest.toml` file is missing
For each update package (of "gamedata" and "mod"), the Updater would do the following actions:

  - Package's `package_type` must be set to `"full"`, otherwise Updater would skip that package
  - [Update](#package-updating-procedure-in-detail) the target package unconditionally
  - Write new `manifest.toml` inherited from `vendor.toml`

### If `manifest.toml` file is present
The first thing the Updater does is compare `info.product_id` of both vendor and manifest. if they do not match, then Updater would exit and report this in logs.

If vendor's `info.version` and manifest's `info.version` are equal, then the Updater would exit because there are supposedly no updates.

For every vendor and manifest update packages ("gamedata" and "mod"), the Updater would perform the following actions:

  - If vendor's package version is lower or equal to manifest's package version, then the Updater would skip updating this package.
  - [Update](#package-updating-procedure-in-detail) the target package with setting package version from vendor package

If, as a result of the update procedure, any package versions have changed, the Updater would write a new `manifest.toml` inherited from vendor with the updated package versions.



## Package updating procedure in detail
Updating the package with name `pkg_name` consists of doing the following actions:

  - If `package_type` is set to `"full"`, then recursively delete package's destionation directory only if it exists on disk (`/sdcard/Android/data/app package name/files/pkg_name`)
  - Recursively copy the `assets/sfml_data/pkg_name/` directory from the APK to the destination directory
  - If recursive copying fails, then the Updater will panic


## Vendor file format (vendor.toml)
The purpose of vendor file is to explicitly define SFML product information (such as general mod info, authors and install instructions).

!!! note "Update packages"
    `[mod]` and `[gamedata]` tables describe **update packages** with names `mod` and `gamedata` respectively. You have to define these two packages, and you can't define any additional ones.

    The difference from "regular" packages is that they additionally have `package_type` fields, so "update packages" are supersets of regular "packages"

Format example:
```toml
[info]
product = "My SFML mod"
product_id = "myorgianization.mybelovedmod" # organization.modname format is not a strict requirement, but rather a recommendation for mod developers
author = "Me and my friends"
version = "0.0.1"

[mod]
version = "0.0.1"
package_type = "update"

[gamedata]
version = "0.0.1"
package_type = "full"
```
All `version` fields follow [Semantic Versioning](https://semver.org/).

File location (inside of the APK file): `assets/sfml_data/vendor.toml`

`package_type` is an enum with only 2 valid options: `update` and `full`

## Manifest file format (manifest.toml)
Manifest file format is almost the same as vendor's, but there are no `package_type` fields.

!!! note "Packages"
    `[mod]` and `[gamedata]` tables describe **packages** with names `mod` and `gamedata` respectively. You have to define these two packages, and you can't define any additional ones.

Example:
```toml
[info]
product = "My SFML mod"
product_id = "myorgianization.mybelovedmod" # organization.modname format is not a strict requirement, but rather a recommendation for mod developers
author = "Me and my friends"
version = "0.0.1"

[mod]
version = "0.0.1"

[gamedata]
version = "0.0.1"
```

File location (on disk): `/sdcard/Android/data/app package name/files/manifest.toml`

!!! warning "No manifest"
    Beware that on the first game launch, or after clearing app's data, there would initially be **no** `manifest.toml` file!
