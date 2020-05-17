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
