# Project Template: Frontend

This project contains the frontend of the  __Project Template__ web application created with
[Create React App](https://github.com/facebook/create-react-app). It contains everything
needed to develop the frontend of a new HWP module including the associated Bamboo specs in a
Maven Java module as well as the deployment specs of the __Project Template__'s frontend to the
relevant k8s clusters.

## Table of Contents
- [Module structure](#module-structure)
    * [Important files in the project's root directory](#important-files-in-the-projects-root-directory)
- [Translations with i18next](#translations-with-i18next)
- [Imports from CMMN-Frontend](#imports-from-cmmn-frontend)
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
The __Project Template__ frontend project contains the following directories:
- __.idea__
- __bamboo-specs__
- __deployment__
- __public__
- __src__
- __test__

The [__.idea__ directory](./.idea) contains files related to the settings of the _IntelliJ IDEA_
IDE.

In the [__bamboo-specs__ directory](./bamboo-specs) the build and deployment plans for the DevStack
Bamboo CI/CD system are stored. They will be automatically scanned and executed if specs
scanning has been enabled.

You should add the __bamboo-specs__ as its own module in the _IntelliJ IDEA_ IDE by selecting
_File_ > _New_ > _Module from Existing Sources..._, then choosing the __bamboo-specs__ directory
before selecting _Maven_ in the following dialog.
When the __bamboo-specs__ are their own module, you can run build them locally using Maven and run
the included tests. Further information and set-up instructions can be found in the [README file of
the Bamboo Specs directory](./bamboo-specs/README.md).

The [__deployment__ directory](./deployment) contains .yaml specs for the deployment to kubernetes
as well as associated script files. More information can be found in the [README file of the
deployment section](./deployment/README.md).

The [__public__ directory](./public) is the location for the various assets required for a web
application including locales in different languages (i.e. the
[de.json](./public/locales/basic/de.json) and [en.json](./public/locales/basic/en.json)) as used by
_i18next_, graphics such as a favicon file, and other files such as the _manifest.json_ and the
_robots.txt_.

The [__src__ directory](./src) is the heart of the React App and contains all components, pages and
other relevant code constructs.

In the [__test__ directory](./test) _Cypress_ integration tests with mocked data, plugins and custom
commands can be found. How to start the tests can be found in
[how to start template project](#how-to-start-the-template-project)

### Important files in the project's root directory
The following files may require modifications when developing a new application based on the
 __Project Template__'s frontend:

- _.env_
- _.env.development_
- _package.json_
- _cypress.json_
- _README.md_

The [_.env_](./.env) and [_.env.development_](./.env.development) files contain variables that
will be inserted on local start-up (in case of the _.env.development_) or during the build
process (in case of the _.env_ file)  into the React Application by the _dotenv_ npm module.
They provide constants to the React Application that can be accessed from everywhere within it.

E.g. the _.env_ and _.env.development_ files contains constants defining the duration how long
notifications are shown, external URLs used in the footer, E-Mail addresses shown on the contact
and help pages, the
[application key](https://devstack.vwgroup.com/confluence/display/AP/Application+Keys) and further
constants.

The [_package.json_](./package.json) contains all npm modules included in the project. It also
contains definitions for the scripts, as well as the version and name of the React Application. New
npm modules can be added here [see _Adding a new dependency_](#adding-a-new-dependency) for further
details.

The [_cypress.json_](./cypress.json) contains the settings for _Cypress_.

## Translations with i18next
_i18next_ provides a simple way to localize the application by providing translations in multiple
languages. These are either requested from TROL or loaded from the [public/locales/basic
folder](./public/locales/basic). In order to utilize _i18next_ in React components use the
_useTranslation_ hook.

## Imports from CMMN-Frontend
This project imports multiple packages from CMMN-Frontend:
- _@hwp/api_ provides _Axios_-based hooks that can be used for communication via HTTP
- _@hwp/api-errors_ provides a collection of errors that match the errors from the backend and can
  therefore be parsed
- _@hwp/components_ provides a collection of react components that can and should be used by
  applications within the HWP product family:
  - Ready-to-use components for application _Header_ and _Footer_
  - Components containing a _DateRangePicker_ and _Dropzone_
- _@hwp/notifications_ provides notifications to ensure a constant look-and-feel across
  applications.
- _@hwp/pages_ provides default pages for applications within the HWP product family:
  - A _Help_ page (/help)
  - A _Contact_ page (/contact)
  - A _Data Privacy Statement_ page (/data-privacy-statement)
  - A _Third Party Licences_ page (/third-party-licenses)
  - A _Settings_ page (/settings)
  - A _Terms Of Use_ page (/terms-of-use)
- _@hwp/terms-of-use_ provides logic to manage the terms of use in a centralised way
- _@hwp/terms-of-use-acceptance_ provides logic to manage the status of the user-specific
  acceptance of the terms of use in a centralised way
- _@hwp/theme_ provides a standardised design for applications within the HWP product family
  that is based on the VW corporate design
- _@hwp/trol_ provides logic to channel communication to TROL
- _@hwp/types_ provides general HWP TypeScript types
- _@hwp/user_ provides logic to take care of all centralised user functions offered by the HWP
  cluster

## Bamboo Specs
The __Project Template__ includes _Bamboo Java specs_ to automatically generate build and deployment
plans if Bamboo has been appropriately configured.

Further information can be [found in the README of the _Bamboo Specs_ directory](./bamboo-specs/README.md).

## Deployment
In order to deploy a new application based on the __Project Template__ into the AWS Cloud, a
number of changes may have to be made to the files in the `deployment` directory.

Further instructions and information can be
[found in the README of the _Deployment_ directory](./deployment/README.md).

## Docker
A _Dockerfile_ is located in the root directory of the project. In most cases there should be no
need to change anything in it. The _Dockerfile_ is adapted for the use in our AWS EKS clusters
and the project specifications, which must be taken into account during changes.

## Conventions
Each React component's name shall start with a capital letter. All variables shall be written in
 _camelCase_.

Each React component shall include an _index.ts_ file, which contains nothing but the exports for
the component.

All npm modules are resolved from the DevStack Artifactory by configuring the _node package manager_
[as described in the Confluence](https://devstack.vwgroup.com/confluence/display/AP/How-to+install+node.js)
by executing the following line in the terminal:

    npm config -g set registry https://devstack.vwgroup.com/artifactory/api/npm/npm-public/

All dependencies in the _package.json_ shall have a fixed version. Their licenses need to meet
[the official guidelines](https://devstack.vwgroup.com/confluence/display/AP/How-to+handle+Open+Source+Licenses).
All dependencies that are only used during development are declared as _devDependencies_ in the
_package.json_.

For the hwp dependencies you also need the hwp repository hwp-frontend scope, which can be added by
executing the following lines in the terminal:

    npm config set @hwp:registry https://devstack.vwgroup.com/artifactory/api/npm/hwp-frontend/

    npm login --registry=https://devstack.vwgroup.com/artifactory/api/npm/hwp-frontend/

Enter your W-TAMU username, password and your vw e-mail address like you did before.

The __Project Template__ contains a pre-configured set of _ESLint_ rules derived from the _airbnb
-typescript_ rules, which reflect most of the desired standards. Rules should only be disabled if
no other alternative exists and ideally only locally. The reasons should be documented using
comments.

Furthermore, _SonarQube_ should be used for a static analysis of the code. If _SonarQube_ finds
code smells, possible vulnerabilities or bugs, they should be fixed.

_Jest_ component tests are to be added into the same directory as the corresponding component;
_Cypress_ tests on the other hand go into the `test/cypress/integration` directory.

The fallback translation files shall be located in the
[_public/locales/basic_](./public/locales/basic) folder and contain the entries sorted by key
alphabetically ascending.

## How to start the app
Ensure that appropriate versions of _Node_ (^14.17.0) and _npm_ (7.17.0) are installed. If you plan
to execute _npm_ inside your IDE, you also have to ensure that the correct _npm_ is used(and not the
one bundled with your IDE).
To start the __Project Template__'s frontend/React web application, you can use:
```
npm start
```
This runs the app in the development mode. Open [http://localhost:3000](http://localhost:3000) to
view it in the browser.

The page will reload automatically every time after you have edited any source file. You will
also see any linting errors in the console.

You can also build the __Project Template__ frontend React web application using the command
```
npm run build
```

This will build the app for production into the `build` folder. It correctly bundles React in
production mode and optimizes the build for the best performance.
The build is minified, and the filenames include the hashes. It can be started using the npm
module _serve_ or other alternatives.

## How to test the app
The frontend of the __Project Template__ includes _Cypress_ tests for the user interface's
functionality and _Jest_ tests for components. Whenever a new page is added, _Cypress_ tests
should also be added to verify the page's functionality. Whenever a new component is written,
_Jest_ tests also should be added for the new component. _Jest_ tests can be run using `npm run
test`.

To execute _Cypress_ integration tests for the  __Project Template__'s web frontend, start it as
described above, then use either:
```
npm run cy:run:chrome
```
or
```
npm run cy:run:firefox
```
or
```
npm run cy:run:edge
```
to run the tests in the console using the Chrome browser, Firefox browser or the Edge browser
respectively. It should be noted that Firefox is the main browser used by Volkswagen and hence
should have priority. Alternatively utilize
```
cypress open
```
in order to open the graphical user interface to manually select browser and tests for execution.

To execute _Jest_ unit tests in a terminal execute:
```
npm run test
```

To generate merged code coverage reports use:
```
npm run coverage
```
to clean up existing reports, run _Cypress_ tests in Edge, execute _Jest_ tests and merge the
reports into a single one. The single and merged results can be found within the
[__coverage__ directory](./coverage)

You can execute the __eslint__ checks performed on the
[_build server_](./bamboo-specs/src/main/resources/com/vwgroup/hwp/bamboo/module/test-run-eslint.sh)
locally:
```
eslint --ext .js,.ts,.tsx,.jsx,.json src public
```

You can also just run the i18n checks:
```
eslint --ext .json public
```

To fix eventually found sorted-keys-errors:
```
eslint --ext .json --fix public
```

## How to create a new module based on the template
The __Project Template__ should be forked by forking within
[_Bitbucket_](https://devstack.vwgroup.com/bitbucket/projects/AP/repos/projecttemplate-frontend?fork).
It is important that _Fork-Synchronisation_ is turned **OFF**, because this would lead to a big
amount of unnecessary branches and builds within the _Bamboo_ build pipeline.

After a new module has been forked from the __Project Template__ the following steps should be
taken:

Rename/replace all references to the __Project Template__:
- the `React_APP_ID`, `REACT_APP_NAME` and `REACT_APP_BACKEND_TROL_APP_NAME` variables in the [_.env_](./.env)
 and the [_.env.development_](./.env.development) files
- the `title` in the [_index.html_](./public/index.html)
- the `shortName` and `name` variables in the [_manifest.json_](./public/manifest.json)
- the `name` value in the [_package.json_](./package.json) in the root directory.

Configure the _BitBucket_ repository by configuring repository permissions (admin permissions to
user ground _cgm-ap-administrators_ and _cgm-ap-developers_), branch permissions and merge
checks. Ensure that the fork synchronization is disabled and delete the branches that are not needed
in your module.

Set up Bamboo as described in the [Bamboo Specs README](./bamboo-specs/README.md).

Before the first deployment, you may want to change the deployment files as described in the
[deployment README](./deployment/README.md).

## Extending the template
When adding a new component to the __Project Template__'s frontend, ensure that it meets all
conventions and standards. Aside of the normal reviewer, also assign the architects as reviewers to
the pull request.

### Adding a new dependency
All dependencies of the HWP modules must also be included in the __Project Template__. The reasons
for adding the new dependency should be documented, and the architects as well the relevant
product owners should agree with the decision to add the new dependency.

To add a new dependency, just add it to the correct section of the _package.json_.

Do not use the minimum version `^` annotation but rather ensure that all npm module versions in
the _package.json_ are fixed.
