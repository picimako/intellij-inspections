# Java

## "".equals(String) call can be replaced by String.isEmpty()

IntelliJ has an inspection to check for `someString.equals("")` code snippets but there is no inspection to check its opposite:

```java
"".equals(someString)
```

which may be replaced with an `someString.isEmpty()` call.

**Template:**

```xml
<replaceConfiguration name="&quot;&quot;.equals(String) call can be replaced by String.isEmpty()" text="&quot;&quot;.equals($STRING_ARGUMENT$)" recursive="false" caseInsensitive="true" type="JAVA" reformatAccordingToStyle="true" shortenFQN="true" useStaticImport="true" replacement="$STRING_ARGUMENT$.isEmpty()">
    <constraint name="STRING_ARGUMENT" regexp="^&quot;&quot;$" nameOfExprType="java\.lang\.String" negateName="true" within="" contains="" />
</replaceConfiguration>
```
