# Schematics CLI

* == executable -- for running -- a [Schematic](/packages/angular_devkit/schematics/README.md)

# Usage

* `schematics [CollectionName:]SchematicName [options, ...]`
  * if `[CollectionName:]` NOT specified -> use the internal collection -- provided by the -- Schematics CLI
  * `options`
    * `--debug`
      * == debug mode
      * if the collection is a relative path -> by default, it's `true`
    * `--allow-private`
      * from the command line, allow private schematics -- to be -- run
      * by default, `false`
    * `--dry-run`
      * NOT output anything
      * show what actions -- would be -- performed
      * if `debug==true` -> by default, `true`
    * `--force`
      * force overwriting files / -- would otherwise be an -- error
    * `--list-schematics`
      * list ALL collection's schematics -- by -- name
        * collection name -- should be suffixed by a -- colon
          * _Example:_ '@angular-devkit/schematics-cli:'
    * `--no-interactive`
      * disables interactive input prompts
    * `--verbose`
      * show MORE information
    * `--help`

# Examples

* `schematics blank <name>`
  * create a NEW NPM package / contains a blank schematic
* `schematics schematic --name <name>`
  * how to build a schematic
