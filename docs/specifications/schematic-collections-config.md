# Schematics Collections (`schematicCollections`)

* `schematicCollections`
  * uses
    * | root or at project level 's `angular.json` 's  `cli` option

        ```jsonc
        {
          "$schema": "./node_modules/@angular/cli/lib/config/schema.json",
          "version": 1,
          "cli": {
            "schematicCollections": ["@schematics/angular", "@angular/material"]
          }
          // ...
        }
        ```
  * if it's NOT configured & you run a schematic / NOT part of `@schematics/angular` -> you need to run `ng generate [collection-name:schematic-name]`
    * -> `ng generate` TOO verbose | REPEATED usages -> 👀recommended to specify `schematicCollections` 👀

* _Example:_ if you add `@angular/material` | `schematicCollections` & you run `ng generate navigation` -> == (being run) `ng generate @angular/material:navigation`

## Conflicting schematic names

* if Schematics Collections1's schematic name == Schematics Collections2's schematic name & you run `ng generate` and `ng new` -> 👀will run the FIRST schematic matched / set | `schematicCollections` 👀
