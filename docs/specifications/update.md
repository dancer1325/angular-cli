# Update Command -- `ng update` --

* allows
  * 👀updating 👀
    * +>=1 packages +
    * packages' peer dependencies +
    * package's peer dependencies / -- depends on -- it

* ❌if there are inconsistencies -> tool will error out & NOTHING committed | filesystem ❌
  * _Example of inconsistency:_ peer dependencies -- can NOT be matches by a -- SIMPLE semver range

## Command Line Usage

```bash
ng update <package1 [package2 [...]]> [options]
```

* packages
  * 👀-- follows the convention of -- `[@scope/]packageName[@version-range-or-dist-tag]` 👀
  * ❌NOT found | YOUR dependencies -> will trigger an error ❌
  * ❌ / have a higher version | YOUR `package.json` -> will trigger an error ❌

* TODO:

| Flag             | Argument  | Description                                                                                                                                                                                 |
| ---------------- | --------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `--force`        | `boolean` | If true, skip the verification step and perform the update even if some peer dependencies would be invalidated. Peer dependencies errors will still be shown as warning. Defaults to false. |
| `--next`         | `boolean` | If true, allows version discovery to include Beta and RC. Defaults to false.                                                                                                                |
| `--migrate-only` | `boolean` | If true, don't change the `package.json` file, only apply migration scripts.                                                                                                                |
| `--from`         | `version` | Apply migrations from a certain version number.                                                                                                                                             |
| `--to`           | `version` | Apply migrations up to a certain version number (inclusive). By default will update to the installed version.                                                                               |

## Details

* steps order / -- performed by -- schematic
  1. from `package.json` -- get ALL installed package names & versions, and -- set | `dependencyMap: Map<string, string>`
  2. from `dependencyMap: Map<string, string>` -- fetch ALL NPM repository's `package.json` / contains ALL versions, and -- gather them | `Map<string, NpmPackageJson>`
  3. update the `Map<>`'s package version / believed to be installed ( == largest version number / match the version range)
  4. believed installed version (could be) != exact installed versions
     1. recommendations: have a proper `package-lock.json` loader & `yarn.lock`
  5. / EACH packages mentioned | command line -- update to the -- target version
     1. by default, largest NON-beta NON-rc version

```python
# ARGV    The packages being requested by the user.
# NPM     A map of package name to a map of version to PackageJson structure.
# V       A map of package name to available versions.
# PKG     A map of package name to PackageJson structure, for the installed versions.
# next    A flag for the "--next" command line argument.

# First add all updating packages' peer dependencies. This should be recursive but simplified
# here for readability.
ARGV += [ NPM[p][max([ v for v in V[p] if (not is_beta(v) or next) ])].peerDependencies
          for p in ARGV ]

for p in ARGV:
  x = max([ v for v in V[p] if (not is_beta(v) or next) ])

  for other in set(PKG.keys()) - set([ p ]):
    # Verify all packages' peer dependencies.
    if has(other.peerDependencies, p) and !compatible(x, other.peerDependencies[p]):
      showError('Cannot update dependency "%s": "%s" is incompatible with the updated dependency' % (x, other))

    if any( has(other.peerDependencies, peer) and !compatible(x, other.peerDependencies[peer])
            for peer in PKG[p].peerDependencies.keys() ):
      showError('Cannot update dependency "%s": "%s" depends on an incompatible peer dependency' % (x, other))

  update_package_json(p, x)
```

## Library Developers

* Libraries
  * -- responsible for defining -- their OWN update schematics

* `ng update` tool
  * 👀if it detects the `"ng-update"` key | library's "package.json" -> will run the update schematic | it 👀
  * 👀if `"ng-update"` key NOT defined | library's "package.json" ->
    * NOT support the update workflow
    * `ng update` == `npm install` 👀

### Migration

* steps
  * | `package.json`,
    * add the `ng-update` key

* `ng-update` fields

