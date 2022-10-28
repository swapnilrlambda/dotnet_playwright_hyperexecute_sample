<img height="100" alt="hyperexecute_logo" src="https://user-images.githubusercontent.com/1688653/159473714-384e60ba-d830-435e-a33f-730df3c3ebc6.png">

HyperExecute is a smart test orchestration platform to run end-to-end Playwright tests at the fastest speed possible. HyperExecute lets you achieve an accelerated time to market by providing a test infrastructure that offers optimal speed, test orchestration, and detailed execution logs.

The overall experience helps teams test code and fix issues at a much faster pace. HyperExecute is configured using a YAML file. Instead of moving the Hub close to you, HyperExecute brings the test scripts close to the Hub!

- <b>HyperExecute HomePage</b>: https://www.lambdatest.com/hyperexecute
- <b>Lambdatest HomePage</b>: https://www.lambdatest.com
- <b>LambdaTest Support</b>: [support@lambdatest.com](mailto:support@lambdatest.com)

To know more about how HyperExecute does intelligent Test Orchestration, do check out [HyperExecute Getting Started Guide](https://www.lambdatest.com/support/docs/getting-started-with-hyperexecute/)

[<img alt="Try it now" width="200 px" align="center" src="images/Try it Now.svg" />](https://hyperexecute.lambdatest.com/?utm_source=github&utm_medium=repository&utm_content=java&utm_term=Playwright)

## Gitpod

Follow the below steps to run Gitpod button:

1. Click '**Open in Gitpod**' button (You will be redirected to Login/Signup page).
2. Login with Lambdatest credentials and it will be redirected to Gitpod editor in new tab and current tab will show hyperexecute dashboard.

## [<img alt="Run in Gitpod" width="200 px" align="center" src="images/Gitpod.svg" />](https://hyperexecute.lambdatest.com/hyperexecute?type=gitpod&framework=.Net&frameworkType=PlayWright)

<!---If logged in, it will be redirected to Gitpod editor in new tab where current tab will show hyperexecute dashboard.

If not logged in, it will be redirected to Login/Signup page and simultaneously redirected to Gitpod editor in a new tab where current tab will show hyperexecute dashboard.

If not signed up, you need to sign up and simultaneously redirected to Gitpod in a new tab where current tab will show hyperexecute dashboard.--->

# How to run Playwright automation tests on HyperExecute (using Playwright framework)

- [Pre-requisites](#pre-requisites)

  - [Download HyperExecute CLI](#download-hyperexecute-cli)
  - [Configure Environment Variables](#configure-environment-variables)

- [Auto-Split Execution with Playwright](#auto-split-execution-with-Playwright)

  - [Core](#core)
  - [Pre Steps and Dependency Caching](#pre-steps-and-dependency-caching)
  - [Post Steps](#post-steps)
  - [Artifacts Management](#artifacts-management)
  - [Test Execution](#test-execution)

- [Matrix Execution with Playwright](#matrix-execution-with-Playwright)

  - [Core](#core-1)
  - [Pre Steps and Dependency Caching](#pre-steps-and-dependency-caching-1)
  - [Post Steps](#post-steps-1)
  - [Artifacts Management](#artifacts-management-1)
  - [Test Execution](#test-execution-1)

- [Secrets Management](#secrets-management)
- [Navigation in Automation Dashboard](#navigation-in-automation-dashboard)

# Pre-requisites

Before using HyperExecute, you have to download HyperExecute CLI corresponding to the host OS. Along with it, you also need to export the environment variables _LT_USERNAME_ and _LT_ACCESS_KEY_ that are available in the [LambdaTest Profile](https://accounts.lambdatest.com/detail/profile) page.

## Download HyperExecute CLI

HyperExecute CLI is the CLI for interacting and running the tests on the HyperExecute Grid. The CLI provides a host of other useful features that accelerate test execution. In order to trigger tests using the CLI, you need to download the HyperExecute CLI binary corresponding to the platform (or OS) from where the tests are triggered:

Also, it is recommended to download the binary in the project's parent directory. Shown below is the location from where you can download the HyperExecute CLI binary:

- Mac: https://downloads.lambdatest.com/hyperexecute/darwin/hyperexecute
- Linux: https://downloads.lambdatest.com/hyperexecute/linux/hyperexecute
- Windows: https://downloads.lambdatest.com/hyperexecute/windows/hyperexecute.exe

## Configure Environment Variables

Before the tests are run, please set the environment variables LT_USERNAME & LT_ACCESS_KEY from the terminal. The account details are available on your [LambdaTest Profile](https://accounts.lambdatest.com/detail/profile) page.

For macOS:

```bash
export LT_USERNAME=LT_USERNAME
export LT_ACCESS_KEY=LT_ACCESS_KEY
```

For Linux:

```bash
export LT_USERNAME=LT_USERNAME
export LT_ACCESS_KEY=LT_ACCESS_KEY
```

For Windows:

```bash
set LT_USERNAME=LT_USERNAME
set LT_ACCESS_KEY=LT_ACCESS_KEY
```

## Auto-Split Execution with Playwright

Auto-split execution mechanism lets you run tests at predefined concurrency and distribute the tests over the available infrastructure. Concurrency can be achieved at different levels - file, module, test suite, test, scenario, etc.

For more information about auto-split execution, check out the [Auto-Split Getting Started Guide](https://www.lambdatest.com/support/docs/getting-started-with-hyperexecute/#smart-auto-test-splitting)

### Core

Auto-split YAML file (_yaml/dotnet_playwright_hyperexecute_autosplit_sample.yaml_) in the repo contains the following configuration:

```yaml
globalTimeout: 150
testSuiteTimeout: 150
testSuiteStep: 150
```

Global timeout, testSuite timeout, and testSuite timeout are set to 150 minutes.

The _runson_ key determines the platform (or operating system) on which the tests are executed. Here we have set the target OS as Windows.

```yaml
runson: win
```

Auto-split is set to true in the YAML file.

```yaml
 autosplit: true
```

_retryOnFailure_ is set to true, instructing HyperExecute to retry failed command(s). The retry operation is carried out till the number of retries mentioned in _maxRetries_ are exhausted or the command execution results in a _Pass_. In addition, the concurrency (i.e. number of parallel sessions) is set to 4.

```yaml
retryOnFailure: true
maxRetries: 5
concurrency: 4
```

### Pre Steps and Dependency Caching

Dependency caching is enabled in the YAML file to ensure that the package dependencies are not downloaded in subsequent runs. The first step is to set the Key used to cache directories. The directory _m2_cache_dir_ is created in the project's root directory.

```yaml
env:
  NUGET_PACKAGES: 'C:\nuget_global_cache'
  NUGET_HTTP_CACHE_PATH: 'C:\nuget_http_cache'
  NUGET_PLUGINS_CACHE_PATH: 'C:\nuget_plugins_cache'
  # Secrets in Vault
  # PAT: ${{ .secrets.testKey }}

cacheKey: '{{ checksum "packages.txt" }}'
```

Steps (or commands) that must run before the test execution are listed in the _pre_ run step. In the example, the Maven packages are downloaded in the _m2_cache_dir_. To prevent test execution at the _pre_ stage, the _maven.test.skip_ parameter is set to _true_ so that only packages are downloaded and no test execution is performed.

```yaml
pre:
  - dotnet list PlaywrightDotnetTests.csproj package > packages.txt
  - nuget locals all -clear
  - dotnet build -c Release
```

### Post Steps

Steps (or commands) that need to run after the test execution are listed in the _post_ step. In the example, we _cat_ the contents of _yaml/win/dotnet_playwright_hyperexecute_autosplit_sample.yaml_

```yaml
post:
  - cat yaml/dotnet_playwright_hyperexecute_autosplit_sample.yaml
```

The _testDiscovery_ directive contains the command that gives details of the mode of execution, along with detailing the command that is used for test execution. Here, we are fetching the list of test names that would be further passed in the _testRunnerCommand_

```yaml
testDiscovery:
  type: raw
  mode: static
  command: grep "case" Program.cs | awk '{print$2}' | sed 's/://g'
```

Running the above command on the terminal will give a list of scenarios present in the Program file:

- single
- iphonetest
- ipadtest

The _testRunnerCommand_ contains the command that is used for triggering the test. The output fetched from the _testDiscoverer_ command acts as an input to the _testRunner_ command.

```yaml
testRunnerCommand: dotnet run $test
```

### Artifacts Management

The _mergeArtifacts_ directive (which is by default _false_) is set to _true_ for merging the artifacts and combing artifacts generated under each task.

The _uploadArtefacts_ directive informs HyperExecute to upload artifacts [files, reports, etc.] generated after task completion. In the example, _path_ consists of a regex for parsing the directories that contain the XML and JSON reports (i.e. _target/surefire-reports/testng-results.xml_ and _target/surefire-reports/testng-results.xml_) directory.

```yaml
mergeArtifacts: true

uploadArtefacts:
  - name: XmlReports
    path:
      - PlaywrightTestReport.xml
  - name: JsonReports
    path:
      - PlaywrightTestReport.json
```

HyperExecute also facilitates the provision to download the artifacts on your local machine. To download the artifacts, click on Artifacts button corresponding to the associated TestID.

<img width="1425" alt="Playwright_autosplit_artefacts_1" src="https://user-images.githubusercontent.com/1688653/160453348-67a416d9-ec37-4467-9a51-1b20484f39f1.png">

### Test Execution

The CLI option _--config_ is used for providing the custom HyperExecute YAML file (i.e. _yaml/dotnet_playwright_hyperexecute_autosplit_sample.yaml).

#### Execute Playwright tests using Autosplit mechanism on Windows platform

Run the following command on the terminal to trigger Playwright tests on the Windows platform. The _--download-artifacts_ option is used to inform HyperExecute to download the artifacts for the job. The _--force-clean-artifacts_ option force cleans any existing artifacts for the project.

```bash
./hyperexecute --config yaml/dotnet_playwright_hyperexecute_autosplit_sample.yaml --force-clean-artifacts --download-artifacts
```

Visit [HyperExecute Automation Dashboard](https://automation.lambdatest.com/hyperexecute) to check the status of execution

<img width="1414" alt="Playwright_autosplit_execution" src="images/dashboard.png">

Shown below is the execution screenshot when the YAML file is triggered from the terminal:

<img width="1412" alt="Playwright_autosplit_cli1_execution" src="images/cli.png">

# Matrix Execution with Playwright

Matrix-based test execution is used for running the same tests across different test (or input) combinations. The Matrix directive in HyperExecute YAML file is a _key:value_ pair where value is an array of strings.

Also, the _key:value_ pairs are opaque strings for HyperExecute. For more information about matrix multiplexing, check out the [Matrix Getting Started Guide](https://www.lambdatest.com/support/docs/getting-started-with-hyperexecute/#matrix-based-build-multiplexing)

### Core

In the current example, matrix YAML file (_yaml/dotnet_playwright_hyperexecute_matrix_sample.yaml_) in the repo contains the following configuration:

```yaml
globalTimeout: 150
testSuiteTimeout: 150
testSuiteStep: 150
```

Global timeout, testSuite timeout, and testSuite timeout are set to 150 minutes.

The target platform is set to Win. Please set the _[runson]_ key to _[mac]_ if the tests have to be executed on the macOS platform.

```yaml
runson: win
```

The _matrix_ constitutes of the following entries - _Playwrighttag_. The entries represent the scenario names in the feature files located in _src/main/java/Features_.

```yaml
matrix:
  tests: ["single", "iphonetest", "ipadtest"]
```

The _testSuites_ object contains a list of commands (that can be presented in an array). In the current YAML file, commands for executing the tests are put in an array (with a '-' preceding each item). The Maven command _mvn test_ is used to run tests located in the current project. In the current project, parallel execution is achieved at the _class_ level. The _maven.repo.local_ parameter in Maven is used for overriding the location where the dependent Maven packages are downloaded. The _Playwright.options_ parameter is used for filtering tests at the _scenario_ level.

```yaml
testSuites:
  - dotnet run $tests
```

### Pre Steps and Dependency Caching

Dependency caching is enabled in the YAML file to ensure that the package dependencies are not downloaded in subsequent runs. The first step is to set the Key used to cache directories. The directory _m2_cache_dir_ is created in the project's root directory.

```yaml
env:
  NUGET_PACKAGES: 'C:\nuget_global_cache'
  NUGET_HTTP_CACHE_PATH: 'C:\nuget_http_cache'
  NUGET_PLUGINS_CACHE_PATH: 'C:\nuget_plugins_cache'

cacheKey: '{{ checksum "packages.txt" }}'
```

Steps (or commands) that must run before the test execution are listed in the _pre_ run step. In the example, the Maven packages are downloaded in the _m2_cache_dir_. To prevent test execution at the _pre_ stage, the _maven.test.skip_ parameter is set to _true_ so that only packages are downloaded and no test execution is performed.

```yaml
pre:
  - dotnet list PlaywrightDotnetTests.csproj package > packages.txt
  - nuget locals all -clear
  - dotnet build -c Release
```

### Post Steps

Steps (or commands) that need to run after the test execution are listed in the _post_ step. In the example, we _cat_ the contents of _yaml/win/dotnet_playwright_hyperexecute_matrix_sample.yaml_

```yaml
post:
  - cat yaml/dotnet_playwright_hyperexecute_matrix_sample.yaml
```

### Artifacts Management

The _mergeArtifacts_ directive (which is by default _false_) is set to _true_ for merging the artifacts and combing artifacts generated under each task.

The _uploadArtefacts_ directive informs HyperExecute to upload artifacts [files, reports, etc.] generated after task completion. In the example, _path_ consists of a regex for parsing the directories that contain the XML and JSON reports (i.e. _target/surefire-reports/testng-results.xml_ and _target/surefire-reports/testng-results.xml_) directory.

```yaml
mergeArtifacts: true

uploadArtefacts:
  - name: XmlReports
    path:
      - PlaywrightTestReport.xml
  - name: JsonReports
    path:
      - PlaywrightTestReport.json
```

HyperExecute also facilitates the provision to download the artifacts on your local machine. To download the artifacts, click on Artifacts button corresponding to the associated TestID.

## Test Execution

The CLI option _--config_ is used for providing the custom HyperExecute YAML file (i.e. _yaml/dotnet_playwright_hyperexecute_matrix_sample.yaml).

#### Execute Playwright tests using Matrix mechanism on Windows platform

Run the following command on the terminal to trigger Playwright tests on the Windows platform. The _--download-artifacts_ option is used to inform HyperExecute to download the artifacts for the job. The _--force-clean-artifacts_ option force cleans any existing artifacts for the project.

```bash
./hyperexecute --config yaml/dotnet_playwright_hyperexecute_matrix_sample.yaml --force-clean-artifacts --download-artifacts
```

Visit [HyperExecute Automation Dashboard](https://automation.lambdatest.com/hyperexecute) to check the status of execution:

<img width="1414" alt="Playwright_matrix_execution" src="images/dashboard.png">

Shown below is the execution screenshot when the YAML file is triggered from the terminal:

<img width="1413" alt="Playwright_cli1_execution" src="images/cli.png">

## Secrets Management

In case you want to use any secret keys in the YAML file, the same can be set by clicking on the _Secrets_ button the dashboard.

<img width="703" alt="Playwright_secrets_key_1" src="https://user-images.githubusercontent.com/1688653/152540968-90e4e8bc-3eb4-4259-856b-5e513cbd19b5.png">

Now create a _secret_ key that you can use in the HyperExecute YAML file.

<img width="359" alt="Playwright_management_1" src="https://user-images.githubusercontent.com/1688653/153250877-e58445d1-2735-409a-970d-14253991c69e.png">

All you need to do is create an environment variable that uses the secret key:

```yaml
env:
  PAT: ${{ .secrets.testKey }}
```

## LambdaTest Community :busts_in_silhouette:

The [LambdaTest Community](https://community.lambdatest.com/) allows people to interact with tech enthusiasts. Connect, ask questions, and learn from tech-savvy people. Discuss best practises in web development, testing, and DevOps with professionals from across the globe.

## Documentation & Resources :books:

If you want to learn more about the LambdaTest's features, setup, and usage, visit the [LambdaTest documentation](https://www.lambdatest.com/support/docs/). You can also find in-depth tutorials around test automation, mobile app testing, responsive testing, manual testing on [LambdaTest Blog](https://www.lambdatest.com/blog/) and [LambdaTest Learning Hub](https://www.lambdatest.com/learning-hub/).

## About LambdaTest

[LambdaTest](https://www.lambdatest.com) is a leading test execution and orchestration platform that is fast, reliable, scalable, and secure. It allows users to run both manual and automated testing of web and mobile apps across 3000+ different browsers, operating systems, and real device combinations. Using LambdaTest, businesses can ensure quicker developer feedback and hence achieve faster go to market. Over 500 enterprises and 1 Million + users across 130+ countries rely on LambdaTest for their testing needs.

[<img height="70" src="https://user-images.githubusercontent.com/70570645/169649126-ed61f6de-49b5-4593-80cf-3391ca40d665.PNG">](https://accounts.lambdatest.com/register)

## We are here to help you :headphones:

- Got a query? we are available 24x7 to help. [Contact Us](mailto:support@lambdatest.com)
- For more info, visit - https://www.lambdatest.com
