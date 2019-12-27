# AssertJ

For people who are looking for a more comprehensive set of AssertJ inspections I recommend checking out the following plugin:
- [Concise AssertJ Optimizing Nitpicker ​(Cajon) - JetBrains plugin page](https://plugins.jetbrains.com/plugin/12195-concise-assertj-optimizing-nitpicker-cajon-)
- [Cajon plugin GitHub project](https://github.com/chrisly42/cajon-plugin)

AssertJ website: https://joel-costigliola.github.io/assertj/

## AssertJ assertion or assumption is not used with static import

In many cases static importing a static method makes the code less cluttered and more readable which some people might find true in case of AssertJ assertions as well.

This inspection would signal code snippets like the following, as incorrect:

```java
Assertions.assertThat(someString).isEmpty();
```

**Template:**

```xml
<searchConfiguration name="AssertJ assertion or assumption is not used with static import." text="$Instance$.$MethodCall$($Parameter$)" recursive="false"
                   caseInsensitive="true" type="JAVA">
<constraint name="Instance"
            regexp="org\.assertj\.core\.api\.(Assertions|Assumptions|AssertionsForClassTypes|AssertionsForInterfaceTypes|BDDAssertions|Java6Assertions|Java6BDDAssertions"
            within="" contains=""/>
<constraint name="MethodCall" target="true" within="" contains=""/>
<constraint name="Parameter" minCount="0" maxCount="2147483647" within="" contains=""/>
</searchConfiguration>
```

## There is no actual assertion called from AssertJ

Since syntactically calling no method after `Assertions.assertThat...()` is correct, the test where this is present will produce a false-positive result, since actually there is
no assertion that would run in this case. An example code snippet might be:

```java
Assertions.assertThat(someString);
```

It supports the following assertion methods on `org.assertj.core.api.Assertions`:
- assertThat...: ThrownBy, Code, ExceptionOfType, NullPointerException, IllegalArgumentException, IOException, IllegalStateException

**Template:**

```xml
<searchConfiguration name="There is no actual assertion called from AssertJ." text="org.assertj.core.api.Assertions.$ASSERTION_METHOD$($PARAMETER$);" recursive="true" caseInsensitive="true" type="JAVA">
<constraint name="ASSERTION_METHOD" regexp="assertThat(|ThrownBy|Code|ExceptionOfType|NullPointerException|IllegalArgumentException|IOException|IllegalStateException)" within="" contains="" />
<constraint name="PARAMETER" within="" contains="" />
<constraint name="__context__" within="" contains="" />
</searchConfiguration>
```

## There is no actual assertion called from AssertJ SoftAssertions

Since syntactically calling no method after `SoftAssertions.assertThat...()` is correct, the test where this is present will produce a false-positive result, since actually there is
no assertion that would run in this case. An example code snippet might be:

```java
SoftAssertions softly = new SoftAssertions();
softly.assertThat(someString);
```

It supports the following assertion methods on `org.assertj.core.api.SoftAssertions`: assertThat, assertThatCode, assertThatThrownBy

**Template:**

```xml
<searchConfiguration name="There is no actual assertion called from AssertJ SoftAssertions." text="$INSTANCE$.$ASSERTION_METHOD$($PARAMETER$);" recursive="false"
                   caseInsensitive="true" type="JAVA">
  <constraint name="ASSERTION_METHOD" regexp="assertThat|assertThatCode|assertThatThrownBy" within="" contains=""/>
  <constraint name="PARAMETER" within="" contains=""/>
  <constraint name="INSTANCE" nameOfExprType="org\.assertj\.core\.api\.SoftAssertions" within="" contains=""/>
</searchConfiguration>
```
