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
      #max-parallel: 2 # Use this if you're activating pro license with matrix
      matrix:
        include:
          - os: windows-latest
            build-target: StandaloneWindows64
          - os: macos-latest
            build-target: StandaloneOSX
          - os: ubuntu-latest
            build-target: StandaloneLinux64

    steps:
      - uses: actions/checkout@v3

        # Installs the Unity Editor based on your project version text file
        # sets -> env.UNITY_EDITOR_PATH
        # sets -> env.UNITY_PROJECT_PATH
        # https://github.com/XRTK/unity-setup
      - uses: xrtk/unity-setup@v7
        with:
          build-targets: ${{ matrix.build-target }}

        # Activates the installation with the provided credentials
        # https://github.com/XRTK/activate-unity-license
      - uses: xrtk/activate-unity-license@v2
        with:
          # Required
          username: ${{ secrets.UNITY_USERNAME }}
          password: ${{ secrets.UNITY_PASSWORD }}
          # Optional
          serial: ${{ secrets.UNITY_SERIAL }} # Required for pro/plus activations
          license-type: 'Personal' # Chooses license type to use [ Personal, Professional ]

      - name: Unity Build (${{ matrix.build-target }})
        uses: xrtk/unity-build@v4
        with:
          build-target: ${{ matrix.build-target }}
```
