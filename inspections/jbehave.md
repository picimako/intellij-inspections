# JBehave

- JBehave website: https://jbehave.org/
- JBehave on GitHub: https://github.com/jbehave
- JBehave Support plugin with code inspections to report unused steps definitions and undefined step usages: https://plugins.jetbrains.com/plugin/7268-jbehave-support

# At least one @Composite step definition doesn't have any of the following starting words: Given, When, Then, And, !--

When a step definition method defines a @Composite annotation with some steps, it is crucial that each step pattern includes one of the step keywords or the comment identifier at the beginning of them, otherwise JBehave throws an exception with the following message:

> No starting word found for step '<INSERT STEP TEXT HERE>' amongst '{WHEN=When, THEN=Then, GIVEN=Given, AND=And, IGNORABLE=!--}'

| Compliant code | Non-compliant code |
|---|---|
| <pre>@Given("I initiated a new empty landing page content creation with $user user")<br>@Composite(steps = {<br>        "Given I logged in with &lt;user> user",<br>        "When I navigate to the create landing content page"})<br>public void initiate_empty_landing_page_creation(User user) {<br>}</pre> | <pre>@Given("I initiated a new empty landing page content creation with $user user")<br>@Composite(steps = {<br>        "I logged in with &lt;user> user",<br>        "When I navigate to the create landing content page"})<br>public void initiate_empty_landing_page_creation(User user) {<br>}</pre> |
|  | <pre>@Given("I initiated a new empty landing page content creation with $user user")<br>@Composite(steps = {<br>        "I logged in with &lt;user> user",<br>        "I navigate to the create landing content page"})<br>public void initiate_empty_landing_page_creation(User user) {<br>}</pre> |
|  | <pre>@Given("I initiated a new empty landing page content creation with $user user")<br>@Composite(steps = {<br>        "asdasd I logged in with &lt;user> user",<br>        "When I navigate to the create landing content page"})<br>public void initiate_empty_landing_page_creation(User user) {<br>}</pre> |
|  | <pre>@Given("I initiated a new empty landing page content creation with $user user")<br>@Composite(steps = {<br>        "GivenI logged in with &lt;user> user",<br>        "When I navigate to the create landing content page"})<br>public void initiate_empty_landing_page_creation(User user) {<br>}</pre> |


Please note that the template also validates whether the starting keyword is followed by a whitespace. This is also included in the table above.


**Script filter (Complete Match):**

```groovy
import com.intellij.psi.*

if (compositeSteps instanceof PsiLiteralExpression) {
	return !hasStartingWord(compositeSteps)
} else if (compositeSteps instanceof PsiArrayInitializerMemberValue) {
	def stepsLiterals = compositeSteps?.initializers
	return stepsLiterals.any { it instanceof PsiLiteralExpression && !hasStartingWord(it) }
} else {
	return false
}

def hasStartingWord(PsiLiteralExpression literal) {    
	return literal.literalElementType == JavaTokenType.STRING_LITERAL && literal?.value?.matches('^(Given|When|Then) .*')
}
```

**Template:**


```xml
<searchConfiguration name="At least one @Composite step definition doesn't have any of the following starting words: Given, When, Then, And, !--" text="@$Step$&#10;@$Composite$(steps = $compositeSteps$)&#10;void $Method$($ParameterType$ $Parameter$);" recursive="true" caseInsensitive="true" type="JAVA" pattern_context="member">
    <constraint name="__context__" script="&quot;import com.intellij.psi.*&#10;&#10;if (compositeSteps instanceof PsiLiteralExpression) {&#10;&#9;return !hasStartingWord(compositeSteps)&#10;} else if (compositeSteps instanceof PsiArrayInitializerMemberValue) {&#10;&#9;def stepsLiterals = compositeSteps?.initializers&#10;&#9;return stepsLiterals.any { it instanceof PsiLiteralExpression &amp;&amp; !hasStartingWord(it) }&#10;} else {&#10;&#9;return false&#10;}&#10;&#10;def hasStartingWord(PsiLiteralExpression literal) {    &#10;&#9;return literal.literalElementType == JavaTokenType.STRING_LITERAL &amp;&amp; literal?.value?.matches('^(Given|When|Then) .*')&#10;}&quot;" within="" contains="" />
    <constraint name="Method" within="" contains="" />
    <constraint name="ParameterType" within="" contains="" />
    <constraint name="Parameter" minCount="0" maxCount="2147483647" within="" contains="" />
    <constraint name="Step" regexp="org\.jbehave\.core\.annotations\.(Given|When|Then)" within="" contains="" />
    <constraint name="Composite" regexp="org\.jbehave\.core\.annotations\.Composite" target="true" within="" contains="" />
    <constraint name="compositeSteps" within="" contains="" />
</searchConfiguration>
```

