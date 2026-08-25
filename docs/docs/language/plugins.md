## Plugins

The C2 compiler is more than a tool for turning `.c2` code into binaries — it aims
to speed up development and take care of the busywork around it.

A good example: in a typical Make/CMake/Ninja project, embedding the git version
into a binary means wiring up a small custom build step yourself, every single
time. c2c instead ships a plugin, __git_version__, that generates a module named
`git_version` containing the current git version. Once the plugin is enabled, that
module can be imported and used like any other:

```c
module main;

import stdio as io;
import git_version;

public fn i32 main(i32 argc, char** argv) {
    io.printf("Git version: %s\n", git_version.Describe);
    return 0;
}
```

A plugin is enabled per-target with a `$plugin` line in `recipe.txt` (see
[Recipe file](../build_system/recipe_file.md)):

```
executable main
    $plugin git_version []
    main.c2
end
```

Plugins can also be listed in a *build-file*; since both a recipe and a
build-file might request the same plugin, duplicates are filtered out
automatically.

Besides __git_version__, c2c bundles a few other plugins:

* __deps_generator__ - writes out a dependency graph for the build (used by
  external tooling)
* __refs_generator__ - writes out a cross-reference database, used by
  [c2tags](../build_system/intro.md) and c2rename to jump to / rename symbols
* __unit_test_plugin__ - wires up the [unit test framework](attributes.md#unit-test-framework-)
* __shell_cmd_plugin__ - runs a configured shell command at build time and
  exposes its result as a generated module, similar to __git_version__ but for
  arbitrary commands
