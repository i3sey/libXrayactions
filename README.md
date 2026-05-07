# libXray

[简体中文](./readme/README.zh_CN.md)

This is a wrapper around [Xray-core](https://github.com/XTLS/Xray-core) to improve the client development experience.

# Note

1. This repository has few maintainers. If you do not report a bug or initiate a PR, your issue will be ignored.
2. This repository does not guarantee API stability, you need to adapt it yourself.
3. This repository is only compatible with the latest release of Xray-core.

# Features

## build

Compile script. It is recommended to always use this script to compile libXray. We will not answer questions caused by using other compilation methods.

depends on git and go.

### Usage

```shell
# Android (min Android API level is 21)
python3 build/main.py android

# Apple (gomobile or go)
python3 build/main.py apple gomobile
python3 build/main.py apple go

# Linux
python3 build/main.py linux

# Windows
python3 build/main.py windows

```

### Android

use [gomobile](https://github.com/golang/mobile) .

### iOS && macOS

#### 1. use gomobile

Need "iOS Simulator Runtime".

This is the best choice for general scenarios and will not conflict with other frameworks.

Supports iOS, iOSSimulator, macOS, macCatalyst.

But it is not possible to set the minimum macOS version, which will cause some warnings when compiling. And it does not support tvOS.

#### 2. use cgo

Need "iOS Simulator Runtime" and "tvOS Simulator Runtime".

Support more compilation options, output c header files.

This works well when you use ffi for integration. For example, integration with swift, kotlin, dart.

Support iOS, iOSSimulator, macOS, tvOS.

Note: The product `LibXray.xcframework` does not contain **module.modulemap**. When using swift, you need to create a bridge file.

### Linux

depend on gcc and g++.

### Windows

depend on MinGW.

you can use winget to install [LLVM MinGW](https://github.com/mstorsjo/llvm-mingw) or [WinLibs](https://github.com/brechtsanders/winlibs_mingw) .

```shell
winget install MartinStorsjo.LLVM-MinGW.UCRT
winget install BrechtSanders.WinLibs.POSIX.UCRT
```

## controller

Used to solve the socket protect problem on Android.

## dns

Used to solve server address resolution issues on Android, Linux, and Windows. If not handled, the DNS traffic will be resent to the tun device, resulting in failure to initiate a connection.

## geo

### count

Read geo files and count the categories and rules.

### read

Read the Xray Json configuration and extract the geo file name used.

## main

Download geosite.dat and geoip.dat and count them.

## memory

Only executed on iOS, GC is initiated once a second. This can alleviate memory pressure on iOS.

## nodep

### file

Write data to a file.

### measure

Speed ​​test the Xray configuration.

### model

The response body of the wrapper interface.

### port

Get free ports.

## share

libXray uses `sendThrough` to store outbound names.

### clash_meta

Parse Clash.Meta configuration.

### generate_share

convert Xray Json to VMessAEAD/VLESS sharing protocol.

### parse_share

convert VMessAEAD/VLESS sharing protocol to Xray Json.

convert VMessQRCode to Xray Json.

### vmess

convert VMessQRCode to Xray Json.

### xray_json

Some tools used to parse shared links.

## xray

### ping

Latency testing.

### stats

Refer to the following configuration:

```json
{
  "metrics" : {
    "tag" : "metrics",
    "listen": "[::1]:49227",
  },
  "policy" : {
    "system" : {
      "statsInboundDownlink" : true,
      "statsInboundUplink" : true,
      "statsOutboundDownlink" : true,
      "statsOutboundUplink" : true
    }
  },
  "stats" : {}
}
```

Note:

1. When testing latency or validating configuration, make sure `metrics` is `null`.

2. When enabling metrics, the Xray-core instance needs to be run in a **child process**.

### validation

Verify the Xray configuration.

### xray

Start and stop Xray instances.

## nodep_wrapper

export nodep.

### xray_wrapper

export xray.

# Credits

[Project X](https://github.com/XTLS/Xray-core)

[VMessPing](https://github.com/v2fly/vmessping)

[FreePort](https://github.com/phayes/freeport)

# License

This repository is based on the MIT License.

---

## Android Build with GitHub Actions

### Quick Start

To build an Android library using GitHub Actions:

```bash
# Create a new branch for your build
git checkout -b android-build-<version>

# Edit .github/workflows/android-build.yml to set desired options, then:

# Option 1: Run via GitHub UI (easiest)
# Go to Actions tab → Android Build → "Run workflow" button
# Select branch and click "Run workflow"

# Option 2: Trigger from command line
git push origin android-build-<version>
```

### Workflow Options

| Parameter | Default | Description |
|-----------|---------|-------------|
| `android_api_level` | 21 | Minimum Android API level (minSdkVersion) |
| `go_version` | 1.26.2 | Go version for compilation |
| `python_version` | 3.11 | Python version for build scripts |

### Output Artifacts

After a successful build, the following files are generated:

- **libXray-sources.jar** - Java source JAR containing Go bindings
- **libXray.aar** - Android Archive with compiled library and resources

Both artifacts are automatically uploaded to GitHub Actions artifacts storage and retained for 30 days.

### Using in Your Android Project

#### Gradle (Kotlin/Java)

Add to your `build.gradle`:

```kotlin
dependencies {
    // Replace with actual artifact path or use a release tag
    implementation files('libXray-sources.jar')
}
```

Or if using a released version from artifacts:

```kotlin
// Example for a specific run number (replace with actual)
implementation("com.github.i3sey.libXrayactions:libXray-android-<RUN_NUMBER>:libXray-sources.jar")
```

#### Android Studio Import

1. Right-click your project → **Add Library to Project**
2. Navigate to the JAR file from artifacts
3. Sync Gradle files

### Reusable Action

The `.github/actions/android-build/action.yml` can be imported into other workflows:

```yaml
jobs:
  build-android:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      
      - name: Build Android Library
        uses: i3sey/libXrayactions/.github/actions/android-build@main
        with:
          android_api_level: 25
```

### Troubleshooting

#### Common Issues

1. **gomobile not found**: The workflow automatically installs gomobile on the first run. Subsequent runs use the cached installation.

2. **Build timeout (60 min default)**: For large projects or complex configurations, consider increasing `build_timeout_minutes` in `.github/vars/android-config.yml`.

3. **macOS vs Ubuntu**: macOS is recommended for best gomobile compatibility. If using Ubuntu, ensure Xcode command-line tools are installed via Homebrew.

#### Checking Build Status

```bash
# View the latest build summary
git log --oneline -1 | head -n 1

# Check artifacts in GitHub UI
# Actions → Android Build → Select run → Artifacts tab
```