# @Composite annotation with one step definition can be switched to using the @Alias annotation.

Composite steps in JBehave is a way of defining step definition methods that can execute 1 or more other steps without implementing extra test logic and adding or injecting classes to do so.

JBehave has the `@Composite` annotation for this. See [official documentation](https://jbehave.org/reference/latest/composite-steps.html).

However if only one step is defined in the `@Composite` annotation it kind of defeats the purpose of this annotation, and that step acts more like an alias for the original step pattern, so it would make sense to use the `@Alias` ([documentation](https://jbehave.org/reference/latest/aliases.html)) annotation instead.

Or another possible solution is to delete the `@Composite` annotation altogether since it might have been left there accidentally.


| Compliant code | Non-compliant code |
|---|---|
| <pre>@Given("I initiated a new empty landing page content creation with $user user")<br>@Alias("GivenI logged in with &lt;user> user")<br>public void initiate_empty_landing_page_creation(User user) {<br>}</pre> | <pre>@Given("I initiated a new empty landing page content creation with $user user")<br>Composite(steps = {"GivenI logged in with &lt;user> user"})<br>public void initiate_empty_landing_page_creation(User user) {<br>}</pre> |
|  | <pre>@Given("I initiated a new empty landing page content creation with $user user")<br>@Composite(steps = "GivenI logged in with &lt;user> user")<br>public void initiate_empty_landing_page_creation(User user) {<br>}</pre> |


**Script filter (Complete Match):**

```groovy
import com.intellij.psi.*

if (compositeSteps instanceof PsiLiteralExpression) {
	return true
} else if (compositeSteps instanceof PsiArrayInitializerMemberValue) {
	return compositeSteps?.initializers?.length == 1
} else {
	return false
}
```

**Template:**

```xml
<searchConfiguration name="@Composite annotation with one step definition can be switched to using the @Alias annotation, or can be deleted entirely." text="@$Step$&#10;@$Composite$(steps = $compositeSteps$)&#10;void $Method$($ParameterType$ $Parameter$);" recursive="true" caseInsensitive="true" type="JAVA" pattern_context="member">
    <constraint name="__context__" script="&quot;import com.intellij.psi.*&#10;&#10;if (compositeSteps instanceof PsiLiteralExpression) {&#10;&#9;return true&#10;} else if (compositeSteps instanceof PsiArrayInitializerMemberValue) {&#10;    //types other then String don't have the property length&#10;&#9;return compositeSteps?.initializers?.length == 1&#10;} else {&#10;&#9;return false&#10;}&quot;" within="" contains="" />
    <constraint name="Method" within="" contains="" />
    <constraint name="ParameterType" within="" contains="" />
    <constraint name="Parameter" minCount="0" maxCount="2147483647" within="" contains="" />
    <constraint name="Step" regexp="org\.jbehave\.core\.annotations\.(Given|When|Then)" within="" contains="" />
    <constraint name="Composite" regexp="org\.jbehave\.core\.annotations\.Composite" target="true" within="" contains="" />
    <constraint name="compositeSteps" within="" contains="" />
</searchConfiguration>
```

# @Aliases annotation with one step definition can be switched to using the @Alias annotation.

Aliases in JBehave is a way of defining alternate versions of a step with which the same behaviour can be expressed in multiple ways making the story file creation more flexible and the story files themselves more readable.

JBehave has the `@Alias` and `@Aliases`annotations for this. See [official documentation](https://jbehave.org/reference/latest/aliases.html).

Ideally the `@Alias` annotation is designed to define a single alias for a step, while the `@Aliases` annotation is used to define multiple steps. However this latter one can still define a single alias, in which case it might be more readable to use the `@Alias` annotation signaling to other users that there's supposed to be only one alias for the given step.

Or as an alternative way one might want to get rid of the whole `@Aliases` annotation and use [pattern variants](https://jbehave.org/reference/latest/pattern-variants.html).

| Compliant code | Non-compliant code |
|---|---|
| Any of the following snippets: | <pre>@When("I navigate to the $urlFragment page")<br>@Aliases(values = "I refresh the $urlFragment page")<br>public void i_navigate_to_the_X_page(String urlFragment) {<br>}</pre> |
| <pre>@When("I navigate to the $urlFragment page")<br>@Alias("I refresh the $urlFragment page")<br>public void i_navigate_to_the_X_page(String urlFragment) {<br>}</pre> | <pre>@When("I navigate to the $urlFragment page")<br>@Aliases(values = {"I refresh the $urlFragment page"})<br>public void i_navigate_to_the_X_page(String urlFragment) {<br>}</pre> |
| <pre>@When("I {navigate to &#124;refresh }the $urlFragment page")<br>public void i_navigate_to_the_X_page(String urlFragment) {<br>}</pre> |  |

**Script filter (Complete Match):**

```groovy
import com.intellij.psi.*

if (aliasDefs instanceof PsiLiteralExpression) {
	return true
} else if (aliasDefs instanceof PsiArrayInitializerMemberValue) {
	return aliasDefs?.initializers?.length == 1
} else {
	return false
}
```

**Template:**

```xml
<searchConfiguration name="@Aliases annotation with one step definition can be switched to using the @Alias annotation." text="@$Step$&#10;@$Aliases$(values = $aliasDefs$)&#10;void $Method$($ParameterType$ $Parameter$);" recursive="true" caseInsensitive="true" type="JAVA" pattern_context="member">
    <constraint name="__context__" script="&quot;import com.intellij.psi.*&#10;&#10;if (aliasDefs instanceof PsiLiteralExpression) {&#10;&#9;return true&#10;} else if (aliasDefs instanceof PsiArrayInitializerMemberValue) {&#10;&#9;return aliasDefs?.initializers?.length == 1&#10;} else {&#10;&#9;return false&#10;}&quot;" within="" contains="" />
    <constraint name="Method" within="" contains="" />
    <constraint name="ParameterType" within="" contains="" />
    <constraint name="Parameter" minCount="0" maxCount="2147483647" within="" contains="" />
    <constraint name="Step" regexp="org\.jbehave\.core\.annotations\.(Given|When|Then)" within="" contains="" />
    <constraint name="Aliases" regexp="org\.jbehave\.core\.annotations\.Aliases" target="true" within="" contains="" />
    <constraint name="aliasDefs" within="" contains="" />
</searchConfiguration>
```

# @UsingEmbedder's thread count must be greater than or equal to 1.

JBehave has the `@UsingEmbedder` annotation with which (besides using other annotations as well) one can configure the Embedder and the test execution properties via annotation based configuration. See [official documentation](https://jbehave.org/reference/latest/configuration.html).

The default value for the `threads` attribute of this annotation is 1, but if one defines a value less than 1:
- it doesn't really make sense, and
- JBehave won't be able to start the text execution.

In that you must either define a proper value or remove the `threads` attribute so that it defaults to 1 thread.

| Compliant code | Non-compliant code |
|---|---|
| Any of the following snippets: | <pre>@UsingEmbedder(threads = 0)<br>public class AllEmbedder extends InjectableEmbedder {<br>}</pre> |
| <pre>@UsingEmbedder(threads = 5)<br>public class AllEmbedder extends InjectableEmbedder {<br>}</pre> | <pre>@UsingEmbedder(threads = -1)<br>public class AllEmbedder extends InjectableEmbedder {<br>}</pre> |
| <pre>@UsingEmbedder()<br>public class AllEmbedder extends InjectableEmbedder {<br>}</pre> |  |

**Script filter (Complete Match):**

```groovy
try {
    threadCount?.text?.toInteger() < 1
} catch (NumberFormatException e) {
    false
}
```

**Template:**

```xml
<searchConfiguration name="@UsingEmbedder's thread count must be greater than or equal to 1." text="@$UsingEmbedder$($threads$ = $threadCount$)&#10;class $Class$ {}" recursive="true" caseInsensitive="true" type="JAVA" pattern_context="default">
    <constraint name="__context__" script="&quot;try {&#10;    threadCount?.text?.toInteger() &lt; 1&#10;} catch (NumberFormatException e) {&#10;    false&#10;}&quot;" within="" contains="" />
    <constraint name="Class" within="" contains="" />
    <constraint name="UsingEmbedder" regexp="org\.jbehave\.core\.annotations\.UsingEmbedder" within="" contains="" />
    <constraint name="threads" regexp="threads" target="true" within="" contains="" />
    <constraint name="threadCount" within="" contains="" />
</searchConfiguration>
```

# One or more aliases start with one of the following keywords: Given, When, Then, And

When defining aliases for a step, the mapping of steps works as follows (for a step originally defined as WHEN):

| Story step | Alias definition |
|---|---|
| `When I navigate back to the LANDING page` | `@Alias("I navigate back to the $page page")` |
| `When When I navigate back to the LANDING page` | `@Alias("When I navigate back to the $page page")` |

Although technically it is not incorrect to use the Given, When, Then, And keywords in the alias values, and there might be cases when the step text starts with a step keyword, probably the first one in the table above is used in most cases.

| Compliant code | Non-compliant code |
|---|---|
| <pre>@When("I navigate to the $urlFragment page")<br>@Aliases(values = {<br>        "I navigate back to the $urlFragment page",<br>        "I refresh the $urlFragment page"<br>})<br>public void i_navigate_to_the_X_page(String urlFragment) {<br>}</pre> | <pre>@When("I navigate to the $urlFragment page")<br>@Aliases(values = {<br>        "When I navigate back to the $urlFragment page",<br>        "I refresh the $urlFragment page"<br>})<br>public void i_navigate_to_the_X_page(String urlFragment) {<br>}</pre> |
|  | <pre>@When("I navigate to the $urlFragment page")<br>@Aliases(values = {<br>        "When I navigate back to the $urlFragment page",<br>        "When I refresh the $urlFragment page"<br>})<br>public void i_navigate_to_the_X_page(String urlFragment) {<br>}</pre> |
| <pre>@When("I navigate to the $urlFragment page")<br>@Alias("I navigate back to the $urlFragment page")<br>public void i_navigate_to_the_X_page(String urlFragment) {<br>}</pre> | <pre>@When("I navigate to the $urlFragment page")<br>@Alias("When I navigate back to the $urlFragment page")<br>public void i_navigate_to_the_X_page(String urlFragment) {<br>}</pre> |

**Script filter - for @Aliases (Complete Match):**

```groovy
import com.intellij.psi.*

if (aliasDefs instanceof PsiLiteralExpression) {
	return hasStartingWord(aliasDefs)
} else if (aliasDefs instanceof PsiArrayInitializerMemberValue) {
	def aliasValues = aliasDefs?.initializers
	return aliasValues.any { it instanceof PsiLiteralExpression && hasStartingWord(it) }
} else {
	return false
}

def hasStartingWord(PsiLiteralExpression literal) {    
	return literal.literalElementType == JavaTokenType.STRING_LITERAL && literal?.value?.matches('^(Given|When|Then) .*')
}
```

**Template (for @Aliases):**

```xml
<searchConfiguration name="One or more aliases start with one of the following keywords: Given, When, Then, And" text="@$Step$&#10;@$Aliases$(values = $aliasDefs$)&#10;void $Method$($ParameterType$ $Parameter$);" recursive="true" caseInsensitive="true" type="JAVA" pattern_context="member">
    <constraint name="__context__" script="&quot;import com.intellij.psi.*&#10;&#10;if (aliasDefs instanceof PsiLiteralExpression) {&#10;&#9;return hasStartingWord(aliasDefs)&#10;} else if (aliasDefs instanceof PsiArrayInitializerMemberValue) {&#10;&#9;def aliasValues = aliasDefs?.initializers&#10;&#9;return aliasValues.any { it instanceof PsiLiteralExpression &amp;&amp; hasStartingWord(it) }&#10;} else {&#10;&#9;return false&#10;}&#10;&#10;def hasStartingWord(PsiLiteralExpression literal) {    &#10;&#9;return literal.literalElementType == JavaTokenType.STRING_LITERAL &amp;&amp; literal?.value?.matches('^(Given|When|Then) .*')&#10;}&quot;" within="" contains="" />
    <constraint name="Method" within="" contains="" />
    <constraint name="ParameterType" within="" contains="" />
    <constraint name="Parameter" minCount="0" maxCount="2147483647" within="" contains="" />
    <constraint name="Step" regexp="org\.jbehave\.core\.annotations\.(Given|When|Then)" within="" contains="" />
    <constraint name="Aliases" regexp="org\.jbehave\.core\.annotations\.Aliases" target="true" within="" contains="" />
    <constraint name="aliasDefs" within="" contains="" />
</searchConfiguration>
```

**Script filter - for @Alias (Complete Match):**

```groovy
import com.intellij.psi.*

aliasDef instanceof PsiLiteralExpression ? hasStartingWord(aliasDef) : false

def hasStartingWord(PsiLiteralExpression literal) {    
	return literal.literalElementType == JavaTokenType.STRING_LITERAL && literal?.value?.matches('^(Given|When|Then) .*')
}
```

**Template (for @Alias):**

```xml
<searchConfiguration name="The alias starts with one of the following keywords: Given, When, Then, And" text="@$Step$&#10;@$Alias$(value = $aliasDef$)&#10;void $Method$($ParameterType$ $Parameter$);" recursive="true" caseInsensitive="true" type="JAVA" pattern_context="member">
    <constraint name="__context__" script="&quot;import com.intellij.psi.*&#10;&#10;aliasDef instanceof PsiLiteralExpression ? hasStartingWord(aliasDef) : false&#10;&#10;def hasStartingWord(PsiLiteralExpression literal) {    &#10;&#9;return literal.literalElementType == JavaTokenType.STRING_LITERAL &amp;&amp; literal?.value?.matches('^(Given|When|Then) .*')&#10;}&quot;" within="" contains="" />
    <constraint name="Method" within="" contains="" />
    <constraint name="ParameterType" within="" contains="" />
    <constraint name="Parameter" minCount="0" maxCount="2147483647" within="" contains="" />
    <constraint name="Step" regexp="org\.jbehave\.core\.annotations\.(Given|When|Then)" within="" contains="" />
    <constraint name="Alias" regexp="org\.jbehave\.core\.annotations\.Alias" target="true" within="" contains="" />
    <constraint name="aliasDef" within="" contains="" />
</searchConfiguration>
```

# @UsingEmbedder's storyTimeout format is incorrect or misleading.

The `@UsingEmbedder` annotation has a `storyTimeouts` attribute in which one can define timeout for the stories run with the current `Embedder`.

There are two built-in timeout parsers for this with two different timeout formats:
- `org.jbehave.core.embedder.StoryTimeouts.SimpleTimeoutParser`: e.g. `1d 2h 3m 4s`
- `org.jbehave.core.embedder.StoryTimeouts.DigitTimeoutParser`: e.g. `10`

The following table contains examples for (most of) the valid timout values:

| Timeout with whitespaces | Timeout without whitespaces |
|---|---|
| 1d          | 1d       |
| 1d 2h       | 1d2h     |
| 1d 2h 3m    | 1d2h3m   |
| 1d 2h 3m 4s | 1d2h3m4s |
| 1d 3m       | 1d3m     |
| 1d 3m 4s    | 1d3m4s   |
| 1d 4s       | 1d4s     |
| 2h          | 2h       |
| 2h 3m       | 2h3m     |
| 2h 3m 4s    | 2h3m4s   |
| 2h 4s       | 2h4s     |
| 3m          | 3m       |
| 3m 4s       | 3m4s     |
| 4s          | 4s       |
| 30          | 30       |

Altough it is also a valid timeout when the last unit character is left out e.g. `1d2h3m4` it is misleading because the 4 at the end is ignored by the parser and not counted into the accumulated timeout value. Thus this inspection also highlight these kind of values too.

Please note that custom units can be defined for the `SimpleTimeoutParser` so adjustment of this inspection might be needed according to that.

| Compliant code | Non-compliant code |
|---|---|
| Only positive values: `@UsingEmbedder(storyTimeouts = "10")` | `@UsingEmbedder(storyTimeouts = "-10")` |
| By default only d, h, s, m are supported units: `@UsingEmbedder(storyTimeouts = "1d")` | `@UsingEmbedder(storyTimeouts = "1dd")` |
| String values are incorrect: `@UsingEmbedder(storyTimeouts = "10s")` | `@UsingEmbedder(storyTimeouts = "asd")` |
| Last value without unit is ignored: `@UsingEmbedder(storyTimeouts = "1d 2h 3m 4s")` | `@UsingEmbedder(storyTimeouts = "1d 2h 3m 4")` |
| Last value without unit is ignored: `@UsingEmbedder(storyTimeouts = "1d2h3m4s")` | `@UsingEmbedder(storyTimeouts = "1d2h3m4")` |

**Script filter (Complete Match):**

```groovy
import com.intellij.psi.*

def timeoutPattern = /((\d+d\s?)?(\d+h\s?)?(\d+m\s?)?(\d+s\s?)?)|(\d+)/
timeout instanceof PsiLiteralExpression && timeout.literalElementType == JavaTokenType.STRING_LITERAL && !timeout?.value?.matches(timeoutPattern)
```

**Template:**

```xml
<searchConfiguration name="@UsingEmbedder's storyTimeout format is incorrect or misleading." text="@$UsingEmbedder$($storyTimeout$ = $timeout$)&#10;class $Class$ {}" recursive="true" caseInsensitive="true" type="JAVA" pattern_context="default">
    <constraint name="__context__" script="&quot;import com.intellij.psi.*&#10;&#10;def timeoutPattern = /((\d+d\s?)?(\d+h\s?)?(\d+m\s?)?(\d+s\s?)?)|(\d+)/&#10;timeout instanceof PsiLiteralExpression &amp;&amp; timeout.literalElementType == JavaTokenType.STRING_LITERAL &amp;&amp; !timeout?.value?.matches(timeoutPattern)&quot;" within="" contains="" />
    <constraint name="Class" within="" contains="" />
    <constraint name="UsingEmbedder" regexp="org\.jbehave\.core\.annotations\.UsingEmbedder" within="" contains="" />
    <constraint name="storyTimeout" regexp="storyTimeouts" target="true" within="" contains="" />
    <constraint name="timeout" within="" contains="" />
</searchConfiguration>
```

# Unnecessary list item separator (comma) at the end of @UsingEmbedder's systemProperties attribute.

The `@UsingEmbedder` annotation has a `systemProperties` attribute in which one can define Java system property names and values to pass into the executed tests.

It can be defined in the following formats:

| systemProperties attribute | Created system properties |
|---|---|
| `prop` | `prop` with empty String value  |
| `prop:value` | `prop` with value `value` |
| `prop1:value1,prop2` | `prop1` with value `value1` and `prop2` with empty String value |
| `prop1:value1,prop2:value2` | `prop1` with value `value1` and `prop2` with value `value2` |

Leaving a list item separator comma at the end of the attribute value is not invalid, the system properties values will still be parsed, but it might be misleading why it is left there.

| Compliant code | Non-compliant code |
|---|---|
| `@UsingEmbedder(systemProperties = "prop")` | `@UsingEmbedder(systemProperties = "prop,")` |
| `@UsingEmbedder(systemProperties = "prop:value")` | `@UsingEmbedder(systemProperties = "prop:value,")` |
| `@UsingEmbedder(systemProperties = "prop1:value1,prop2")` | `@UsingEmbedder(systemProperties = "prop1:value1,prop2,")` |
| `@UsingEmbedder(systemProperties = "prop1:value1,prop2:value2")` | `@UsingEmbedder(systemProperties = "prop1:value1,prop2:value2,")` |


**Script filter (Complete Match):**

```groovy
import com.intellij.psi.*

properties instanceof PsiLiteralExpression && properties.literalElementType == JavaTokenType.STRING_LITERAL && properties?.value?.endsWith(',')
```

**Template:**

```xml
<searchConfiguration name="Unnecessary list item separator (comma) at the end of @UsingEmbedder's systemProperties attribute." text="@$UsingEmbedder$($systemProperties$ = $properties$)&#10;class $Class$ {}" recursive="true" caseInsensitive="true" type="JAVA" pattern_context="default">
    <constraint name="__context__" script="&quot;import com.intellij.psi.*&#10;&#10;properties instanceof PsiLiteralExpression &amp;&amp; properties.literalElementType == JavaTokenType.STRING_LITERAL &amp;&amp; properties?.value?.endsWith(',')&quot;" within="" contains="" />
    <constraint name="Class" within="" contains="" />
    <constraint name="UsingEmbedder" regexp="org\.jbehave\.core\.annotations\.UsingEmbedder" within="" contains="" />
    <constraint name="systemProperties" regexp="systemProperties" target="true" within="" contains="" />
    <constraint name="properties" within="" contains="" />
</searchConfiguration>
```

# One or more meta filters' format is incorrect.

JBehave provides the option to filter executed stories based on meta filters, in multiple ways. One way is to define it via the `@UsingEmbedder` annotation's `metaFilters` attribute. For the supported formats please head over to the official [Meta filtering documentation](https://jbehave.org/reference/latest/meta-filtering.html).

This inspection aims to highlight format problems when the Default meta matcher is used. This also means that if this inspection is added but you use a different meta matcher, like the Groovy one, it will flag all meta matcher values as incorrect.

| Compliant code | Non-compliant code |
|---|---|
| `@UsingEmbedder(metaFilters = "+smoke -skip")` | `@UsingEmbedder(metaFilters = "+smoke --skip")` |
| `@UsingEmbedder(metaFilters = "+smoke *")` | `@UsingEmbedder(metaFilters = "smoke *")` |
| `@UsingEmbedder(metaFilters = {"+smoke", "-skip"})` | `@UsingEmbedder(metaFilters = {"+ smoke", "-skip"})` |
| `@UsingEmbedder(metaFilters = {"+smoke this that", "-skip"})` | `@UsingEmbedder(metaFilters = {"+smoke this $that", "-skip"})` |

**Script filter (Complete Match):**

```groovy
import com.intellij.psi.*

if (filters instanceof PsiLiteralExpression) {
	return !hasCorrectFormat(filters)
} else if (filters instanceof PsiArrayInitializerMemberValue) {
	def aliasValues = filters?.initializers
	return aliasValues.any { it instanceof PsiLiteralExpression && !hasCorrectFormat(it) }
} else {
	return false
}

def hasCorrectFormat(PsiLiteralExpression literal) {    
	return literal.literalElementType == JavaTokenType.STRING_LITERAL && literal?.value?.matches('([+-]([\\w\\*]+ ?)+)+')
}
```

**Template:**

```xml
<searchConfiguration name="One or more meta filters' format is incorrect." text="@$UsingEmbedder$($metaFilters$ = $filters$)&#10;class $Class$ {}" recursive="true" caseInsensitive="true" type="JAVA" pattern_context="default">
    <constraint name="__context__" script="&quot;import com.intellij.psi.*&#10;&#10;if (filters instanceof PsiLiteralExpression) {&#10;&#9;return !hasCorrectFormat(filters)&#10;} else if (filters instanceof PsiArrayInitializerMemberValue) {&#10;&#9;def aliasValues = filters?.initializers&#10;&#9;return aliasValues.any { it instanceof PsiLiteralExpression &amp;&amp; !hasCorrectFormat(it) }&#10;} else {&#10;&#9;return false&#10;}&#10;&#10;def hasCorrectFormat(PsiLiteralExpression literal) {    &#10;&#9;return literal.literalElementType == JavaTokenType.STRING_LITERAL &amp;&amp; literal?.value?.matches('([+-]([\\w\\*]+ ?)+)+')&#10;}&quot;" within="" contains="" />
    <constraint name="Class" within="" contains="" />
    <constraint name="UsingEmbedder" regexp="org\.jbehave\.core\.annotations\.UsingEmbedder" within="" contains="" />
    <constraint name="metaFilters" regexp="metaFilters" target="true" within="" contains="" />
    <constraint name="filters" within="" contains="" />
</searchConfiguration>
```
