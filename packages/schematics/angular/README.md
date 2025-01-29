# @schematics/angular

* == collection of [schematics](/packages/angular_devkit/schematics/README.md) /
  * 👀generate an Angular application 👀

## Schematics

| Name           | Description                                                                                                 |
|----------------|-------------------------------------------------------------------------------------------------------------|
| app-shell      | Generates an app shell / runs a server-side version of an app                                               |
| application    | Generates a NEW basic app definition in the workspace's "projects/"                                         |
| class          | Creates a NEW, generic class definition in the GIVEN project                                                |
| component      | Creates a NEW, generic component definition in the GIVEN project                                            |
| directive      | Creates a NEW, generic directive definition in the GIVEN project                                            |
| enum           | Generates a NEW, generic enum definition in the GIVEN project                                               |
| guard          | Generates a NEW, generic route guard definition in the GIVEN project                                        |
| interceptor    | Creates a NEW, generic interceptor definition in the GIVEN project                                          |
| interface      | Creates a NEW, generic interface definition in the GIVEN project                                            |
| library        | Creates a NEW, generic library project in the CURRENT workspace                                             |
| module         | Creates a NEW, generic NgModule definition in the GIVEN project                                             |
| ng-new         | Creates a NEW project -- by combining the -- workspace + application schematics                             |
| pipe           | Creates a NEW, generic pipe definition in the GIVEN project                                                 |
| resolver       | Creates a NEW, generic resolver definition in the GIVEN project                                             |
| service        | Creates a NEW, generic service definition in the GIVEN project                                              |
| service-worker | Creates a service worker -- by passing this schematic to the -- `run` command                               |
| web-worker     | Creates a NEW, generic web worker definition in the GIVEN project                                           |
| workspace      | Initializes an EMPTY workspace and adds the necessary dependencies -- required by an -- Angular application |

## Disclaimer

* schematics / executed -- via the
  * Angular CLI & their associated options -- are considered stable
  * ❌programmatic APIs -- are NOT
    * officially supported
    * subject -- to the -- breaking change guarantees of SemVer ❌
