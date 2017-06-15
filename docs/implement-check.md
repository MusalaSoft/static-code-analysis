# Integrating Custom Check Into The Tool

The Static Analysis Tool provides the necessary infrastructure for custom check implementations using the API of FindBugs, Checkstyle and PMD. 

Helpful links when writing a custom check for the first time may be:

- for [Checkstyle](http://checkstyle.sourceforge.net/writingchecks.html);
- for [PMD](http://pmd.sourceforge.net/pmd-4.3.0/howtowritearule.html);
- for [FindBugs](https://www.ibm.com/developerworks/library/j-findbug2/).

In this guide we will use the Checkstyle API, because it is easy to use, supports different file extensions and languages. 

We assume that you have already installed the Eclipse IDE and Maven.

## Create an Eclipse project

After checking out the repository on your local machine execute `mvn eclipse:eclipse` in project root directory, this will generate an Eclipse project, containing the `.classpath` and `.project` files. You are ready to import the project in Eclipse.

```
	Hint! Depending on your Java installation the static-code-analysis project might not find tools.jar. Please make sure that the folder containing the tools.jar is included in the PATH variable (for Windows) and rerun mvn eclipse:eclipse.
```

## Write Custom Checks Using Checkstyle API

The first answer that you would have to answer before staring is what kind of files would you like to process:

- for **none .java** files extend the `org.openhab.tools.analysis.checkstyle.api.AbstractStaticCheck`. We have included there some helpful methods for processing different types of files and others, take a look at the javadoc for detailed information;
- for **.java** files you will most probably have to extend `com.puppycrawl.tools.checkstyle.api.AbstractCheck`.

And once again, please refer to the Checkstyle documentation for writing a check, if you haven't.

## Include Checks In The Ruleset

The next step is to integrate your check into the tool. You will have to add it to the ruleset. The ruleset location for Checkstyle is in the `src/main/resources/rulesets/checkstyle` folder.

You will have to consider several things before adding your check there.

- you can add your check as a child of the `Checker` module or as a child of the `TreeWalker` module. If you are extending `com.puppycrawl.tools.checkstyle.api.AbstractCheck`, you should choose `TreeWalker`, otherwise `Checker`;
- add a severity property for your check. Keep in mind that a severity of `error` will brake the build if a problem is found by your check, so use this wisely;
- if you have some others configuration properties in your check, include them as well. 

## Add Tests For The New Checks

You can easily test your custom rules for Checkstyle.

In order to add a new test for Checkstyle you have to do two things:

- Create a test class in the `src/test/java` folder that extends `org.openhab.tools.analysis.checkstyle.api.AbstractStaticCheckTest`;
- Add a test files in `src/test/resources/checks/checkstyle/NAME_OF_YOUR_CHECK` that contain the code to be tested.

Just a few notes how is testing working in Checkstyle. A single error message generated by your check includes:
- path to the file, where the problem is found;
- line in the file;
- message that should help the user to identify the problem.

In order to test your check the testing framework expects that you provide an example collection of error messages as described above and compares them with the messages generated by your check. You can achieve that by calling some of the `com.puppycrawl.tools.checkstyle.BaseCheckTestSupport` verify(...) methods.

## Execute The Check On openHAB Addons Repository

We would highly recommend this step. The static code analysis tool is used in the `openhab2-addons` build. 

This is a two step process. Firstly execute `mvn clean install` from the root of the `static-code-analysis` repository. This will install the artifact into your local Maven repository. Please note that this is a snapshot version (e.g. x.x.x-SNAPSHOT).

The second step is to execute `mvn clean install -Dsat.version=x.x.x-SNAPSHOT` from the root of the `openhab2-addons` repository.

Take a look at the log as it is described in the [openHAB documentation](http://docs.openhab.org/developers/development/bindings.html#static-code-analysis).

## Still need additional help ?

Take a look at the existing checks and don't hesitate to ask in the [issues page](https://github.com/openhab/static-code-analysis/issues).