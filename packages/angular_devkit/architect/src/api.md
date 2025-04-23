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
        * recommendations
          * \# of files / -- to -- process
      * by default,
        * | start of a run == 1
      * if omitted -> 's value == last total
    * `status`
      * update the status string
      * if omitted -> status string is NOT modified
  * `scheduleTarget(target: Target, overrides?: json.JsonObject, scheduleOptions?: ScheduleOptions): Promise<BuilderRun> {}`
    * schedule a target | SAME workspace
      * ALTHOUGH target is being executed right now, you can ALSO schedule it
      * targets / SAME name -> are serialized
      * if you run the SAME target & wait for it TILL end -> deadlocking scenario
    * SAME targets
      * == SAME project & target & configuration
    * `@param target`
      * == target -- to -- schedule
    * `@param overrides`
      * == set of options -- to override the -- workspace set of options
    * `@param scheduleOptions`
      * == ADDITIONAL optional / schedule options
    * `@return Promise<BuilderRun>`
      * == promise of a run
      * | ALL run's members are available -> it will resolve
  * `scheduleBuilder(builderName: string, options?: json.JsonObject, scheduleOptions?: ScheduleOptions): Promise<BuilderRun> {}`
    * schedule a builder -- by -- its name
      * ALLOWED ALSO SAME builder / is being executed
    * `@param builderName`
      * == `packageName:builderName` tuple
    * `@param options`
      * ALL options / use -- for the -- builder
      * by default, empty object
    * `@param scheduleOptions`
      * ADDITIONAL scheduling options.
    * `@return Promise<BuilderRun>`
      * | ALL run's members are available, it will resolve
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
* `interface BuilderRun {}`
  * == Run / returned by `scheduleBuilder()` OR `scheduleTarget()`
  * reconstructed -- ACROSS -- memory boundaries
* TODO:
