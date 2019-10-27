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

## StringBuilder: chained append() calls may have better performance than multiple individual calls to append().

There is a JVM option called `OptimizeStringConcat` (see https://www.oracle.com/technetwork/articles/java/vmoptions-jsp-140102.html)
which is designed to
> Optimize String concatenation operations where possible.

That also means optimizing calls to `StringBuilder.append()` when they are chained together, and depending on how it is used
and in what circumstances, it may also provide better performance than separate individual calls to `append()`. See additional sources:
- https://stackoverflow.com/questions/44334233/why-is-the-stringbuilder-chaining-pattern-sb-appendx-appendy-faster-than-reg
- http://hg.openjdk.java.net/jdk8u/jdk8u/hotspot/file/4d9931ebf861/src/share/vm/opto/stringopts.cpp

This inspection would signal a code snippet like the following, as incorrect:

```java
StringBuilder sb = new StringBuilder();
sb.append("This");
sb.append(" is");
sb.append(" Spartaaaa!!!!");
```

which calls can be chained together like for instance:

```java
StringBuilder sb = new StringBuilder();
sb.append("This").append(" is").append(" Spartaaaa!!!!");
```

**Template:**

```xml
<searchConfiguration name="Chained append() calls may have better performance than multiple individual calls to append()." text="$stringBuilder$.append($value$);&#10;$stringBuilder$.append($otherValue$);" recursive="true" caseInsensitive="true" type="JAVA" pattern_context="default">
    <constraint name="__context__" within="" contains="" />
    <constraint name="stringBuilder" nameOfExprType="java\.lang\.StringBuilder" expressionTypes="java.lang.StringBuilder" within="" contains="" />
    <constraint name="value" within="" contains="" />
    <constraint name="otherValue" within="" contains="" />
</searchConfiguration>
```
