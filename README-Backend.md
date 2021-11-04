# Project Template: Backend

This project contains the backend of the  __Project Template__ web application. It contains everything needed to develop
the backend of a new HWP module including the associated Bamboo specs in a Maven Java module as well as the deployment
specs of the __Project Template__'s backend to the relevant k8s clusters.

## Table of Contents
- [Module structure](#module-structure)
  * [Important files in the project's root directory](#important-files-in-the-projects-root-directory)
- [Imports from CMMN-Backend](#imports-from-cmmn-backend)
- [Bamboo Specs](#bamboo-specs)
- [Deployment](#deployment)
- [Docker](#docker)
- [Conventions](#conventions)
- [How to start the app](#how-to-start-the-app)
- [How to test the app](#how-to-test-the-app)
- [How to create a new module based on the template](#how-to-create-a-new-module-based-on-the-template)
- [Extending the template](#extending-the-template)
  * [Adding a new dependency](#adding-a-new-dependency)

## Module structure
The __Project Template__ backend project contains the following directories:
- __app__
- __bamboo-specs__
- __data__
- __deployment__
- __gradle__
- __service__

The [__app__ directory](./app) is a module that defines the Spring Boot application. All necessary Spring configurations
are collected within the _application.yaml_. Within this module, REST endpoints are implemented as one or more Spring
REST controllers.

__app__ contains several packages:
- __BackendApplication__ is the Spring Boot application
- __configuration__ holds the Spring Boot configuration
- __endpoint__ holds REST-Controllers and helpers

In the [__bamboo-specs__ directory](./bamboo-specs) the build and deployment plans for the DevStack Bamboo CI/CD system
are stored. They will be automatically scanned and executed if specs scanning has been enabled.

You should add the __bamboo-specs__ as its own module in the _IntelliJ IDEA_ IDE by selecting _File_ > _New_ >
_Module from Existing Sources..._, then choosing the __bamboo-specs__ directory before selecting _Maven_ in the
following dialog. When the __bamboo-specs__ are their own module, you can run build them locally using Maven and run the
included tests. Further information and set-up instructions can be found in the
[README file of the Bamboo Specs directory](./bamboo-specs/README.md).

The [__data__ directory](./data) is a module that contains:
- data classes like POJOs and database entities
- special validation implementation  with no need for any runtime framework like spring (depends on common)
- data object related helpers and utilities like factories
- request objects
- result objects

__data__ contains several packages:
- __entity__ holds entity classes and helpers
- __request__ holds request objects for services (i.e. parameters) and helpers
- __result__ holds result objects of services and helpers

The __data__ module does not have any Spring configuration.

The [__deployment__ directory](./deployment) contains .yaml specs for the deployment to kubernetes as well as associated
script files. More information can be found in the [README file of the deployment section](./deployment/README.md).

The [__service__ directory](./service) is a module that contains all service related implementations for business logic.
A REST-controller must always delegate a request to a corresponding service implementation. Repository access is
encapsulated. __No other than the service module is allowed to use the repository directly!__

__service__ contains several packages:
- __service__ holds services and helpers
- __repository__ holds repositories (JPA and Documents) and helpers

Each submodule has a `build.gradle` mostly to define dependencies.

### Important files in the project's root directory
The following files may require modifications when developing a new application based on the __Project Template__'s
backend:

- _build.gradle_
- _gradle.properties.example_
- _settings.gradle_

The [_build.gradle_](./build.gradle)  has a section of `dependencyManagement` to define all necessary libraries in their
specific version needed. The submodules refer to those libraries without version number. If the dependencies need to be
updated the following articles need to be adhered:
- [How-to handle Open Source Licenses](https://devstack.vwgroup.com/confluence/x/eUMEC)
- [Update strategy for Open Source Libraries](https://devstack.vwgroup.com/confluence/x/w3_dCg)

The [_gradle.properties.example_](./gradle.properties.example) file represents a template to build a _gradle.properties_
file from.

The [_settings.gradle_](./settings.gradle) simply includes the submodules.

## Imports from CMMN-Backend
This project imports multiple packages from CMMN-Backend:
- _common-auth_ provides utilities for authorization
- _common-base_ provides several features:
  - A `RestResponse` used as a HWP module-standard for response format
  - A `HwpExceptionHandler` to equally handle exceptions across all HWP modules
  - A config to add the hort commit id from git to the header to improve the support for your application
  - A way to handle application context:
    - Manage user credentials 
    - Add X-Correlation-Id header 
    - Add X-App-Id header
  - Converters for the user-Id and correlation-ID to ake equalizing logging formats across HWP modules easier
  - An interceptor to get the current acceptance status of the ToU from the USPI backend before each request is handled
  - A configuration for _Jackson_ `ObjectMapper` that extends the default mapper by the `JsonNullableModule`
- _common-gdpr_ provides the needed framework to answer gdpr requests

## Bamboo Specs
The __Project Template__ includes _Bamboo Java specs_ to automatically generate build and deployment plans if Bamboo has
been appropriately configured.

Further information can be [found in the README of the _Bamboo Specs_ directory](./bamboo-specs/README.md).

## Deployment
In order to deploy a new application based on the __Project Template__ into the AWS Cloud, a number of changes may have
to be made to the files in the `deployment` directory.

Further instructions and information can be [found in the README of the _Deployment_ directory](./deployment/README.md).

## Docker
A _Dockerfile_ is located in the root directory of the project. In most cases there should be no need to change anything
in it. The _Dockerfile_ is adapted for the use in our AWS EKS clusters and the project specifications, which must be
taken into account during changes.

## Conventions
Entities (SQL or NoSQL) **MUST NOT** have a qualifying pre- or suffix, e.g. `Book`, `Language`, `Application`. Request
objects for service method parameters **MUST** be suffixed with `Request`, e.g. `CreateBookRequest`,
`FilterLanguageRequest`, `UpdateApplicationRequest`. Result objects (i.e. base types of returned objects of service
methods) **MUST** be suffixed with `Result`, e.g. `BookResult`, `List<LanguageResult>`, `Optional<ApplicationResult>`.

Interfaces **MUST NOT** be qualified with a pre- or suffix. Interfaces **MUST NOT** be defined at all, if there is
exactly one implementation. Start interfacing with the need for a second implementation (e.g.
`AuthorizerMock implements AuthAccess`, `AuthorizerProd implements AuthAccess`).

JPA or other repositories **MUST** end on `Repository`. Services **MUST** end on `Service`. Controllers **MUST** end on
`Controller`. HTTP-Request-Interceptors **MUST** end on `Interceptor`. HTTP-Filter **MUST** end on `Filter`.
Configuration classes **MUST** end on `Configuration`. Name similar objects analogically.

All dependencies in the _build.gradle_ shall have a fixed version. Their licenses need to meet
[the official guidelines](https://devstack.vwgroup.com/confluence/display/AP/How-to+handle+Open+Source+Licenses).
All dependencies that are only used in a specific scope are declared as such in the _build.gradle_.

Furthermore, _SonarQube_ should be used for a static analysis of the code. If _SonarQube_ finds code smells, possible
vulnerabilities or bugs, they should be fixed.

## How to start the app
Ensure having an appropriate Java Version (see [build.gradle](./build.gradle)). Create your personal _gradle.properties_
based on [gradle.properties.example](./gradle.properties.example). Without this file it is not possible to login to
_Artifactory_ and draw all dependencies from there.

**NOTICE:** Due to a bug within _IntelliJ IDEA_, it is not possible to login to _Artifactory_ when executing _Gradle_
`build` or `reload` through the IDE. A currently accepted workaround is to execute [`gradlew`](./gradlew) on a CLI.

To start the __Project Template__'s backend web application, simple start the
[ModuletemplateApplication.java](./app/src/main/java/com/vwgroup/hwp/moduletemplate/ModuletemplateApplication.java) with
the profile `development`.

This runs the app in the development mode at [http://localhost:8080](http://localhost:8080).

The application needs to be restarted every time after you have edited any source file.

You can also build the __Project Template__ backend web application using the _Gradle_ task `build`. This will build the
app for production into the `build` folder.

## How to test the app
The backend of the __Project Template__ includes some tests. To execute all tests use the _Gradle_ task `test`.

## How to create a new module based on the template
The __Project Template__ should be forked by forking within
[_Bitbucket_](https://devstack.vwgroup.com/bitbucket/projects/AP/repos/projecttemplate-backend?fork). It is important
that _Fork-Synchronisation_ is turned **OFF**, because this would lead to a big amount of unnecessary branches and
builds within the _Bamboo_ build pipeline.

After a new module has been forked from the __Project Template__ the following steps should be taken:

Rename/replace all references to the __Project Template__:
- the [ModuletemplateApplication](app/src/main/java/com/vwgroup/hwp/moduletemplate/ModuletemplateApplication.java)
- the [ModuletemplateApplication (test)](app/src/test/java/com/vwgroup/hwp/moduletemplate/ModuletemplateApplication.java)
- the `app.id` within the [application.yml](app/src/main/resources/application.yml)
- the `allprojects`: `group` and `version` within the [build.gradle](build.gradle)
- the `bootJar`: `mainClassName` within the [build.gradle(:app)](app/build.gradle)
- the `rootProject.name` within the [settings.gradle](settings.gradle)
- the file name, `info.title` and `info.description` of/within the
  [OpenAPI file](app/src/main/resources/public/open-api/tmpl-api.yaml)
- the class names of the authorization-package:
  - [TemplatePermission](service/src/main/java/com/vwgroup/hwp/moduletemplate/service/authorization/TemplatePermission.java)
  - [TemplateAuthorizedObjectConverter](service/src/main/java/com/vwgroup/hwp/moduletemplate/service/authorization/TemplateAuthorizedObjectConverter.java)
- the names of all packages

Configure the _BitBucket_ repository by configuring repository permissions (admin permissions to user ground
_cgm-ap-administrators_ and _cgm-ap-developers_), branch permissions and merge checks. Ensure that the fork
synchronization is disabled and delete the branches that are not needed in your module.

Set up Bamboo as described in the [Bamboo Specs README](./bamboo-specs/README.md).

Before the first deployment, you may want to change the deployment files as described in the
[deployment README](./deployment/README.md).

## Extending the template
When adding a new component to the __Project Template__'s backend, ensure that it meets all conventions and standards.
Aside of the normal reviewer, also assign the architects as reviewers to the pull request.

### Adding a new dependency
All dependencies of the HWP modules must also be included in the __Project Template__. The reasons for adding the new
dependency should be documented, and the architects as well the relevant product owners should agree with the decision
to add the new dependency.

To add a new dependency, just add it to the correct section of the _build.gradle_.

Ensure that all versions in the _build.gradle_ are fixed.