| Field Name         | Type                                      | Description                                                                                                                                                                                                                                                                                                                                                                                 |
| ------------------ | ----------------------------------------- |---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `requirements`     | `{ [packageName: string]: VersionRange }` | TODO:A map of package names to version to check for minimal requirement. If one of the libraries listed here does not match the version range specified in `requirements`, an error will be shown to the user to manually update those libraries. For example, `@angular/core` does not support updates from versions earlier than 5, so this field would be `{ '@angular/core': '>= 5' }`. |
| `migrations`       | `string`                                  | A relative path (or resolved using Node module resolution) to a Schematics collection definition.                                                                                                                                                                                                                                                                                           |
| `packageGroup`     | `string[]`                                | A list of npm packages that are to be grouped together. When running the update schematic it will automatically include all packages as part of the packageGroup in the update (if the user also installed them).                                                                                                                                                                           |
| `packageGroupName` | `string`                                  | The name of the packageGroup to use. By default, uses the first package in the packageGroup. The packageGroupName needs to be part of the packageGroup and should be a valid package name.                                                                                                                                                                                                  |

#### Example

* Library my-lib / 2 steps to update (4 to 4.5, 4.5 to 5)

    ```json, title=package.json
    {
      "ng-update": {
        "requirements": {
          "my-lib": "^5"
        },
        "migrations": "./migrations/migration-collection.json"
      }
    }
    ```

* steps
  * create a migration collection == Schematics collection

    ```json
    {
      "schematics": {
        "migration-01": {
          "version": "6",
          "factory": "./update-6"
        },
        "migration-02": {
          "version": "6.2",
          "factory": "./update-6_2"
        },
        "migration-03": {
          "version": "6.3",
          "factory": "./update-6_3"
        },
        "migration-04": {
          "version": "7",
          "factory": "./update-7"
        },
        "migration-05": {
          "version": "8",
          "factory": "./update-8"
        }
      }
    }
    ```

* TODO:
The update tool would then read the current version of library installed, check against all `version` fields and run the schematics, until it reaches the version required by the user (inclusively).
If such a collection is used to update from version 5 to version 7, the `01`, `02`, `03,` and `04` functions would be called.
If the current version is 7 and a `--refactor-only` flag is passed, it would run the migration `04` only.
More arguments are needed to know from which version you are updating.

* if you run `ng update @angular/core` == run `ng generate @angular/core/migrations:migration-01`

## Use cases

### Help

`ng update`, shows what updates would be applied;

```sh
$ ng update
We analyzed your package.json, there's some packages to update:

Name                Version            Command to update
----------------------------------------------------------------------------
@angular/cli        1.7.0  >  6.0.0    ng update @angular/cli
@angular/core       5.4.3  >  6.0.1    ng update @angular/core
@angular/material   5.2.1  >  6.0.0    ng update @angular/material
@angular/router     5.4.3  >  6.0.1    ng update @angular/core

There might be additional packages that are outdated.
```

### Simple Multi-steps

I have a dependency on Angular, Material and CLI. I want to update the CLI, then Angular, then Material in separate steps.

#### Details

1. `ng update @angular/cli`.
   Updates the CLI and packages that have a peer dependencies on the CLI (none), running refactoring tools from CLI 1 to 6.
1. `ng update @angular/core`.
   Updates the Core package and all packages that have a peer dependency on it.
   2. This can get tricky if `@angular/material` get caught in the update because the version installed does not directly allow the new version of `@angular/core`. In this case

### Complex Case

package.json:

```json
{
  "dependencies": {
    "@angular/material": "5.0.0",
    "@angular/core": "5.5.5"
  }
}
```

Commands:

```bash
ng update @angular/core
```

- updates `@angular/core` to the `latest` dist-tag (6.0.0)
- sees that `@angular/material` is not compatible with 6.0.0; **error out.**

```bash
ng update @angular/material
```

- update `@angular/material` to latest version, that should be compatible with the current `@angular/core`.
- if that version is not compatible with you
- tell the user about a higher version that requires an update to `@angular/core`.

## Notes

1. if someone is on CLI 1.5, the command is not supported. The user needs to update to `@angular/cli@latest`, then `ng update @angular/cli`.
   1. Post install hook will check versions of cli configuration and show a message to run the `ng update` command.
1. NPM proxies or cache are not supported by the first version of this command.
