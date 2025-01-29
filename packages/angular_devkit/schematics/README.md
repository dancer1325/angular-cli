# Schematics

* := scaffolding library
  * -- for the -- modern web
  * ❌-> NOT work by itself ❌

## Description

* Schematics
  * == generators /
    * about EXITING filesystem, can
      * refactor
      * move them around
    * can create files
  * vs OTHER generators (_Example:_ Yeoman or Yarn Create)
    * purely descriptive
    * ONCE (NOT BEFORE) ALL is ready to be committed -> changes are applied | ACTUAL filesystem
  * 👀NO side effect, by design 👀

# Glossary

| Term           | Description                                                                                                                                                                                                                                                                                                                                    |
| -------------- |------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Schematics** | == generator / executes descriptive code <br/> &nbsp; WITHOUT side effects <br/> &nbsp; on EXISTING file system                                                                                                                                                                                                                                |
| **Collection** | == list of schematics metadata / schematics -- can be referred by -- name                                                                                                                                                                                                                                                                      |
| **Tool**       | == code / use the Schematics library                                                                                                                                                                                                                                                                                                           |
| **Tree**       | == staging area for changes / contains <br/> &nbsp; the original file system <br/> &nbsp; list of changes to apply to it                                                                                                                                                                                                                       |
| **Rule**       | == function / <br/> &nbsp; applies actions to a `Tree` <br/> &nbsp; returns a NEW `Tree` / will contain ALL transformations to be applied                                                                                                                                                                                                      |
| **Source**     | == function / <br/> &nbsp; from an EMPTY filesystem -- creates an -- entirely NEW `Tree` <br/> _Example:_ file source / from disk read files & create a Create Action / EACH read file                                                                                                                                                         |
| **Action**     | == atomic operation / <br/> &nbsp; -- to be validated & committed to a -- filesystem or a `Tree` <br/> &nbsp; created by schematics                                                                                                                                                                                                            |
| **Sink**       | == ALL `Action`s final destination                                                                                                                                                                                                                                                                                                             |
| **Task**       | == way to execute an external command or script in a schematic <br/> &nbsp; -- created by using the -- `SchematicContext` object <br/> &nbsp; can be scheduled to run BEFORE or AFTER the schematic `Tree` is applied  <br/> uses <br/> &nbsp; perform actions (_Example:_ installing dependencies, running tests, or building a project) |

# Tooling

* goal here
  * library usage
  * tooling API / NOT go into the tool implementation itself

