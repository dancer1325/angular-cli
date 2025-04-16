# @angular-devkit/build-angular

* == [Architect builders](/packages/angular_devkit/architect/README.md) /
  * allows, about Angular applications and libraries,
    * build
    * test

## Builders

* TODO:

| Name            | Description                                                                                                                                                                                          |
| --------------- |------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| application     | Build an Angular application / target a browser & server environment -- via -- [esbuild](https://esbuild.github.io)                                                                                  |
| app-shell       | Build an Angular [App shell](https://angular.dev/ecosystem/service-workers/app-shell).                                                                                                               |
| browser         | Build an Angular application / target a browser environment -- via -- [Webpack](https://webpack.js.org)                                                                                              |
| browser-esbuild | Build an Angular application / target a browser environment -- via -- [esbuild](https://esbuild.github.io)                                                                                           |
| dev-server      | A development server that provides live reloading.                                                                                                                                                   |
| extract-i18n    | Extract i18n messages from an Angular application.                                                                                                                                                   |
| karma           | Execute unit tests using [Karma](https://github.com/karma-runner/karma) test runner.                                                                                                                 |
| ng-packagr      | Build and package an Angular library in [Angular Package Format (APF)](https://angular.dev/tools/libraries/angular-package-format) format using [ng-packagr](https://github.com/ng-packagr/ng-packagr). |
| prerender       | [Prerender](https://angular.dev/guide/prerendering) pages of your application. Prerendering is the process where a dynamic page is processed at build time generating static HTML.                   |
| server          | Build an Angular application targeting a [Node.js](https://nodejs.org) environment.                                                                                                                  |
| ssr-dev-server  | A development server which offers live reload during development, but uses server-side rendering.                                                                                                    |
| protractor      | **Deprecated** - Run end-to-end tests using [Protractor](https://www.protractortest.org/) framework.                                                                                                 |

## Disclaimer

* builders
  * if they are executed -- via the Angular CLI & their associated options -> are considered stable
* programmatic APIs
  * ❌️NOT considered officially supported ❌
  * NOT subject to the breaking change -- guarantees of -- SemVer
