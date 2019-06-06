# Cucumber

### When a hook declares an argument it must be of type cucumber.api.Scenario

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

### Hooks must declare 0 or 1 arguments

This is based on one of the exception handling happening in [`cucumber.runtime.java.JavaHookDefinition`](https://github.com/cucumber/cucumber-jvm/blob/352a62e01ee66088baf548864cf067bdc7c7af11/java/src/main/java/cucumber/runtime/java/JavaHookDefinition.java).

This inspection would signal a code snippet like the following, as incorrect:

```java
@BeforeStep
public void beforeStep(Scenario scenario, TestCase testCase) {
}
```

It supports tagged hooks and all of the hook annotations: `@Before`, `@BeforeStep`, `@After`, `@AfterStep`.

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
