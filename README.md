# react-native-cli-bump-version

A **simple** react-native cli plugin to bump versions at platform files.

## Install

`npm i --save-dev @ernestbies/react-native-cli-bump-version`

`yarn add -D @ernestbies/react-native-cli-bump-version`

## Usage

Since this is a react-native cli plugin, after adding it to the project
you can call:

```shell script
npx react-native bump-version --type patch --version-file version.json
```

That should produce this:

```shell script
iOS project.pbxproj code: 24 -> 25
Android build.gradle code: 23 -> 24
iOS project.pbxproj version: 1.10.6 -> 1.10.7
Android gradle.build version: 1.10.6 -> 1.10.7
package.json: 1.10.6 -> 1.10.7
Version file saved at: dir/version.json
```

The plugin updates and write the output listed files, and it's up to you to
commit them.

If you use the `--version-file fileName` flag, the versionName and versionCode for Android and iOS will be saved into the specified file.

Tip: I usually create a script entry for the command, since it tends to be long:

```json
{
    "scripts": {
        "bump": "npx react-native bump-version --skip-semver-for android"
    }
}
```

That way you can invoke it like: `yarn bump --type patch`

## Flags

Just ask for help:

```shell script
npx react-native bump-version --help

Options:
  --type [major|minor|patch]           SemVer release type, optional if --skip-semver-for all is passed.
  --semver [String]                    Pass release version if known. Overwrites calculated SemVer. Optional.
  --skip-semver-for [android|ios|all]  Skips bump SemVer for specified platform.
  --skip-code-for [android|ios|all]    Skips bump version codes for specified platform.
  --version-file [String]              Specifies the filename to save version information. Optional.
  -h, --help                           display help for command
```

### Recommendations

#### Use gradle for SemVer sync

Android can handle automatically semantic version sync with `package.json`:

```groovy
import groovy.json.JsonSlurper

def getNpmVersion() {
    def inputFile = file("$rootDir/../package.json")
    def jsonPackage = new JsonSlurper().parseText(inputFile.text)

    return jsonPackage["version"]
}

android {
  ...
  defaultConfig {
        applicationId "com.example"
        minSdkVersion rootProject.ext.minSdkVersion
        targetSdkVersion rootProject.ext.targetSdkVersion
        versionCode 25
        versionName getNpmVersion()
        ...
    }
    ...
}
```

Note: with this you should pass `--skip-semver-for android`, otherwise the cli
will break.

#### Use MARKETING_VERSION in `Info.plist`

I've choosen to remove the `Info.plist` manipulation as it was not needed
if it uses the `MARKETING_VERSION` env var, so be sure that your project/xcode is updated and that
the `Info.plist` file has `MARKETING_VERSION` instead of SemVer string:

```xml
	<key>CFBundleShortVersionString</key>
	<string>$(MARKETING_VERSION)</string>
```

### Mention

This is extended library with additional features based on:

-   [react-native-cli-bump-version](https://github.com/Grohden/react-native-cli-bump-version)
