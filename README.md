# XRTK Unity Build

A GitHub Action that builds XRTK based projects.

Part of the [Mixed Reality Toolkit (XRTK)](https://github.com/XRTK) open source project.

> This action requires that your Unity project be setup and using the com.xrtk.core package.

## How to use

```yaml
on:
  push:
    branches:
      - 'main'
  pull_request:
    branches:
      - '*'

  # Allows you to run this workflow manually from the Actions tab
  workflow_dispatch:

concurrency:
  group: ${{ github.ref }}
  cancel-in-progress: true

jobs:
  build:
    runs-on: ${{ matrix.os }}
    strategy:
      matrix:
        include:
          - os: windows-latest
            build-target: StandaloneWindows64
          - os: macos-latest
            build-target: StandaloneOSX
          - os: linux-latest
            build-target: StandaloneLinux64

    steps:
      - uses: actions/checkout@v2
        with:
          submodules: recursive
          clean: true

      - name: Unity Build (${{ matrix.build-target }})
        uses: xrtk/unity-build@main
        with:
          build-target: ${{ matrix.build-target }}
```
