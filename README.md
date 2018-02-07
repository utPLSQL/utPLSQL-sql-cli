This project is no longer supported or developed. 
----
It was replaced with [utPLSQL-cli](https://github.com/utPLSQL/utPLSQL-cli)
----


# utPLSQL-sql-cli

Bash & windows command-line client for [utPLSQL v3](https://github.com/utPLSQL/utPLSQL/)

Provides an easy way of invoking utPLSQL from command-line.
Main features:

- Provides outputs from first reporter in real-time, so you can see the progress of your test execution 
- Ability to run tests with multiple reporters simultaneously
- Ability to save output from every individual reporter to separate output file
- Provides coloured outputs
- Maps project and test files to database objects for reporting purposes
- Allows execution of selected suites, subset of suite

# Requirements 

The scripts require `sqlplus` to be installed and configured to be in your PATH.

When using reporters for Sonar or Coveralls the the `ut_run.bat`/`ut_run` script needs to be invoked from project's root directory.     

Number of script parameters cannot exceed 39.

# Script Invocation

`ut_run user/password@database [-p=(ut_path|ut_paths)] [-c] [-f=format [-o=output] [-s] ...] [-source_path=path] [-test_path=path]`

# Parameters

```
  user              - username to connect as
  password          - password of the user
  database          - database to connect to
  -p=suite_path(s)     - A suite path or a comma separated list of suite paths for unit test to be executed.     
                      The path(s) can be in one of the following formats:
                        schema[.package[.procedure]]
                        schema:suite[.suite[.suite][...]][.procedure]
                      Both formats can be mixed in the list.
                      If only schema is provided, then all suites owner by that schema are executed.
                      If -p is omitted, the current schema is used.
  -f=format         - A reporter to be used for reporting.
                    If no -f option is provided, the default ut_documentation_reporter is used.
                    Available options:
                      -f=ut_documentation_reporter
                        A textual pretty-print of unit test results (usually use for console output)
                      -f=ut_teamcity_reporter
                        For reporting live progress of test execution with Teamcity CI. 
                      -f=ut_xunit_reporter
                        Used for reporting test results with CI servers like Jenkins/Hudson/Teamcity.
                      -f=ut_coverage_html_reporter
                        Generates a HTML coverage report with summary and line by line information on code coverage.
                        Based on open-source simplecov-html coverage reporter for Ruby.
                        Includes source code in the report.
                      -f=ut_coveralls_reporter
                        Generates a JSON coverage report providing information on code coverage with line numbers.
                        Designed for [Coveralls](https://coveralls.io/).
                      -f=ut_coverage_sonar_reporter
                        Generates a JSON coverage report providing information on code coverage with line numbers.
                        Designed for [SonarQube](https://about.sonarqube.com/) to report coverage.
                      -f=ut_sonar_test_reporter
                        Generates a JSON report providing detailed information on test execution.
                        Designed for [SonarQube](https://about.sonarqube.com/) to report test execution.

  -o=output         - Defines file name to save the output from the specified reporter.
                      If defined, the output is not displayed on screen by default. This can be changed with the -s parameter.
                      If not defined, then output will be displayed on screen, even if the parameter -s is not specified.
                      If more than one -o parameter is specified for one -f parameter, the last one is taken into consideration.
  -s                - Forces putting output to to screen for a given -f parameter.
  -source_path=path - Path to project source files. Used by coverage reporters. The path needs to be relative to the projects root directory.
  -test_path=path   - Path to unit test source files. Used by test reporters. The path needs to be relative to the projects root directory.
  -c                - If specified, enables printing of test results in colors as defined by ANSICONSOLE standards. 
                      Works only on reporeters that support colors (ut_documentation_reporter)
```

Parameters -f, -o, -s are correlated. That is parameters -o and -s are controlling outputs for reporter specified by the preceding -f parameter.

**Sonar and Coveralls reporter will only provide valid reports, when source_path and/or test_path are provided, and ut_run is executed from your project's root path.**

Examples:

`ut_run hr/hr@xe -p=hr_test -f=ut_documentation_reporter -o=run.log -s -f=ut_coverage_html_reporter -o=coverage.html -source_path=source`

Invokes all Unit tests from schema/package "hr_test" with two reporters:

- ut_documentation_reporter - will output to screen and save output to file "run.log"
- ut_coverage_html_reporter - will report **only** on database objects that are mapping to file structure from "source" folder and save output to file "coverage.html"


`ut_run hr/hr@xe`

Invokes all unit test suites from schema "hr".
Results are displayed to screen using default `ut_documentation_reporter`.

**Enabling color outputs on Windows**

To enable color outputs from SQLPlus on winddows you need to install an open-source utility called [ANSICON](http://adoxa.altervista.org/ansicon/)
