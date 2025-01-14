# JLLPrefixes

Collect, symlink and copy around prefixes of JLL packages!  This package makes it easy to use prefixes of JLL packages outside of Julia, either by symlinking or copying them to a stable prefix.

Example:

```julia
using JLLPrefixes

# Download all of FFMPEG_jll, then copy all the files into the ~/local/ffmpeg_prefix` folder
prefix = expanduser("~/local/ffmpeg_prefix")
artifact_paths = collect_artifact_paths(["FFMPEG_jll"])
deplot_artifact_paths(prefix, artifact_paths)
run(`$(joinpath(prefix, "bin", "ffmpeg")) -version`)
```

The files are now available to be used outside of Julia!  No more `LD_LIBRARY_PATH` shenanigans!  Note that some tools (such as `git`) may still need some help finding their data files, and so you may still need to define _some_ environment variables.

## Deployment strategies

By default, `JLLPrefixes` will attempt to use `hardlink()` to hit the sweet spot of performance (hardlinking is faster than copying) and compatibility (executables with RPATHs and other relative paths embedded within them don't play well with symlinks).
If you are unable to use hardlinks (e.g. your source and destination are located on different filesystems/devices) it will silently fall back to using `copy()`.
If you require high performance and don't care about compatibility with executables with embedded relative paths, you can manually request `deploy_artifact_paths()` to use `symlink()` by passing `:symlink` as the `strategy` keyword argument.