* [Schematics CLI](../schematics_cli)
  * published | [NPM's @angular-devkit/schematics-cli](https://www.npmjs.com/package/@angular-devkit/schematics-cli)
  * responsible for
    1. TODO: Create the Schematic Engine, and pass in a Collection and Schematic loader.
    1. Understand and respect the Schematics metadata and dependencies between collections. Schematics can refer to dependencies, and it's the responsibility of the tool to honor those dependencies.
       2. The reference CLI uses NPM packages for its collections.
    1. Create the Options object.
       2. Options can be anything, but the schematics can specify a JSON Schema that should be respected.
       3. The reference CLI, for example, parses the arguments as a JSON object and validates it with the Schema specified by the collection.
    1. Schematics provides some JSON Schema formats for validation that tooling should add.
       2. These validate paths, html selectors and app names.
       3. Please check the reference CLI for how these can be added.
    1. Call the schematics with the original Tree.
       2. The tree should represent the initial state of the filesystem.
       3. The reference CLI uses the current directory for this.
    1. Create a Sink and commit the result of the schematics to the Sink.
       2. Many sinks are provided by the library; FileSystemSink and DryRunSink are examples.
    1. Output any logs propagated by the library, including debugging information.

* tooling API's components
  * [`SchematicEngine`](#engine----schematicengine---)

## Engine -- `SchematicEngine` --

* `SchematicEngine`
  * -- responsible for --
    * loading & constructing
      * `Collection`s
      * `Schematics`
  * | create an engine, the tooling -- provides an -- `EngineHost` interface / understands how
    * from a name -- create -- a `CollectionDescription`
    * create a `SchematicDescription`

# Schematics (Generators)

* Schematics
  * == generators
  * == part of a `Collection`

## Collection -- `collection.json` --

* place |  reference CLI (❓)
* 's properties

| Prop Name   | Type     | Description     |
| ----------- | -------- |-----------------|
| **name**    | `string` | collection name |
| **version** | `string` | unused field    |

## Schematic

# Operators, Sources and Rules

* `Source`
  * == generator of a `Tree` / from nothing -- creates an -- entirely NEW root tree
* `Rule`
  * == transformation / from one `Tree` -- to -- another `Tree`
  * [`Rule`](src/engine/interface.ts#L73)
* `Schematic`
  * | root
  * == `Rule` / normally applied | filesystem

## Operators

`FileOperator`s apply changes to a single `FileEntry` and return a new `FileEntry`. The result follows these rules:

1. If the `FileEntry` returned is null, a `DeleteAction` will be added to the action list.
1. If the path changed, a `RenameAction` will be added to the action list.
1. If the content changed, an `OverwriteAction` will be added to the action list.

It is impossible to create files using a `FileOperator`.

## Provided Operators

The Schematics library provides multiple `Operator` factories by default that cover basic use cases:

| FileOperator                     | Description                                                          |
| -------------------------------- | -------------------------------------------------------------------- |
| `contentTemplate<T>(options: T)` | Apply a content template (see the [Templating](#templating) section) |
| `pathTemplate<T>(options: T)`    | Apply a path template (see the [Templating](#templating) section)    |

## Provided Sources

The Schematics library additionally provides multiple `Source` factories by default:

| Source                                 | Description                                                                                                                |
| -------------------------------------- | -------------------------------------------------------------------------------------------------------------------------- |
| `empty()`                              | Creates a source that returns an empty `Tree`.                                                                             |
| `source(tree: Tree)`                   | Creates a `Source` that returns the `Tree` passed in as argument.                                                          |
| `url(url: string)`                     | Loads a list of files from the given URL and returns a `Tree` with the files as `CreateAction` applied to an empty `Tree`. |
| `apply(source: Source, rules: Rule[])` | Apply a list of `Rule`s to a source, and return the resulting `Source`.                                                    |

## Provided Rules

The schematics library also provides `Rule` factories by default:

| Rule                                        | Description                                                                            |
| ------------------------------------------- | -------------------------------------------------------------------------------------- |
| `noop()`                                    | Returns the input `Tree` as is.                                                        |
| `chain(rules: Rule[])`                      | Returns a `Rule` that's the concatenation of other `Rule`s.                            |
| `forEach(op: FileOperator)`                 | Returns a `Rule` that applies an operator to every file of the input `Tree`.           |
| `move(root: string)`                        | Moves all the files from the input to a subdirectory.                                  |
| `merge(other: Tree)`                        | Merge the input `Tree` with the other `Tree`.                                          |
| `contentTemplate<T>(options: T)`            | Apply a content template (see the Template section) to the entire `Tree`.              |
| `pathTemplate<T>(options: T)`               | Apply a path template (see the Template section) to the entire `Tree`.                 |
| `template<T>(options: T)`                   | Apply both path and content templates (see the Template section) to the entire `Tree`. |
| `filter(predicate: FilePredicate<boolean>)` | Returns the input `Tree` with files that do not pass the `FilePredicate`.              |

# Templating

As referenced above, some functions are based upon a file templating system, which consists of path and content templating.

The system operates on placeholders defined inside files or their paths as loaded in the `Tree` and fills these in as defined in the following, using values passed into the `Rule` which applies the templating (i.e. `template<T>(options: T)`).

## Path Templating

| Placeholder             | Description                                                                                                                      |
| ----------------------- | -------------------------------------------------------------------------------------------------------------------------------- |
| `__variable__`          | Replaced with the value of `variable`.                                                                                           |
| `__variable@function__` | Replaced with the result of the call `function(variable)`. Can be chained to the left (`__variable@function1@function2__ ` etc). |

## Content Templating

| Placeholder         | Description                                                                                                                                 |
| ------------------- | ------------------------------------------------------------------------------------------------------------------------------------------- |
| `<%= expression %>` | Replaced with the result of the call of the given expression. This only supports direct expressions, no structural (for/if/...) JavaScript. |
| `<%- expression %>` | Same as above, but the value of the result will be escaped for HTML when inserted (i.e. replacing '<' with '\&lt;')                         |
| `<% inline code %>` | Inserts the given code into the template structure, allowing to insert structural JavaScript.                                               |
| `<%# text %>`       | A comment, which gets entirely dropped.                                                                                                     |

# Examples

* TODO: add | real project?

## Simple

* Schematics /
  * creates a "hello world" file
  * determine -- via an option -- its path

```typescript
import { Tree } from '@angular-devkit/schematics';

// options      ==    list options -- from the -- tooling
// returns a Rule   == transformation / from a `Treee` -- to -- ANOTHER `Tree`
export default function MySchematic(options: any) {
  return (tree: Tree) => {
    tree.create(options.path + '/hi', 'Hello world!');
    return tree;
  };
}
```

## Templating

* Schematics / creates -- via passing an option for the name, a -- file containing a new Class

```typescript
// files/__name@dasherize__.ts

export class <%= classify(name) %> {
}
```

```typescript
// index.ts

import { strings } from '@angular-devkit/core';
import {
  Rule,
  SchematicContext,
  SchematicsException,
  Tree,
  apply,
  branchAndMerge,
  mergeWith,
  template,
  url,
} from '@angular-devkit/schematics';
import { Schema as ClassOptions } from './schema';

export default function (options: ClassOptions): Rule {
  return (tree: Tree, context: SchematicContext) => {
    if (!options.name) {
      throw new SchematicsException('Option (name) is required.');
    }

    const templateSource = apply(url('./files'), [
      template({
        ...strings,
        ...options,
      }),
    ]);

    return branchAndMerge(mergeWith(templateSource));
  };
}
```

Additional things from this example:

1. `strings` provides the used `dasherize` and `classify` functions, among others.
1. The files are on-disk in the same root directory as the `index.ts` and loaded into a `Tree`.
1. Then the `template` `Rule` fills in the specified templating placeholders.
   2. For this, it only knows about the variables and functions passed to it via the options-object.
1. Finally, the resulting `Tree`, containing the new file, is merged with the existing files of the project which the Schematic is run on.
