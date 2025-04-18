* `interface BuilderContext {}`
  * uses
    * your builder's 2@ argument
  * TODO:
  * `logger: logging.LoggerApi`
    * == logger / appends messages | log
    * ways
      * separate interface OR
      * completely ignored
  * `reportStatus(status: string): void;`
    * update the status string / shown | interface
      * if `status` == empty string -> remove the status
  * `reportProgress(current: number, total?: number, status?: string): void;`
    * update the progress / this builder run
    * `current`
      * == CURRENT progress
      * 's values [0, total]
    * `total`
      * == NEW total -- to -- set
      * by default,
        * | start of a run == 1
      * if omitted -> 's value == last total
    * `status`
      * update the status string
      * if omitted -> status string is NOT modified
  * TODO:
*
  ```
  type BuilderInput = json.JsonObject & RealBuilderInput;
  type BuilderOutput = json.JsonObject & RealBuilderOutput;
  type BuilderProgress = json.JsonObject & RealBuilderProgress & TypedBuilderProgress;
  ```
  * -> compatible -- with -- `JsonObject`
    * Complexity: JsonObject requires MANDATORY ALL members & Angular's types can have OPTIONAL members
    * Solution: 🧠make compatible -- via -- `&` of types 🧠

* TODO:
