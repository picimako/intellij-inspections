# TestNG

- TestNG website: https://testng.org/doc/index.html
- TestNG on GitHub: https://github.com/cbeust/testng

## TestNG assertion is not used with static import.

In many cases static importing a static method makes the code less cluttered and more readable which some people might find true in case of TestNG assertions as well.

This inspection would signal code snippets like the following, as incorrect:

```java
Assert.assertEquals(someString, anotString)
```

Supported classes:
- `org.testng.Assert`
- `org.testng.AssertJUnit`
- `org.testng.FileAssert`

**Template:**

```xml
<searchConfiguration name="TestNG assertion is not used with static import." text="$Instance$.$MethodCall$($Parameter$)" recursive="false" caseInsensitive="true" type="JAVA">
    <constraint name="Instance" regexp="org.testng.Assert|org.testng.AssertJUnit|org.testng.FileAssert" within="" contains=""/>
    <constraint name="MethodCall" target="true" within="" contains=""/>
    <constraint name="Parameter" minCount="0" maxCount="2147483647" within="" contains=""/>
</searchConfiguration>
```
