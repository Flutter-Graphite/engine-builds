# Building Flutter locally with Graphite Enabled

**Note:** macOS is the only platform that supports compiling for both Android
and iOS, so I would recommend using a mac if possible.

## Setting up the dev environment

_The following is adapted from
[Flutter's documentation](https://github.com/flutter/flutter/wiki/Setting-up-the-Engine-development-environment)._

Make sure you have the following dependencies available:

- Linux, macOS, or Windows.
  - Linux supports cross-compiling artifacts for Android and Fuchsia, but not
    iOS.
  - macOS supports cross-compiling artifacts for Android and iOS.
  - Windows doesn't support cross-compiling artifacts for any of Android,
    Fuchsia, or iOS.
- git (used for source version control).
- An IDE. See also the section at the bottom of this page for advice on setting
  up syntax highlighting while editing the engine.
- An ssh client (used to authenticate with GitHub).
- Chromium's
  [depot_tools](https://commondatastorage.googleapis.com/chrome-infra-docs/flat/depot_tools/docs/html/depot_tools_tutorial.html#_setting_up)
  (make sure it's in your path). We use the `gclient` tool from `depot_tools`.
  - Make sure to install gclient by running `./gclient` in the command line.
- Python (used by many of our tools, including gclient).
- On macOS and Linux: curl and unzip (used by gclient sync).
- On Windows:
  - Visual Studio 2017 or later (required for non-Googlers only).
  - [Windows 10 SDK](https://developer.microsoft.com/en-us/windows/downloads/windows-sdk/)
    (required for non-Googlers only). Be sure to install the "Debugging Tools
    for Windows" feature.
- On macOS: the latest Xcode.
- Recommended for Googlers: Goma for distributed builds. The compiling page has
  more information on how to set this up.

### Setup steps

1. If you haven't configured your machine with an SSH key that's known to github
   then follow the directions here:
   https://help.github.com/articles/generating-ssh-keys/.
2. Non-Googler Windows users should set the following environment variables to
   point `depot_tools` to their Visual Studio installation directory:
   - `DEPOT_TOOLS_WIN_TOOLCHAIN=0`
   - `GYP_MSVS_OVERRIDE_PATH=C:\Program Files\Microsoft Visual Studio\2022\Community`
     (use the path of your installation)
3. Create an empty directory called `engine` for your copy of the repository and
   `cd` into it. (It is possible to use a different name, but some tools assume
   this name unless configured otherwise, so calling it `engine` will make thing
   easier.)
4. Create a `.gclient` file in the `engine` directory with the following
   contents:

```python
solutions = [
  {
    "managed": False,
    "name": "src/flutter",
    "url": "git@github.com:flutter/engine.git",
    "custom_deps": {
      "src/third_party/skia": "git@github.com:Flutter-Graphite/skia.git" 
    },
    "deps_file": "DEPS",
    "safesync_url": "",
  },
]
```

5. `gclient sync` in that directory. On Windows, `gclient sync` must be run as
   Administrator due to this issue. This will fetch all the source code that
   Flutter depends on. Avoid interrupting this script, as doing so can leave
   your repository in an inconsistent state that is tedious to clean up. (This
   step automatically runs `git clone`, among other things.)
