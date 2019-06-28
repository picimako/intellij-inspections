# Cucumber

## Licensing
Please let me know if using the same exact exception messages as the descriptions of the inspections is not permitted due to licensing issues.
I am happy to change them, it just made sense and I think it is clearer to use the same messages here too.

## When a hook declares an argument it must be of type cucumber.api.Scenario

This is based on one of the exception handling happening in [`cucumber.runtime.java.JavaHookDefinition`](https://github.com/cucumber/cucumber-jvm/blob/352a62e01ee66088baf548864cf067bdc7c7af11/java/src/main/java/cucumber/runtime/java/JavaHookDefinition.java).

This inspection would signal a code snippet like the following, as incorrect:

```java
@Before
public void beforeScenario(TestCase testCase) {
}
```

A correct snippet looks like the one below:

```java
@Before
public void beforeScenario(Scenario scenario) {
}
```

It supports tagged hooks and all of the hook annotations: `@Before`, `@BeforeStep`, `@After`, `@AfterStep`.

**Template:**

```xml
<searchConfiguration name="When a hook declares an argument it must be of type cucumber.api.Scenario." text="@$HookAnnotation$&#10;$ReturnType$ $hookMethod$($HookType$ $parameter$) ;" recursive="true" caseInsensitive="true" type="JAVA">
	<constraint name="__context__" within="" contains="" />
	<constraint name="HookAnnotation" regexp="cucumber\.api\.java\.(Before|BeforeStep|After|AfterStep)" maxCount="2147483647" within="" contains="" />
	<constraint name="ReturnType" within="" contains="" />
	<constraint name="hookMethod" within="" contains="" />
	<constraint name="HookType" regexp="cucumber\.api\.Scenario" target="true" negateName="true" within="" contains="" />
	<constraint name="parameter" within="" contains="" />
</searchConfiguration>
```

## Hooks must declare 0 or 1 arguments

This is based on one of the exception handling happening in [`cucumber.runtime.java.JavaHookDefinition`](https://github.com/cucumber/cucumber-jvm/blob/352a62e01ee66088baf548864cf067bdc7c7af11/java/src/main/java/cucumber/runtime/java/JavaHookDefinition.java).

This inspection would signal a code snippet like the following, as incorrect:

```java
@BeforeStep
public void beforeStep(Scenario scenario, TestCase testCase) {
}
```

It supports tagged hooks and all of the hook annotations: `@Before`, `@BeforeStep`, `@After`, `@AfterStep`.

**Template:**

```xml
<searchConfiguration name="Hooks must declare 0 or 1 arguments." text="@$HookAnnotation$&#10;$ReturnType$ $hookMethod$($HookType$ $parameter$) ;" recursive="true" caseInsensitive="true" type="JAVA">
    <constraint name="__context__" within="" contains="" />
    <constraint name="HookAnnotation" regexp="cucumber\.api\.java\.(Before|BeforeStep|After|AfterStep)" maxCount="2147483647" within="" contains="" />
    <constraint name="ReturnType" within="" contains="" />
    <constraint name="hookMethod" target="true" within="" contains="" />
    <constraint name="HookType" negateName="true" within="" contains="" />
    <constraint name="parameter" minCount="2" maxCount="2147483647" within="" contains="" />
</searchConfiguration>
```

## You must supply an output argument to html. Like so: html:output.

This is based on one of the exception handling happening in [`cucumber.runtime.formatter.PluginFactory`](https://github.com/cucumber/cucumber-jvm/blob/311c6f8e8ba21526abdbac6f27295551421a1d6f/core/src/main/java/cucumber/runtime/formatter/PluginFactory.java).

This inspection would signal a code snippet like the following, as incorrect (it is being highlighted regardless of the position of the the **plugin** attribute):

```java
@CucumberOptions(plugin = "html")
```

**Script filter ($Class$):**

```groovy
!__context__.interface && !__context__.enum
```

**Template:**

```xml
<searchConfiguration name="You must supply an output argument to html. Like so: html:output" text="@cucumber.api.CucumberOptions($plugin$ = &quot;html&quot;)&#10;class $Class$ {}" recursive="true" caseInsensitive="true" type="JAVA">
    <constraint name="__context__" within="" contains="" />
    <constraint name="Class" script="&quot;!__context__.interface &amp;&amp; !__context__.enum&quot;" within="" contains="" />
    <constraint name="plugin" regexp="plugin" target="true" within="" contains="" />
</searchConfiguration>
```

## You must supply an output argument to junit. Like so: junit:output.

This is based on one of the exception handling happening in [`cucumber.runtime.formatter.PluginFactory`](https://github.com/cucumber/cucumber-jvm/blob/311c6f8e8ba21526abdbac6f27295551421a1d6f/core/src/main/java/cucumber/runtime/formatter/PluginFactory.java).

This inspection would signal a code snippet like the following, as incorrect (it is being highlighted regardless of the position of the the **plugin** attribute):

```java
@CucumberOptions(plugin = "junit")
```

**Script filter ($Class$):**

```groovy
!__context__.interface && !__context__.enum
```

**Template:**

```xml
<searchConfiguration name="You must supply an output argument to junit. Like so: junit:output" text="@cucumber.api.CucumberOptions($plugin$ = &quot;junit&quot;)&#10;class $Class$ {}" recursive="true" caseInsensitive="true" type="JAVA">
    <constraint name="__context__" within="" contains="" />
    <constraint name="Class" script="&quot;!__context__.interface &amp;&amp; !__context__.enum&quot;" within="" contains="" />
    <constraint name="plugin" regexp="plugin" target="true" within="" contains="" />
</searchConfiguration>
```

## @CucumberOptions glue and extraGlue attributes cannot be specified at the same time

This is based on one of the exception handling happening in [`cucumber.runtime.RuntimeOptionsFactory`](https://github.com/cucumber/cucumber-jvm/blob/7a262f077a894367a4a62eb39f1d31d57351923c/core/src/main/java/cucumber/runtime/RuntimeOptionsFactory.java).

This inspection would signal a code snippet like the following, as incorrect (it is being highlighted regardless of the position of these attributes):

```java
@CucumberOptions(tags = "@tag", glue = "com.project.stepdefs", extraGlue = "com.project.otherthings")
```

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
    <constraint name="CucumberOptions" regexp="cucumber\.api\.CucumberOptions" maxCount="2147483647" target="true" within="" contains="" />
</searchConfiguration>
```

## Classes annotated with @RunWith(Cucumber.class) must not define any Step Definition or Hook methods

This is based on one of the exception handling happening in [`cucumber.runtime.junit.Assertions`](https://github.com/cucumber/cucumber-jvm/blob/e471442b0bd6a6d263bbcac03c8079107a36d84d/junit/src/main/java/cucumber/runtime/junit/Assertions.java).

This inspection would signal a code snippet like the following ones (or the combination of them), as incorrect:

```java
@RunWith(Cucumber.class)
public class JUnitTest {

    @When("the page should be loaded")
    public void the_page_should_be_loaded() {
    }
}
```

```java
@RunWith(Cucumber.class)
public class JUnitTest {

    @Before
    public void beforeScenario(Scenario scenario) {
    }
}
```

**Supported annotations:**
- **Hooks (including tagged hooks):** Before, BeforeStep, After, AfterStep
- **Steps:** `Given, When, Then, And, But

**Template:**

```xml
<searchConfiguration name="Classes annotated with @RunWith(Cucumber.class) must not define any Step Definition or Hook methods" text="@org.junit.runner.RunWith(cucumber.api.junit.Cucumber.class)&#10;public class $Class$ {&#10;&#10;    @$HookOrStepDefAnnotation$&#10;    public void $hookOrStepDefMethod$($Parameter$ $param$) {}&#10;}" recursive="true" caseInsensitive="true" type="JAVA">
    <constraint name="__context__" within="" contains="" />
    <constraint name="Class" within="" contains="" />
    <constraint name="HookOrStepDefAnnotation" regexp="cucumber\.api\.java\.((en\.(Given|When|Then|And|But))|(Before|BeforeStep|After|AfterStep))" maxCount="2147483647" target="true" within="" contains="" />
    <constraint name="hookOrStepDefMethod" maxCount="2147483647" within="" contains="" />
    <constraint name="Parameter" within="" contains="" />
    <constraint name="param" minCount="0" maxCount="2147483647" within="" contains="" />
</searchConfiguration>
```