# Sauce Java

According to its GitHub page the Sauce Java Helper libraries
"... project contains helper libraries for consuming Sauce Labs services from Java using TestNG and/or JUnit."

You can find more information on the *[Sauce Java GitHub project](https://github.com/saucelabs/sauce-java)*.

## ConcurrentParameterized.Parameters: The parameter value is malformed. See javadoc for the list of valid values.

This is based on the javadoc written for the `@Parameters` annotation in [`com.saucelabs.junit.ConcurrentParameterized`](https://github.com/saucelabs/sauce-java/blob/master/junit/src/main/java/com/saucelabs/junit/ConcurrentParameterized.java)

This inspection would signal a code snippet like the following, as incorrect:

```java
@ConcurrentParameterized.Parameters(name = "{incorrect")
public void someMethod() {
}
```

This template can handle all the following kind of incorrect values:
- no curly braces: `"asdf"`
- no opening curly brace: `"{asdf"`
- no closing curly brace: `"asdf}"`
- incorrect value inside the curly braces: `"{asdf}"`
- negative value inside the curly braces: `"{-1}"`
- multiple digit number starting with 0: `"{012}"`

**Script filter ($Name$)**

```groovy
def placeholderPattern = /\{(.*)\}/
def matcher = Name?.value =~ placeholderPattern
def isIncorrect
if (matcher) {
    def name = matcher.group(1)
    if (!name.equals("index")) {
    	try {
    		def nameAsInt = name.toInteger()
    		if (name.length() > 1 && name.startsWith("0")) {
    			isIncorrect = true
    		} else {
    			isIncorrect = name.toInteger() < 0
    		}
    	} catch (NumberFormatException e) {
    		isIncorrect = true
    	}
    } else {
    	isIncorrect = false
    }
} else {
	isIncorrect = true
}
isIncorrect
```

**Template:**

```xml
<searchConfiguration name="The parameter value is malformed. See javadoc for the list of valid values." text="@com.saucelabs.junit.ConcurrentParameterized.Parameters(name = &quot;$Name$&quot;)&#10;$MethodType$ $Method$($ParameterType$ $Parameter$);" recursive="true" caseInsensitive="true" type="JAVA" pattern_context="member">
    <constraint name="__context__" within="" contains="" />
    <constraint name="MethodType" within="" contains="" />
    <constraint name="Method" within="" contains="" />
    <constraint name="ParameterType" within="" contains="" />
    <constraint name="Parameter" minCount="0" maxCount="2147483647" within="" contains="" />
    <constraint name="Name" script="&quot;def placeholderPattern = /\{(.*)\}/&#10;def matcher = Name?.value =~ placeholderPattern&#10;def isIncorrect&#10;if (matcher) {&#10;    def name = matcher.group(1)&#10;    if (!name.equals(&quot;index&quot;)) {&#10;    &#9;try {&#10;    &#9;&#9;def nameAsInt = name.toInteger()&#10;    &#9;&#9;if (name.length() &gt; 1 &amp;&amp; name.startsWith(&quot;0&quot;)) {&#10;    &#9;&#9;&#9;isIncorrect = true&#10;    &#9;&#9;} else {&#10;    &#9;&#9;&#9;isIncorrect = name.toInteger() &lt; 0&#10;    &#9;&#9;}&#10;    &#9;} catch (NumberFormatException e) {&#10;    &#9;&#9;isIncorrect = true&#10;    &#9;}&#10;    } else {&#10;    &#9;isIncorrect = false&#10;    }&#10;} else {&#10;&#9;isIncorrect = true&#10;}&#10;isIncorrect&quot;" target="true" within="" contains="" />
</searchConfiguration>
```

## SauceREST: getFullJobs(20) call can be replaced with getFullJobs() call. The default value is 20.

This inspection would signal a code snippet like the following, as incorrect:

```java
SauceREST rest = new SauceREST("someUserName", "somePassword");
rest.getFullJobs(20);
```

And also provides a replacement option, so that the code snippet above can be replaced with the following:

```java
rest.getFullJobs();
```

**Template:**

```xml
<replaceConfiguration name="getFullJobs(20) call can be replaced with getFullJobs() call. The default value is 20." text="$rest$.getFullJobs(20)" recursive="false" caseInsensitive="true" type="JAVA" pattern_context="default" reformatAccordingToStyle="true" shortenFQN="true" replacement="$rest$.getFullJobs()">
    <constraint name="__context__" within="" contains="" />
    <constraint name="rest" nameOfExprType="com\.saucelabs\.saucerest\.SauceREST" expressionTypes="com.saucelabs.saucerest.SauceREST" exprTypeWithinHierarchy="true" within="" contains="" />
</replaceConfiguration>
```

## SauceREST#uploadFile(): Last, overwrite parameter may be removed. The default value is true.

This inspection would signal a code snippet like the following, as incorrect:

```java
SauceREST rest = new SauceREST("someUserName", "somePassword");
File file;
rest.uploadFile(file, "aString", true);
```

And also provides a replacement option, so that the code snippet above can be replaced with the following:

```java
rest.uploadFile(file, "aString");
```

**Template:**

```xml
<replaceConfiguration name="Last, overwrite parameter may be removed. The default value is true." text="$rest$.uploadFile($file$, $string$, true);" recursive="false" caseInsensitive="true" type="JAVA" pattern_context="default" reformatAccordingToStyle="true" shortenFQN="true" replacement="$rest$.uploadFile($file$, $string$);">
    <constraint name="__context__" within="" contains="" />
    <constraint name="rest" nameOfExprType="com\.saucelabs\.saucerest\.SauceREST" expressionTypes="com.saucelabs.saucerest.SauceREST" within="" contains="" />
    <constraint name="file" nameOfExprType="java\.io\.File" expressionTypes="java.io.File" within="" contains="" />
    <constraint name="string" within="" contains="" />
</replaceConfiguration>
```
