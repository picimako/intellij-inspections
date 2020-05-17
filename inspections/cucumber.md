# Cucumber

- Cucumber website: https://cucumber.io
- Cucumber on GitHub: https://github.com/cucumber/cucumber-jvm

## Licensing
Please let me know if using the same exact exception messages as the descriptions of the inspections is not permitted due to licensing issues.
I am happy to change them, it just made sense, and I think it is clearer to use the same messages here too.

## General information

Package naming was updated with [Cucumber 4.5.0](https://github.com/cucumber/cucumber-jvm/blob/master/CHANGELOG.md#450-2019-06-30),
but the inspection below have support for 2 or 3 different packaging structures where possible and was not forgot about to update.

## When a hook declares an argument it must be of type Scenario

This is based on one of the exception handling happening in [`cucumber.runtime.java.JavaHookDefinition`](https://github.com/cucumber/cucumber-jvm/blob/352a62e01ee66088baf548864cf067bdc7c7af11/java/src/main/java/cucumber/runtime/java/JavaHookDefinition.java).

Cucumber doesn't allow any other `Scenario` types as hook method arguments other than from the packages `cucumber.api` and `io.cucumber.core.api`. This applies to not just
scenario hooks but step hooks as well. 

| Compliant code | Non-compliant code |
|---|---|
| <pre>@Before<br>public void beforeScenario(Scenario scenario) {<br>}</pre> | <pre>@Before<br>public void beforeScenario(TestCase testCase) {<br>}</pre> |

It supports tagged hooks and all of the hook annotations: `@Before`, `@BeforeStep`, `@After`, `@AfterStep`.

**Template:**

```xml
<searchConfiguration name="When a hook declares an argument it must be of type cucumber.api.Scenario/io.cucumber.core.api.Scenario." text="@$HookAnnotation$&#10;$ReturnType$ $hookMethod$($HookType$ $parameter$) ;" recursive="true" caseInsensitive="true" type="JAVA">
	<constraint name="__context__" within="" contains="" />
	<constraint name="HookAnnotation" regexp="(cucumber\.api\.java\|io\.cucumber\.java)\.(Before|BeforeStep|After|AfterStep)" maxCount="2147483647" within="" contains="" />
	<constraint name="ReturnType" within="" contains="" />
	<constraint name="hookMethod" within="" contains="" />
	<constraint name="HookType" regexp="(cucumber\.api|io\.cucumber\.core\.api)\.Scenario" target="true" negateName="true" within="" contains="" />
	<constraint name="parameter" within="" contains="" />
</searchConfiguration>
```

## Hooks must declare 0 or 1 arguments

This is based on one of the exception handling happening in [`cucumber.runtime.java.JavaHookDefinition`](https://github.com/cucumber/cucumber-jvm/blob/352a62e01ee66088baf548864cf067bdc7c7af11/java/src/main/java/cucumber/runtime/java/JavaHookDefinition.java).

Cucumber doesn't allow more than 1 argument for hook methods, which in fact wouldn't make much sense because you can only run a hook for a single scenario or step but not multiple ones
with the same hooks method at the same time.

| Compliant code | Non-compliant code |
|---|---|
| <pre>@BeforeStep<br>public void beforeStep(Scenario scenario) {<br>}</pre> | <pre>@BeforeStep<br>public void beforeStep(Scenario scenario, TestCase testCase) {<br>}</pre> |
| <pre>@BeforeStep<br>public void beforeStep() {<br>}</pre> |  |

It supports tagged hooks and all of the hook annotations: `@Before`, `@BeforeStep`, `@After`, `@AfterStep`.

**Template:**

```xml
<searchConfiguration name="Hooks must declare 0 or 1 arguments." text="@$HookAnnotation$&#10;$ReturnType$ $hookMethod$($HookType$ $parameter$) ;" recursive="true" caseInsensitive="true" type="JAVA">
    <constraint name="__context__" within="" contains="" />
    <constraint name="HookAnnotation" regexp="(cucumber\.api\.java\|io\.cucumber\.java)\.(Before|BeforeStep|After|AfterStep)" maxCount="2147483647" within="" contains="" />
    <constraint name="ReturnType" within="" contains="" />
    <constraint name="hookMethod" target="true" within="" contains="" />
    <constraint name="HookType" negateName="true" within="" contains="" />
    <constraint name="parameter" minCount="2" maxCount="2147483647" within="" contains="" />
</searchConfiguration>
```

## You must supply an output argument to certain plugins.

This is based on one of the exception handling happening in [`cucumber.runtime.formatter.PluginFactory`](https://github.com/cucumber/cucumber-jvm/blob/311c6f8e8ba21526abdbac6f27295551421a1d6f/core/src/main/java/cucumber/runtime/formatter/PluginFactory.java).

This inspection would signal a code snippet like the following, as incorrect (it is being highlighted regardless of the position of the the **plugin** attribute).

| Compliant code | Non-compliant code |
|---|---|
| <pre>@CucumberOptions(plugin = "html:output")</pre> | <pre>@CucumberOptions(plugin = "html")</pre> |
| <pre>@CucumberOptions(plugin = "junit:output")</pre> | <pre>@CucumberOptions(plugin = "junit")</pre> |

**Script filter ($Class$):**

```groovy
!__context__.interface && !__context__.enum
```

**Template (html):**

```xml
<searchConfiguration name="You must supply an output argument to html. Like so: html:output" text="@$CucumberOptions$($plugin$ = &quot;$html$&quot;)&#10;class $Class$ {}" recursive="true" caseInsensitive="true" type="JAVA" pattern_context="default">
    <constraint name="__context__" within="" contains="" />
    <constraint name="Class" script="&quot;!__context__.interface &amp;&amp; !__context__.enum&quot;" within="" contains="" />
    <constraint name="plugin" regexp="plugin" within="" contains="" />
    <constraint name="CucumberOptions" regexp="(cucumber\.api|io\.cucumber\.junit|io\.cucumber\.testng)\.CucumberOptions" within="" contains="" />
    <constraint name="html" regexp="^html$" target="true" within="" contains="" />
</searchConfiguration>
```

**Template (junit):**

```xml
<searchConfiguration name="You must supply an output argument to html. Like so: junit:output" text="@$CucumberOptions$($plugin$ = &quot;$junit$&quot;)&#10;class $Class$ {}" recursive="true" caseInsensitive="true" type="JAVA" pattern_context="default">
    <constraint name="__context__" within="" contains="" />
    <constraint name="Class" script="&quot;!__context__.interface &amp;&amp; !__context__.enum&quot;" within="" contains="" />
    <constraint name="plugin" regexp="plugin" within="" contains="" />
    <constraint name="CucumberOptions" regexp="(cucumber\.api|io\.cucumber\.junit|io\.cucumber\.testng)\.CucumberOptions" within="" contains="" />
    <constraint name="junit" regexp="^junit$" target="true" within="" contains="" />
</searchConfiguration>
```

## @CucumberOptions glue and extraGlue attributes cannot be specified at the same time

This is based on one of the exception handling happening in [`cucumber.runtime.RuntimeOptionsFactory`](https://github.com/cucumber/cucumber-jvm/blob/7a262f077a894367a4a62eb39f1d31d57351923c/core/src/main/java/cucumber/runtime/RuntimeOptionsFactory.java).

Additional details about the purpose of extraGlue may be found in the [cucumber-jvm/#1438 GitHub issue](https://github.com/cucumber/cucumber-jvm/issues/1438).

| Compliant code | Non-compliant code |
|---|---|
| <pre>@CucumberOptions(tags = "@tag", glue = "com.project.stepdefs")</pre> | <pre>@CucumberOptions(tags = "@tag", glue = "com.project.stepdefs", extraGlue = "com.project.otherthings")</pre> |
| <pre>@CucumberOptions(tags = "@tag", extraGlue = "com.project.otherthings")</pre> |  |

```groovy
!__context__.interface && !__context__.enum
```

**Template:**

```xml
<searchConfiguration name="glue and extraGlue cannot be specified at the same time" text="@$CucumberOptions$(glue = &quot;$glue$&quot;, extraGlue = &quot;$extraGlue$&quot;)&#10;class $Class$ {}" recursive="true" caseInsensitive="true" type="JAVA">
    <constraint name="__context__" within="" contains="" />
    <constraint name="Class" script="&quot;!__context__.interface &amp;&amp; !__context__.enum&quot;" within="" contains="" />
    <constraint name="glue" within="" contains="" />
    <constraint name="extraGlue" within="" contains="" />
    <constraint name="CucumberOptions" regexp="(cucumber\.api|io\.cucumber\.junit|io\.cucumber\.testng)\.CucumberOptions" maxCount="2147483647" target="true" within="" contains="" />
</searchConfiguration>
```

## Classes annotated with @RunWith(Cucumber.class) must not define any Step Definition or Hook methods

This is based on one of the exception handling happening in [`cucumber.runtime.junit.Assertions`](https://github.com/cucumber/cucumber-jvm/blob/e471442b0bd6a6d263bbcac03c8079107a36d84d/junit/src/main/java/cucumber/runtime/junit/Assertions.java).

To see the reason why this validation is put in place, please head to the referenced class, and see the error message provided there.

| Compliant code | Non-compliant code |
|---|---|
| <pre>@RunWith(Cucumber.class)<br>public class JUnitTest {<br>    //Without hooks and step definitions<br>}</pre> | <pre>@RunWith(Cucumber.class)<br>public class JUnitTest {<br>    @When("the page should be loaded")<br>    public void the_page_should_be_loaded() {<br>    }<br>}</pre> |
| <pre></pre> | <pre>@RunWith(Cucumber.class)<br>public class JUnitTest {<br>    @Before<br>    public void beforeScenario(Scenario scenario) {<br>    }<br>}</pre> |

**Supported annotations:**
- **Hooks (including tagged hooks):** Before, BeforeStep, After, AfterStep
- **Steps:** Given, When, Then, And, But

**Template:**

```xml
<searchConfiguration name="Classes annotated with @RunWith(Cucumber.class) must not define any Step Definition or Hook methods" text="@org.junit.runner.RunWith(cucumber.api.junit.Cucumber.class)&#10;public class $Class$ {&#10;&#10;    @$HookOrStepDefAnnotation$&#10;    public void $hookOrStepDefMethod$($Parameter$ $param$) {}&#10;}" recursive="true" caseInsensitive="true" type="JAVA">
    <constraint name="__context__" within="" contains="" />
    <constraint name="Class" within="" contains="" />
    <constraint name="HookOrStepDefAnnotation" regexp="(cucumber\.api\.java\|io\.cucumber\.java)\.((en\.(Given|When|Then|And|But))|(Before|BeforeStep|After|AfterStep))" maxCount="2147483647" target="true" within="" contains="" />
    <constraint name="hookOrStepDefMethod" maxCount="2147483647" within="" contains="" />
    <constraint name="Parameter" within="" contains="" />
    <constraint name="param" minCount="0" maxCount="2147483647" within="" contains="" />
</searchConfiguration>
```

## JUnit @Rule annotated fields and methods have no effect on JUnit classes with @RunWith(Cucumber.class) annotation

JUnit's `@Rule` annotation, whether it is applied to a field or a method, [is not processed by the `Cucumber` JUnit runner](https://github.com/cucumber/cucumber-jvm/tree/master/junit#using-junit-rules),
having them in a JUnit test class doesn't have any effect.

Although a `@Rule` annotated field or method must be a subtype of *org.junit.rules.TestRule (preferred) or @link org.junit.rules.MethodRule* (according to the javadoc),
this template doesn't enforce it because regardless of what is annotated is as `@Rule`, it won't have any effect and (at least the annotation) should be removed.

| Compliant code | Non-compliant code |
|---|---|
| <pre>@RunWith(Cucumber.class)<br>public class CucumberJUnitTestClass {<br>}</pre> | <pre>@RunWith(Cucumber.class)<br>public class CucumberJUnitTestClass {<br><br>    @Rule<br>    public TemporaryFolder temporaryFolder = new TemporaryFolder();<br>}</pre> |
|  | <pre>@RunWith(Cucumber.class)<br>public class CucumberJUnitTestClass {<br><br>    public TemporaryFolder temporaryFolder = new TemporaryFolder();<br><br>    @Rule<br>    public TemporaryFolder getFolder() {<br>        return temporaryFolder;<br>    }<br>}</pre> |

There are three templates:
- the first two validates fields and methods separately, and highlights the `@Rule` annotation. These two should be used together.
- the third one validates both fields and methods but highlights the test class name. This can be used in itself.

Depending on what part of the code you would like to get highlighted, you can use either the first two or the third template.

| Target | Template text | Comment |
|---|---|---|
| fields | <pre>@org.junit.runner.RunWith(io.cucumber.junit.Cucumber.class)<br>class $Class$ {<br><br>    @$Rule$<br>    $RuleType$ $ruleName$ = $Init$;<br>}</pre> | All occurrences of the @Rule annotation are highlighted. |
| methods | <pre>@org.junit.runner.RunWith(io.cucumber.junit.Cucumber.class)<br>class $Class$ {<br><br>    @$Rule$<br>    $RuleType$ $getRule$(){<br>        $body$;<br>    }<br>}</pre> | All occurrences of the @Rule annotation are highlighted. |
| field and methods | <pre>@org.junit.runner.RunWith(io.cucumber.junit.Cucumber.class)<br>class $JUnitTestClass$ {<br>}</pre> | See Script filter below. |

**Template for fields only (Search target: the `@Rule` annotation):**
```xml
<searchConfiguration name="JUnit @Rule annotated fields have no effect on JUnit classes with @RunWith(Cucumber.class) annotation" text="@org.junit.runner.RunWith(io.cucumber.junit.Cucumber.class)&#10;class $Class$ {&#10;&#10;    @$Rule$&#10;    $RuleType$ $ruleName$ = $Init$;&#10;}" recursive="true" caseInsensitive="false" type="JAVA" pattern_context="default">
    <constraint name="__context__" within="" contains="" />
    <constraint name="Rule" regexp="org\.junit\.Rule" target="true" within="" contains="" />
    <constraint name="Class" within="" contains="" />
    <constraint name="RuleType" within="" contains="" />
    <constraint name="ruleName" maxCount="2147483647" within="" contains="" />
    <constraint name="Init" minCount="0" within="" contains="" />
</searchConfiguration>
```

**Template for methods only (Search target: the `@Rule` annotation):**
```xml
<searchConfiguration name="JUnit @Rule annotated methods have no effect on JUnit classes with @RunWith(Cucumber.class) annotation" text="@org.junit.runner.RunWith(io.cucumber.junit.Cucumber.class)&#10;class $Class$ {&#10;&#10;    @$Rule$&#10;    $RuleType$ $getRule$(){&#10;        $body$;&#10;    }&#10;}" recursive="true" caseInsensitive="false" type="JAVA" pattern_context="default">
    <constraint name="__context__" within="" contains="" />
    <constraint name="Rule" regexp="org\.junit\.Rule" target="true" within="" contains="" />
    <constraint name="Class" within="" contains="" />
    <constraint name="RuleType" within="" contains="" />
    <constraint name="getRule" maxCount="2147483647" within="" contains="" />
    <constraint name="body" within="" contains="" />
</searchConfiguration>
```

**Template for both (Search target: the class name):**
```xml
<searchConfiguration name="JUnit @Rule annotated fields and methods have no effect on JUnit classes with @RunWith(Cucumber.class) annotation" text="@org.junit.runner.RunWith(io.cucumber.junit.Cucumber.class)&#10;class $JUnitTestClass$ {&#10;}" recursive="true" caseInsensitive="false" type="JAVA" pattern_context="default">
    <constraint name="__context__" script="&quot;return hasRuleField() || hasRuleMethod()&#10;&#10;boolean hasRuleField() {&#10;&#9;def fields = JUnitTestClass.getFields()&#10;&#9;fields.find { it.hasAnnotation(&quot;org.junit.Rule&quot;) }&#10;}&#10;&#10;boolean hasRuleMethod() {&#10;&#9;def methods = JUnitTestClass.getMethods()&#10;&#9;methods.find { it.hasAnnotation(&quot;org.junit.Rule&quot;) }&#10;}&quot;" within="" contains="" />
    <constraint name="JUnitTestClass" target="true" within="" contains="" />
</searchConfiguration>
```

**Script filter - fields and methods (Complete Match):**
```groovy
return hasRuleField() || hasRuleMethod()

boolean hasRuleField() {
	def fields = JUnitTestClass.getFields()
	fields.find { it.hasAnnotation("org.junit.Rule") }
}

boolean hasRuleMethod() {
	def methods = JUnitTestClass.getMethods()
	methods.find { it.hasAnnotation("org.junit.Rule") }
}
```

## Single Cucumber tag expression should start with an @ character.

Based on the official documentation of the [Cucumber Tag Expressions](https://cucumber.io/docs/cucumber/api/#tag-expressions), tags in a tag expression
must be defined with the `@` character, so when only a single tag is defined in the `tag` attribute of the `@CucumberOptions` annotation
the value of that attribute must start with `@` otherwise Cucumber won't be able to filter by that expression.

| Compliant code | Non-compliant code |
|---|---|
| <pre>@CucumberOptions(tags = "@sometag")</pre> | <pre>@CucumberOptions(tags = "sometag")</pre> |

This inspection supports this annotation from the following packages:
- `cucumber.api` (deprecated in Cucumber-JVM 4.5.0)
- `io.cucumber.junit`
- `io.cucumber.testng`

**Script filter ($TAG_EXPRESSION$)**

```groovy
!TAG_EXPRESSION?.value?.contains(" ") && !TAG_EXPRESSION?.value?.startsWith("@") 
```

**Template:**

```xml
<searchConfiguration name="Single Cucumber tag expression should start with an @ character." text="@$CucumberOptions$(tags = &quot;$TAG_EXPRESSION$&quot;)&#10;class $Class$ {&#10;}" recursive="true" caseInsensitive="true" type="JAVA" pattern_context="default">
    <constraint name="__context__" within="" contains="" />
    <constraint name="TAG_EXPRESSION" script="&quot;!TAG_EXPRESSION?.value?.contains(&quot; &quot;) &amp;&amp; !TAG_EXPRESSION?.value?.startsWith(&quot;@&quot;) &quot;" target="true" within="" contains="" />
    <constraint name="Class" within="" contains="" />
    <constraint name="CucumberOptions" regexp="(cucumber\.api|io\.cucumber\.junit|io\.cucumber\.testng)\.CucumberOptions" within="" contains="" />
</searchConfiguration>
```

## Single Cucumber tag expression within a tagged hook annotation should start with an @ character.

Based on the official documentation of the [Cucumber Tag Expressions](https://cucumber.io/docs/cucumber/api/#tag-expressions), tags in a tag expression
must be defined with the `@` character, so when only a single tag is defined as the value of a tagged hook annotation
that value must start with `@` otherwise Cucumber won't be able to filter by that expression.

| Compliant code | Non-compliant code |
|---|---|
| <pre>@After("@sometag")</pre> | <pre>@After("sometag")</pre> |
| <pre>@Before("@sometag")</pre> | <pre>@Before("sometag")</pre> |

This inspection supports annotations from the following packages:
- `cucumber.api.java` (deprecated in Cucumber-JVM 4.5.0)
- `io.cucumber.java`

Supported hook annotations: `@Before`, `@BeforeStep`, `@After`, `@AfterStep`

**Script filter ($TAG_EXPRESSION$)**

```groovy
!pattern?.value?.contains(" ") && !pattern?.value?.startsWith("@") 
```

**Template:**

```xml
<searchConfiguration name="Single Cucumber tag expression in a tagged hook annotation should start with an @ character." text="class $Class$ {&#10;    @$HookAnnotation$($pattern$)&#10;    $ReturnType$ $Method$($ParamType$ $param$);&#10;}" recursive="true" caseInsensitive="true" type="JAVA" pattern_context="default">
    <constraint name="__context__" within="" contains="" />
    <constraint name="Class" within="" contains="" />
    <constraint name="Method" maxCount="2147483647" within="" contains="" />
    <constraint name="ReturnType" within="" contains="" />
    <constraint name="param" minCount="0" maxCount="2147483647" within="" contains="" />
    <constraint name="ParamType" within="" contains="" />
    <constraint name="pattern" script="&quot;!pattern?.value?.contains(&quot; &quot;) &amp;&amp; !pattern?.value?.startsWith(&quot;@&quot;) &quot;" target="true" within="" contains="" />
    <constraint name="HookAnnotation" regexp="(cucumber\.api\.java|io\.cucumber\.java)\.(Before|BeforeStep|After|AfterStep)" maxCount="2147483647" within="" contains="" />
</searchConfiguration>
```

## Step pattern contains incomplete parameter placeholder

Cucumber has its own expression language and elements for step patterns which includes parameter type placeholders like `{string}`. There are built-in ones but custom ones may be
implemented as well. See official documentation here: [Cucumber expression](https://cucumber.io/docs/cucumber/cucumber-expressions/).

However if one makes a mistake and misses the starting or closing curly brace of these placeholders then Cucumber will fail to parse that step properly.

| Compliant code | Non-compliant code |
|---|---|
| Original format:
| <pre>@When("I have {int} apples")<br>public void i_have_X_apples(int numberOfApples) {<br>}</pre> | <pre>@When("I have {int apples")<br>public void i_have_X_apples(int numberOfApples) {<br>}</pre> |
|  | <pre>@When("I have int} apples")<br>public void i_have_X_apples(int numberOfApples) {<br>}</pre> |
| Java8 format: |  |
| <pre>this.Given("I have {int} apples", () -> {});</pre> | <pre>this.Given("I have {int apples", () -> {});</pre> |
|  | <pre>this.Given("I have int} apples", () -> {});</pre> |

Regardless of the position and number of placeholders the inspection will mark the pattern incorrect if it finds at least one incorrect placeholder.

This inspection supports all the built-in placeholders, except `{}`, and the `@Given`, `@When`, `@Then`, `@And` and `@But` annotations from the `cucumber.api.java.en`
and `io.cucumber.java.en` packages.

It ignores values where the `{` character is escaped (`\\{`), it is not considered as the beginning of a placeholder.

To alter/extend the support for other step annotations one has to change the regexp of the `StepAnnotation` template variable.

To have your own custom placeholders supported add them to the `parameterTypes` variable in the script filter of the `stepPattern` template variable.

**Script filter ($stepPattern$)**

The matches check at the beginning is added to decrease the performance impact of the script, and execute only when there is an actual (not-escaped)
placeholder in the pattern.
```groovy
if (stepPattern?.value?.matches('.*(?<!\\\\)\\{.*')) {
    def parameterTypes = "(int|float|word|string|biginteger|bigdecimal|byte|short|long|double)"
    def incompleteStepPattern = "(?<!\\\\)\\{" + parameterTypes + "(?![a-zA-Z\\}])|(?<![a-zA-Z\\{])" + parameterTypes + "\\}"
    def matcher = stepPattern?.value =~ incompleteStepPattern
    return matcher ? true : false
}
false
```

**Template:**

```xml
<searchConfiguration name="Step pattern contains incomplete parameter placeholder." text="@$StepAnnotation$(&quot;$stepPattern$&quot;)&#10;void $stepDefinitionMethod$($ParameterType$ $parameter$);" recursive="true" caseInsensitive="true" type="JAVA" pattern_context="member">
    <constraint name="__context__" within="" contains="" />
    <constraint name="stepPattern" script="&quot;if (stepPattern?.value?.matches('.*(?&lt;!\\\\)\\{.*')) {&#10;&#9;def parameterTypes = &quot;(int|float|word|string|biginteger|bigdecimal|byte|short|long|double)&quot;&#10;&#9;def incompleteStepPattern = &quot;(?&lt;!\\\\)\\{&quot; + parameterTypes + &quot;(?![a-zA-Z\\}])|(?&lt;![a-zA-Z\\{])&quot; + parameterTypes + &quot;\\}&quot;&#10;&#9;def matcher = stepPattern?.value =~ incompleteStepPattern&#10;&#9;return matcher ? true : false&#10;}&#10;false&quot;" target="true" within="" contains="" />
    <constraint name="stepDefinitionMethod" within="" contains="" />
    <constraint name="ParameterType" within="" contains="" />
    <constraint name="parameter" minCount="0" maxCount="2147483647" within="" contains="" />
    <constraint name="StepAnnotation" regexp="(cucumber\.api\.java|io\.cucumber\.java)\.(en\.(Given|When|Then|And|But))" within="" contains="" />
</searchConfiguration>
```

### Java8 format support

If you are using Java8 type step definitions the following inspection can detect problems in that form:

**Script filter ($Step$) - $stepPattern$ is unchanged**

```groovy
Step.resolveMethod().getContainingClass().getQualifiedName().matches("(cucumber\\.api\\.java8\\.|io\\.cucumber\\.java8\\.).*")
```

```xml
<searchConfiguration name="Step pattern contains incomplete parameter placeholder." text="$Java8BaseStepDefInterface$.$Step$(&quot;$stepPattern$&quot;, ($Parameters$) -&gt; {});" recursive="true" caseInsensitive="true" type="JAVA" pattern_context="default">
    <constraint name="__context__" within="" contains="" />
    <constraint name="stepPattern" script="&quot;if (stepPattern?.value?.matches('.*(?&lt;!\\\\)\\{.*')) {&#10;&#9;def parameterTypes = &quot;(int|float|word|string|biginteger|bigdecimal|byte|short|long|double)&quot;&#10;&#9;def incompleteStepPattern = &quot;(?&lt;!\\\\)\\{&quot; + parameterTypes + &quot;(?![a-zA-Z\\}])|(?&lt;![a-zA-Z\\{])&quot; + parameterTypes + &quot;\\}&quot;&#10;&#9;def matcher = stepPattern?.value =~ incompleteStepPattern&#10;&#9;return matcher ? true : false&#10;}&#10;false&quot;" target="true" within="" contains="" />
    <constraint name="Step" script="&quot;Step.resolveMethod().getContainingClass().getQualifiedName().matches(&quot;(cucumber\\.api\\.java8\\.|io\\.cucumber\\.java8\\.).*&quot;)&quot;" regexp="Given|When|Then|And|But" within="" contains="" />
    <constraint name="Parameters" minCount="0" maxCount="2147483647" within="" contains="" />
    <constraint name="Java8BaseStepDefInterface" minCount="0" within="" contains="" />
</searchConfiguration>
```

## Step pattern contains unregistered parameter type

During writing step patterns one may make a mistake and have a typo in the name of the parameter type placeholders which Cucumber won't recognize, which it will only find out when the related test is executed.

| Compliant code | Non-compliant code |
|---|---|
| Original format: |   |
| <pre>@When("I have {string} apples")<br>public void i_have_X_apples(String quality) {<br>}</pre> | <pre>@When("I have {Strng} apples")<br>public void i_have_X_apples(String quality) {<br>}</pre> |
| Java8 format: |  |
| <pre>this.Given("I have {string} apples", () -> {});</pre> | <pre>this.Given("I have {Strng} apples", () -> {});</pre> |

Regardless of the position and number of placeholders the inspection will mark the pattern incorrect if it finds at least one unregistered placeholder.

This inspection supports all the built-in placeholders, including `{}`, and the `@Given`, `@When`, `@Then`, `@And` and `@But` annotations from the `cucumber.api.java.en` package.

It ignores values where the `{` character is escaped (`\\{`), it is not considered as the beginning of a placeholder.

To alter/extend the support for other step annotations one has to change the regexp of the `StepAnnotation` template variable.

To have your own custom placeholders supported add them to the `parameterTypes` list in the script filter of the `stepPattern` template variable.

**Script filter ($stepPattern$)**

The matches check at the beginning is added to decrease the performance impact of the script, and execute only when there is an actual (not-escaped)
placeholder in the pattern.
```groovy
if (stepPattern?.value?.matches('.*(?<!\\\\)\\{.*')) {
    def placeholderPattern = /(?<!\){((?:[^{])*)}/
    def parameterTypes = ['', 'int', 'float', 'word', 'string', 'biginteger', 'bigdecimal', 'byte', 'short', 'long', 'double']
    def matcher = stepPattern?.value =~ placeholderPattern

    //if there is at least one unregistered placeholder, then signal true
    def hasAtLeastOneUnregisteredPlaceholder = false
    while(matcher.find()) {
        def type = matcher.group(1)
        if (!parameterTypes.contains(type)) {
            hasAtLeastOneUnregisteredPlaceholder = true
            break;
        }
    }
    return hasAtLeastOneUnregisteredPlaceholder

}
false
```

**Template:**

```xml
<searchConfiguration name="Step pattern contains unregistered parameter type." text="@$StepAnnotation$(&quot;$stepPattern$&quot;)&#10;void $stepDefinitionMethod$($ParameterType$ $parameter$);" recursive="true" caseInsensitive="true" type="JAVA" pattern_context="member">
    <constraint name="__context__" within="" contains="" />
    <constraint name="stepPattern" script="&quot;if (stepPattern?.value?.matches('.*(?&lt;!\\\\)\\{.*')) {&#10;&#9;def placeholderPattern = /(?&lt;!\\)\{((?:[^{])*)\}/&#10;&#9;def parameterTypes = ['', 'int', 'float', 'word', 'string', 'biginteger', 'bigdecimal', 'byte', 'short', 'long', 'double']&#10;&#9;def matcher = stepPattern?.value =~ placeholderPattern&#10;&#10;&#9;//if there is at least one unregistered placeholder, then signal true&#10;&#9;def hasAtLeastOneUnregisteredPlaceholder = false&#10;&#9;while(matcher.find()) {&#10;&#9;&#9;def type = matcher.group(1)&#10;&#9;    if (!parameterTypes.contains(type)) {&#10;&#9;    &#9;hasAtLeastOneUnregisteredPlaceholder = true&#10;&#9;    &#9;break;&#10;&#9;    }&#10;&#9;}&#10;&#9;return hasAtLeastOneUnregisteredPlaceholder&#10;}&#10;false&quot;" target="true" within="" contains="" />
    <constraint name="stepDefinitionMethod" within="" contains="" />
    <constraint name="ParameterType" within="" contains="" />
    <constraint name="parameter" minCount="0" maxCount="2147483647" within="" contains="" />
    <constraint name="StepAnnotation" regexp="(cucumber\.api\.java|io\.cucumber\.java)\.(en\.(Given|When|Then|And|But))" within="" contains="" />
</searchConfiguration>  
```

### Java8 format support

If you are using Java8 type step definitions the following inspection can detect problems in that form:

**Script filter ($Step$) - $stepPattern$ is unchanged**

```groovy
Step.resolveMethod().getContainingClass().getQualifiedName().matches("(cucumber\\.api\\.java8\\.|io\\.cucumber\\.java8\\.).*")
```

```xml
<searchConfiguration name="Step pattern contains unregistered parameter type." text="$Java8BaseStepDefInterface$.$Step$(&quot;$stepPattern$&quot;, ($Parameters$) -&gt; {});" recursive="true" caseInsensitive="true" type="JAVA" pattern_context="default">
    <constraint name="__context__" within="" contains="" />
    <constraint name="stepPattern" script="&quot;if (stepPattern?.value?.matches('.*(?&lt;!\\\\)\\{.*')) {&#10;&#9;def placeholderPattern = /(?&lt;!\\)\{((?:[^{])*)\}/&#10;&#9;def parameterTypes = ['', 'int', 'float', 'word', 'string', 'biginteger', 'bigdecimal', 'byte', 'short', 'long', 'double']&#10;&#9;def matcher = stepPattern?.value =~ placeholderPattern&#10;&#10;&#9;//if there is at least one unregistered placeholder, then signal true&#10;&#9;def hasAtLeastOneUnregisteredPlaceholder = false&#10;&#9;while(matcher.find()) {&#10;&#9;&#9;def type = matcher.group(1)&#10;&#9;    if (!parameterTypes.contains(type)) {&#10;&#9;    &#9;hasAtLeastOneUnregisteredPlaceholder = true&#10;&#9;    &#9;break;&#10;&#9;    }&#10;&#9;}&#10;&#9;return hasAtLeastOneUnregisteredPlaceholder&#10;}&#10;false&quot;" target="true" within="" contains="" />
    <constraint name="Step" script="&quot;Step.resolveMethod().getContainingClass().getQualifiedName().matches(&quot;(cucumber\\.api\\.java8\\.|io\\.cucumber\\.java8\\.).*&quot;)&quot;" regexp="Given|When|Then|And|But" within="" contains="" />
    <constraint name="Parameters" minCount="0" maxCount="2147483647" within="" contains="" />
    <constraint name="Java8BaseStepDefInterface" minCount="0" within="" contains="" />
</searchConfiguration>
```

## You're not allowed to extend classes that define Step Definitions or hooks.

This is based on the specific exception type: [`InvalidMethodException`](https://github.com/cucumber/cucumber-jvm/blob/1f9085426716acb26414acdb010d3fbb06b9f364/java/src/main/java/io/cucumber/java/InvalidMethodException.java)

For the reason why Step Definitions and Hooks classes are not allowed to be extended, see Aslak Hellesøy's answer in [this Google Groups discussion](https://groups.google.com/forum/#!topic/cukes/ke7MhnjqQGQ).

| Compliant code | Non-compliant code |
|---|---|
| <pre>public class HomepageSteps {<br>    @Inject //use the DI framework of your choice<br>    HeroImageSteps heroImageSteps;<br>}</pre> | <pre>public class HomepageSteps extends HeroImageSteps {<br>}</pre> |

THe highlighted part in this case is `CommonSteps`.

I found two different ways that this can be validated. Below you can find them.

### Validating if the name of the superclass ends with a certain keyword

This is a very simple check and it doesn't validate the existence of actual Step Definitions and hooks, but may be useful
when the step definitions classes are always named in a certain way, for instance they always end with `Steps`.  

**Template:**

```xml
<searchConfiguration name="Step definition classes are not allowed to be extended." text="class $Class$ extends $Parent$ {}" recursive="true" caseInsensitive="true" type="JAVA" pattern_context="default">
    <constraint name="__context__" within="" contains="" />
    <constraint name="Class" target="true" within="" contains="" />
    <constraint name="Parent" regexp=".*Steps$" within="" contains="" />
</searchConfiguration>
```

### Validating whether there is a step or hook annotated class among the declared methods of this class

This is the more strict than the previous inspection because it checks for the existence of actual step and hook annotations, and stops at the
first one if it comes across one.

It may also happen that a class named as a step definition might not contain step definition methods or hooks at all
but in that case the class might need to be renamed to something more appropriate.

**Script filter (Complete match)**

```groovy
import com.intellij.psi.*

def stepAnnotations = ["cucumber.api.java.en.Given", "cucumber.api.java.en.When", "cucumber.api.java.en.Then",
    "cucumber.api.java.en.And", "cucumber.api.java.en.But", "io.cucumber.java.en.Given", "io.cucumber.java.en.When", "io.cucumber.java.en.Then",
    "io.cucumber.java.en.And", "io.cucumber.java.en.But"]
PsiElement superclass = Parent.resolve();
if (superclass instanceof PsiClass) {
	for (PsiMethod method : superclass.getMethods()) {
		for (PsiAnnotation methodAnnotation : method.getAnnotations()) {
			if (stepAnnotations.contains(methodAnnotation.getQualifiedName())) {
				return true
			}
		}
	}
}
false
```

The list of validated annotations may be extended or changed entirely in the list called `stepAnnotations` to suit custom needs.

**Template:**

```xml
<searchConfiguration name="Classes defining Step Definitions or hooks are not allowed to be extended." text="class $Class$ extends $Parent$ {}" recursive="true" caseInsensitive="true" type="JAVA" pattern_context="default">
    <constraint name="__context__" script="&quot;import com.intellij.psi.*&#10;&#10;def stepAnnotations = [&quot;cucumber.api.java.en.Given&quot;, &quot;cucumber.api.java.en.When&quot;, &quot;cucumber.api.java.en.Then&quot;,&quot;cucumber.api.java.en.And&quot;, &quot;cucumber.api.java.en.But&quot;, &quot;io.cucumber.java.en.Given&quot;, &quot;io.cucumber.java.en.When&quot;, &quot;io.cucumber.java.en.Then&quot;, &quot;io.cucumber.java.en.And&quot;, &quot;io.cucumber.java.en.But&quot;]&#10;PsiElement superclass = Parent.resolve();&#10;if (superclass instanceof PsiClass) {&#10;&#9;for (PsiMethod method : superclass.getMethods()) {&#10;&#9;&#9;for (PsiAnnotation methodAnnotation : method.getAnnotations()) {&#10;&#9;&#9;&#9;if (stepAnnotations.contains(methodAnnotation.getQualifiedName())) {&#10;&#9;&#9;&#9;&#9;return true&#10;&#9;&#9;&#9;}&#10;&#9;&#9;}&#10;&#9;}&#10;}&#10;false&quot;" within="" contains="" />
    <constraint name="Class" within="" contains="" />
    <constraint name="Parent" target="true" within="" contains="" />
</searchConfiguration>
```

## Explicit parameter type converter definition is not necessary for an enum type.

In case of Cucumber expressions there is a special, anonymous parameter (`{}`) with which, among other types, enum
parameter types can be targeted. However if someone is not familiar with this parameter, might start defining custom parameter type converters
for each enum types they want to convert.

This makes it reasonable to validate `TypeRegistryConfigurer` implementations whether
they explicitly define parameter type converters for Enum types.

The related, official documentation can be found at [Cucumber Expressions - Parameter types](https://cucumber.io/docs/cucumber/cucumber-expressions/#parameter-types).

| Compliant code | Non-compliant code |
|---|---|
| Use the `{}` parameter in step definition patterns, e.g. <pre>@When("I navigate to {} page")<br>public void i_navigate_to_X_page(PageType pageType) {<br>}</pre> | <pre>typeRegistry.defineParameterType(new ParameterType<>("type", ".*", PageType.class, (String value) -> PageType.valueOf(value)));</pre> |
|  | <pre>typeRegistry.defineParameterType(new ParameterType<>("type", ".*", PageType.class, (Transformer<PageType>) PageType::valueOf));</pre> |
The type `PageType` is just for the sake of the example, it may be any type that is enum.


**Script filter (Complete match)**

```groovy
typeRegistry.resolve().getType().getCanonicalText().matches("(io\\.cucumber\\.core\\.api|io\\.cucumber\\.stepexpression)\\.TypeRegistry")
```

**Template:**

```xml
<searchConfiguration name="Explicit parameter type converter is not necessary for an enum type. You can use empty {} placeholder instead." text="$typeRegistry$.defineParameterType(new io.cucumber.cucumberexpressions.ParameterType&lt;&gt;($keyword$, $pattern$, $Type$.class, (java.lang.String $value$) -&gt; {$Type$.valueOf($value$);}));" recursive="true" caseInsensitive="true" type="JAVA" pattern_context="default">
    <constraint name="__context__" within="" contains="" />
    <constraint name="typeRegistry" script="&quot;typeRegistry.resolve().getType().getCanonicalText().matches(&quot;(io\\.cucumber\\.core\\.api|io\\.cucumber\\.stepexpression)\\.TypeRegistry&quot;)&quot;" nameOfExprType="io\.cucumber\.stepexpression\.TypeRegistry" expressionTypes="io.cucumber.stepexpression.TypeRegistry" within="" contains="" />
    <constraint name="keyword" within="" contains="" />
    <constraint name="value" within="" contains="" />
    <constraint name="pattern" within="" contains="" />
    <constraint name="Type" within="" contains="" />
</searchConfiguration>
```

Since the above mentioned code snippet may be expressed differently, with a method reference, the following template has support to identify enum converters in that form as well:

```xml
<searchConfiguration name="Explicit parameter type converter is not necessary for an enum type. You can use empty {} placeholder instead." text="$typeRegistry$.defineParameterType(new io.cucumber.cucumberexpressions.ParameterType&lt;&gt;($keyword$, $pattern$, $Type$.class, (io.cucumber.cucumberexpressions.Transformer&lt;$Type$&gt;) $Type$::valueOf));" recursive="true" caseInsensitive="true" type="JAVA" pattern_context="default">
    <constraint name="__context__" within="" contains="" />
    <constraint name="keyword" within="" contains="" />
    <constraint name="pattern" within="" contains="" />
    <constraint name="Type" within="" contains="" />
    <constraint name="typeRegistry" script="&quot;typeRegistry.resolve().getType().getCanonicalText().matches(&quot;(io\\.cucumber\\.core\\.api|io\\.cucumber\\.stepexpression)\\.TypeRegistry&quot;)&quot;" nameOfExprType="io\.cucumber\.stepexpression\.TypeRegistry" expressionTypes="io.cucumber.stepexpression.TypeRegistry" within="" contains="" />
</searchConfiguration>
```

**Notes, caveats:**
- There might be other cases or forms of parameter type conversion that this inspection doesn't handle at the moment.
- These inspections cannot identify code snippets where the target parameter type is a parent interface type of the one that is being created,
e.g.

    ```java
    new ParameterType<>("nodetype", ".*", NodeType.class, (String value) -> ComponentType.valueOf(value)));
    ```
    where `NodeType` is an interface implemented by `ComponentType`.
- These inspections also cannot identify cases where the `TypeRegistry.defineParameterType()` method is not directly parameterized
with the actual values but it gets them for example from method parameters e.g.:

    ```java
    private <T> void addConverter(TypeRegistry typeRegistry, String keyword, Class<T> type, Transformer<T> transformer) {
        typeRegistry.defineParameterType(new ParameterType<>(keyword, ".*", type, transformer));
    }
    ```

## Step definition method is not implemented, has no body.

Step definition methods with empty body are considered unimplemented, which Cucumber also informs you about when you run a test.

| Compliant code | Non-compliant code |
|---|---|
| <pre>@When("I open the homepage")<br>public void i_open_the_homepage() {<br>    //Some test logic<br>}</pre> | <pre>@When("I open the homepage")<br>public void i_open_the_homepage() {<br>}</pre> |

**Template:**

```xml
<searchConfiguration name="Unimplemented step definition method." text="class $Class$ {&#10;  @$Step$ ()&#10;  void $Method$ ($ParameterType$ $Parameter$) {&#10;      $body$;&#10;  }&#10;}" recursive="true" caseInsensitive="true" type="JAVA" pattern_context="default">
    <constraint name="__context__" within="" contains="" />
    <constraint name="Method" withinHierarchy="true" target="true" within="" contains="" />
    <constraint name="ParameterType" within="" contains="" />
    <constraint name="Parameter" minCount="0" maxCount="2147483647" within="" contains="" />
    <constraint name="Step" regexp="(cucumber\.api\.java\|io\.cucumber\.java)\.((en\.(Given|When|Then|And|But))" within="" contains="" />
    <constraint name="body" minCount="0" maxCount="0" within="" contains="" />
    <constraint name="Class" within="" contains="" />
</searchConfiguration>
```

**Java8 step definition format:**

```xml
<searchConfiguration name="Unimplemented step definition method." text="$Java8BaseStepDefInterface$.$Step$(&quot;$stepPattern$&quot;, ($Parameters$) -&gt; {$methodBody$;});" recursive="true" caseInsensitive="true" type="JAVA" pattern_context="default">
    <constraint name="__context__" within="" contains="" />
    <constraint name="stepPattern" within="" contains="" />
    <constraint name="Step" script="&quot;Step.resolveMethod().getContainingClass().getQualifiedName().matches(&quot;(cucumber\\.api\\.java8\\.|io\\.cucumber\\.java8\\.).*&quot;)&quot;" regexp="Given|When|Then|And|But" within="" contains="" />
    <constraint name="Parameters" minCount="0" maxCount="2147483647" within="" contains="" />
    <constraint name="Java8BaseStepDefInterface" minCount="0" within="" contains="" />
    <constraint name="methodBody" minCount="0" maxCount="0" target="true" within="" contains="" />
</searchConfiguration>
```

## Cucumber step definition method is not used in any Gherkin feature file.

The **Gherkin** and **Cucumber for Java** IntelliJ plugins can link Gherkin steps and Cucumber step definition methods together,
and they also have inspection support for highlighting steps in Gherkin files that are not linked to any step definition methods.

However, the opposite inspection can be valid too, as in highlighting step definition methods that are not used in any feature files.

There might be multiple reasons why such an inspection can be useful:
- the step pattern in the step def method has been edited, but it hasn't been updated in the feature files
- the step pattern in the step def method has been edited but there is an unnoticed typo in it
- step definition methods may also be called from Java classes (regardless of whether this is a good practice or not) to avoid code duplication or create composite steps,
and it can happen that a method is no longer used in feature files but is still treated as a step definition method (e.g. it is still annotated as `@Given`, `@When`, etc.).

This inspection searches for the usages of step definition methods everywhere (for now).

Currently, this template supports the following properties (which can be altered easily):
- annotations: `@Given`, `@When`, `@Then`, `@And`, `@But`
- packages: cucumber.api.java.en, io.cucumber.java.en 

**Script filter (Complete match)**

```groovy
import com.intellij.psi.search.EverythingGlobalScope
import com.intellij.psi.search.searches.ReferencesSearch

return !isFoundGherkinStepForMethod()

def isFoundGherkinStepForMethod() {
    def methodUsageReferences = ReferencesSearch.search(Method, new EverythingGlobalScope()).findAll()
    //workaround because the Script filter cannot find CucumberStepReference when referenced as fully qualified
    return methodUsageReferences.any { it.getClass().getName().endsWith('CucumberStepReference')}
}
```

**Template:**

```xml
<searchConfiguration name="Cucumber step definition method is not used in any Gherkin feature file." text="@$Step$(&quot;$stepPattern$&quot;)&#10;public void $Method$ ($ParameterType$ $Parameter$);" recursive="true" caseInsensitive="true" type="JAVA" pattern_context="member">
    <constraint name="__context__" script="&quot;import com.intellij.psi.search.EverythingGlobalScope&#10;import com.intellij.psi.search.searches.ReferencesSearch&#10;&#10;return !isFoundGherkinStepForMethod()&#10;&#10;def isFoundGherkinStepForMethod() {&#10;&#9;def methodUsageReferences = ReferencesSearch.search(Method, new EverythingGlobalScope()).findAll()&#10;&#9;//workaround because the Script filter cannot find CucumberStepReference when referenced as fully qualified&#10;&#9;return methodUsageReferences.any { it.getClass().getName().endsWith('CucumberStepReference')}&#10;}&quot;" within="" contains="" />
    <constraint name="Method" target="true" within="" contains="" />
    <constraint name="ParameterType" within="" contains="" />
    <constraint name="Parameter" minCount="0" maxCount="2147483647" within="" contains="" />
    <constraint name="Step" regexp="(cucumber\.api\.java\|io\.cucumber\.java)\.((en\.(Given|When|Then|And|But))" within="" contains="" />
    <constraint name="stepPattern" within="" contains="" />
</searchConfiguration>
```
