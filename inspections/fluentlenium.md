# FluentLenium

FluentLenium inspections are now also available in [FluentLenium's repository](https://github.com/FluentLenium/FluentLenium/tree/develop/fluentlenium-ide-support/intellij/inspections)
which were added in [FluentLenium/711](https://github.com/FluentLenium/FluentLenium/issues/711).

- FluentLenium website: https://fluentlenium.com
- FluentLenium on GitHub: https://github.com/FluentLenium/FluentLenium/

## @FluentConfiguration has remoteUrl but webDriver is not defined as "remote"

| Compliant code | Non-compliant code |
|---|---|
| <pre>@FluentConfiguration(remoteUrl = "https://some.domain:4444/wd/hub", webDriver = "remote")<br>public class TestClass {<br>}</pre> | <pre>@FluentConfiguration(remoteUrl = "https://some.domain:4444/wd/hub")<br>public class TestClass {<br>}</pre> |

This inspection is applicable only when both `remoteUrl` and `webDriver` is meant to be configured via the `@FluentConfiguration` annotation an not separately,
e.g via config properties or via command line properties.

The class in FluentLenium that creates the remote webdriver is `org.fluentlenium.configuration.DefaultWebDriverFactories.RemoteWebDriverFactory`,
which might be a useful reference.

**Template:**

```xml
<searchConfiguration name="FluentConfiguration has remoteUrl but webDriver is not defined as &quot;remote&quot;" text="@org.fluentlenium.configuration.FluentConfiguration(webDriver = &quot;$NOT_REMOTE$&quot;, remoteUrl = $REMOTE_URL$)" recursive="true" caseInsensitive="true" type="JAVA">
    <constraint name="__context__" within="" contains="" />
    <constraint name="NOT_REMOTE" regexp="remote" target="true" negateName="true" within="" contains="" />
    <constraint name="REMOTE_URL" within="" contains="" />
</searchConfiguration>
```

## @FluentConfiguration has webDriver defined as "remote" but there is no remoteUrl

| Compliant code | Non-compliant code |
|---|---|
| <pre>@FluentConfiguration(remoteUrl = "https://some.domain:4444/wd/hub", webDriver = "remote")<br>public class TestClass {<br>}</pre> | <pre>@FluentConfiguration(webDriver = "remote")<br>public class TestClass {<br>}</pre> |

This inspection is applicable only when both remoteUrl and webDriver is meant to be configured via the `@FluentConfiguration` annotation an not separately,
e.g via config properties or via command line properties.

The class in FluentLenium that creates the remote webdriver is `org.fluentlenium.configuration.DefaultWebDriverFactories.RemoteWebDriverFactory`,
which might be a useful reference.

**Template:**

```xml
<searchConfiguration name="FluentConfiguration has webDriver defined as &quot;remote&quot; but there is no remoteUrl." text="@org.fluentlenium.configuration.FluentConfiguration(webDriver = &quot;$REMOTE$&quot;, $REMOTE_URL_ATTR$ = $REMOTE_URL$)" recursive="true" caseInsensitive="true" type="JAVA">
    <constraint name="__context__" within="" contains="" />
    <constraint name="REMOTE_URL" minCount="0" maxCount="0" within="" contains="" />
    <constraint name="REMOTE" regexp="remote" target="true" within="" contains="" />
    <constraint name="REMOTE_URL_ATTR" regexp="remoteUrl" minCount="0" maxCount="0" within="" contains="" />
</searchConfiguration>
```

## CapabilitiesFactory and WebDriverFactory implementations must be annotated as @FactoryName to provide a reference to them

Implementations of the `CapabilitiesFactory` and the `WebDriverFactory` interfaces should be annotated as `@FactoryName` because the value given in that annotation
is the name that these particular capabilities and webdriver types may be referenced configured for FluentLenium.

| Compliant code | Non-compliant code |
|---|---|
| <pre>@FactoryName("saucelabs")<br>public class SauceLabsDriverFactory implements WebDriverFactory {<br>}</pre> | <pre>public class SauceLabsDriverFactory implements WebDriverFactory {<br>}</pre> |
| <pre>@FactoryName("browserstack")<br>public class BrowserStackCapabilitiesFactory implements CapabilitiesFactory {<br>}</pre> | <pre>public class BrowserStackCapabilitiesFactory implements CapabilitiesFactory {<br>}</pre> |

**Template:**

```xml
<searchConfiguration name="CapabilitiesFactory and WebDriverFactory implementations must be annotated as @FactoryName to provide a reference to them." text="@$FACTORY_NAME$&#10;public class $FACTORY_IMPLEMENTATION$ implements $FACTORY_INTERFACE$ {}" recursive="true" caseInsensitive="true" type="JAVA">
    <constraint name="__context__" within="" contains="" />
    <constraint name="FACTORY_INTERFACE" regexp="org\.fluentlenium\.configuration\.CapabilitiesFactory|org\.fluentlenium\.configuration\.WebDriverFactory" within="" contains="" />
    <constraint name="FACTORY_NAME" regexp="org\.fluentlenium\.configuration\.FactoryName" minCount="0" maxCount="0" within="" contains="" />
    <constraint name="FACTORY_IMPLEMENTATION" target="true" within="" contains="" />
</searchConfiguration>
```

## @PageUrl annotated class should extend FluentPage

The purpose of the `@PageUrl` annotation is to be able define the URL/path that particular page is available at (be it a remote URL or a local path on a computer).
However when a page object class is annotated as `@PageUrl` it must also extend `FluentPage` because the annotation is processed by `FluentPage` during navigation related actions.

| Compliant code | Non-compliant code |
|---|---|
| <pre>@PageUrl("/login")<br>public class LogInPage extends FluentPage {<br>}</pre> | <pre>@PageUrl("/login")<br>public class LogInPage {<br>}</pre> | 

**Template:**

```xml
<searchConfiguration name="PageUrl annotated class should extend FluentPage." text="@$PAGE_URL_ANNOTATION$( )&#10;public class $PAGE_OBJECT$ extends $FLUENT_PAGE$ { }" recursive="true" caseInsensitive="true" type="JAVA">
    <constraint name="__context__" within="" contains="" />
    <constraint name="PAGE_OBJECT" target="true" within="" contains="" />
    <constraint name="PAGE_URL_ANNOTATION" regexp="org\.fluentlenium\.core\.annotation\.PageUrl" within="" contains="" />
    <constraint name="FLUENT_PAGE" regexp="org\.fluentlenium\.core\.FluentPage" minCount="0" maxCount="0" within="" contains="" />
</searchConfiguration>
```

## The value of the @PageUrl annotation must start with /, http://, https://

The underlying URL building logic is put together in a way that the created URL will only be valid if the value of the `@PageUrl` starts with a / in case of relative URLs
and with http:// or https:// in case of absolute URLs.

| Compliant code | Non-compliant code |
|---|---|
| <pre>@PageUrl("/login")<br>public class LogInPage extends FluentPage {<br>}</pre> | <pre>@PageUrl("login")<br>public class LogInPage extends FluentPage {<br>}</pre> |

**Template:**

```xml
<searchConfiguration name="The value of the @PageUrl annotation must start with /, http://, https://." text="@$PageUrl$ ($Value$)&#10;class $Class$ { }" recursive="true" caseInsensitive="true" type="JAVA">
    <constraint name="__context__" within="" contains="" />
    <constraint name="PageUrl" regexp="org\.fluentlenium\.core\.annotation\.PageUrl" target="true" within="" contains="" />
    <constraint name="Value" regexp="(^(&quot;\/|&quot;https?:\/\/).*&quot;$)|(value = (&quot;\/|&quot;https?:\/\/).*&quot;)" negateName="true" within="" contains="" />
    <constraint name="Class" within="" contains="" />
</searchConfiguration>
```

## Certain FluentWebElement calls can be simplified

There are a couple of convenience methods for certain calls from `FluentWebElement` objects that might be shorter, more concise.

| Compliant code | Non-compliant code |
|---|---|
| <pre>element.id()</pre> | <pre>element.attribute("id")</pre> |
| <pre>element.textContent()</pre> | <pre>element.attribute("textContent")</pre> |
| <pre>element.value()</pre> | <pre>element.attribute("value")</pre> |
| <pre>element.name()</pre> | <pre>element.attribute("name")</pre> |
| <pre>element.first()</pre> | <pre>element.get(0)</pre> |

**Template (id()):**

```xml
<replaceConfiguration name="You can call id() on a FluentWebElement instead of attribute(&quot;id&quot;)." text="$FluentWebElement$.attribute(&quot;id&quot;)" recursive="false" caseInsensitive="true" type="JAVA" reformatAccordingToStyle="false" shortenFQN="false" replacement="$FluentWebElement$.id()">
    <constraint name="__context__" within="" contains="" />
    <constraint name="FluentWebElement" nameOfExprType="org\.fluentlenium\.core\.domain\.FluentWebElement" expressionTypes="org.fluentlenium.core.domain.FluentWebElement" within="" contains="" />
</replaceConfiguration>
```

**Template (textContent()):**

```xml
<replaceConfiguration name="You can call textContent() on a FluentWebElement instead of attribute(&quot;textContent&quot;)." text="$FluentWebElement$.attribute(&quot;textContent&quot;)" recursive="false" caseInsensitive="true" type="JAVA" reformatAccordingToStyle="false" shortenFQN="false" replacement="$FluentWebElement$.textContent()">
    <constraint name="__context__" within="" contains="" />
    <constraint name="FluentWebElement" nameOfExprType="org\.fluentlenium\.core\.domain\.FluentWebElement" expressionTypes="org.fluentlenium.core.domain.FluentWebElement" within="" contains="" />
</replaceConfiguration>
```

**Template (value()):**
 
```xml
<replaceConfiguration name="You can call value() on a FluentWebElement instead of attribute(&quot;value&quot;)." text="$FluentWebElement$.attribute(&quot;value&quot;)" recursive="false" caseInsensitive="true" type="JAVA" reformatAccordingToStyle="false" shortenFQN="false" replacement="$FluentWebElement$.value()">
    <constraint name="__context__" within="" contains="" />
    <constraint name="FluentWebElement" nameOfExprType="org\.fluentlenium\.core\.domain\.FluentWebElement" expressionTypes="org.fluentlenium.core.domain.FluentWebElement" within="" contains="" />
</replaceConfiguration>
```

**Template (name()):**

```xml
<replaceConfiguration name="You can call name() on a FluentWebElement instead of attribute(&quot;name&quot;)." text="$FluentWebElement$.attribute(&quot;name&quot;)" recursive="false" caseInsensitive="true" type="JAVA" reformatAccordingToStyle="false" shortenFQN="false" replacement="$FluentWebElement$.name()">
    <constraint name="__context__" within="" contains="" />
    <constraint name="FluentWebElement" nameOfExprType="org\.fluentlenium\.core\.domain\.FluentWebElement" expressionTypes="org.fluentlenium.core.domain.FluentWebElement" within="" contains="" />
</replaceConfiguration>
```

**Template (first()):**

```xml
<replaceConfiguration name="You can call first() on a FluentList instead of get(0)." text="$FluentList$.get(0)" recursive="false" caseInsensitive="true" type="JAVA" reformatAccordingToStyle="false" shortenFQN="false" replacement="$FluentList$.first()">
    <constraint name="__context__" within="" contains="" />
    <constraint name="FluentList" nameOfExprType="org\.fluentlenium\.core\.domain\.FluentList" expressionTypes="org.fluentlenium.core.domain.FluentList" within="" contains="" />
</replaceConfiguration>
```

## There is no actual assertion called from FluentLeniumAssertions

Just like in case of AssertJ assertions, its FluentLenium specific implementation, `FluentLeniumAssertions` may be used in a way that there is no actual
assertion is called on the object.

| Compliant code | Non-compliant code |
|---|---|
| <pre>assertThat(loginPage.form).isPresent();</pre> | <pre>assertThat(loginPage.form);</pre> |

**Template:**

```xml
<searchConfiguration name="There is no actual assertion called from FluentLeniumAssertions." text="org.fluentlenium.assertj.FluentLeniumAssertions.$ASSERTION_METHOD$($PARAMETER$);" recursive="true" caseInsensitive="true" type="JAVA">
    <constraint name="ASSERTION_METHOD" regexp="assertThat" within="" contains="" />
    <constraint name="PARAMETER" within="" contains="" />
    <constraint name="__context__" within="" contains="" />
</searchConfiguration>
```

## @Page annotated field is instantiated explicitly. Instantiation can be removed as the field is injected by FluentLenium

The `@Page` annotation is meant to be used on fields that one wants to inject into the class thus they are meant to be initialized explicitly.

See [official documentation](https://fluentlenium.com/docs/key_features/#page-injection).

| Compliant code | Non-compliant code |
|---|---|
| <pre>public class Homepage {<br>@Page<br>private HeroImage hero;<br>}</pre> | <pre>public class Homepage {<br>@Page<br>private HeroImage hero = new HeroImage();<br>}</pre> |

**Template:**

```xml
<searchConfiguration name="@Page annotated field is instantiated explicitly. Instantiaton can be removed as the field is injected by FluentLenium." text="class $Class$ { &#10;    @$PageAnnotation$ ( ) &#10;    @Modifier(&quot;Instance&quot;) $FieldType$ $Field$ = $Init$;&#10;}" recursive="true" caseInsensitive="true" type="JAVA">
    <constraint name="__context__" within="" contains="" />
    <constraint name="Class" within="" contains="" />
    <constraint name="FieldType" within="" contains="" />
    <constraint name="Field" maxCount="2147483647" target="true" within="" contains="" />
    <constraint name="Init" within="" contains="" />
    <constraint name="PageAnnotation" regexp="org\.fluentlenium\.core\.annotation\.Page" within="" contains="" />
</searchConfiguration>
```

## @Page annotated field is static. It should be used as an instance field

`@Page` annotated fields are initialized and injected during construction of an instance of the containing class, so they are not meant to be static fields.

| Compliant code | Non-compliant code |
|---|---|
| <pre>public class Homepage {<br>@Page<br>private HeroImage hero;<br>}</pre> | <pre>public class Homepage {<br>@Page<br>private static HeroImage hero;<br>}</pre> |

**Template:**

```xml
<searchConfiguration name="@Page annotated field is static. It should be used as an instance field." text="class $Class$ {&#10;    @$PageAnnotation$ ( )&#10;    static $FieldType$ $Field$ = $Init$;&#10;}" recursive="true" caseInsensitive="true" type="JAVA">
    <constraint name="__context__" within="" contains="" />
    <constraint name="Class" within="" contains="" />
    <constraint name="PageAnnotation" regexp="org\.fluentlenium\.core\.annotation\.Page" within="" contains="" />
    <constraint name="FieldType" within="" contains="" />
    <constraint name="Field" target="true" within="" contains="" />
    <constraint name="Init" minCount="0" within="" contains="" />
</searchConfiguration>
```

## The class is annotated as @FactoryName but doesn't implement WebDriverFactory or CapabilitiesFactory

The `@FactoryName` annotation is designed to be used on classes implementing either the `CapabilitiesFactory` or the `WebDriverFactory` interface,
the value given in that annotation is the name that these particular capabilities and webdriver types may be referenced configured for FluentLenium. 

| Compliant code | Non-compliant code |
|---|---|
| <pre>@FactoryName("saucelabs")<br>public class SauceLabsDriverFactory implements WebDriverFactory {<br>}</pre> | <pre>@FactoryName("saucelabs")<br>public class SauceLabsDriverFactory {<br>}</pre> |
| <pre>@FactoryName("browserstack")<br>public class BrowserStackCapabilitiesFactory implements CapabilitiesFactory {<br>}</pre> | <pre>@FactoryName("browserstack")<br>public class BrowserStackCapabilitiesFactory{<br>}</pre> |

**Template:**

```xml
<searchConfiguration name="The class is annotated as @FactoryName but doesn't implement WebDriverFactory or CapabilitiesFactory." text="@$FACTORY_NAME$&#10;public class $FACTORY_IMPLEMENTATION$ implements $FACTORY_INTERFACE$ {}" recursive="true" caseInsensitive="true" type="JAVA">
    <constraint name="__context__" within="" contains="" />
    <constraint name="FACTORY_NAME" regexp="org\.fluentlenium\.configuration\.FactoryName" within="" contains="" />
    <constraint name="FACTORY_IMPLEMENTATION" target="true" within="" contains="" />
    <constraint name="FACTORY_INTERFACE" regexp="org\.fluentlenium\.configuration\.CapabilitiesFactory|org\.fluentlenium\.configuration\.WebDriverFactory" minCount="0" maxCount="0" within="" contains="" />
</searchConfiguration>
```

## The value of the @FactoryName annotation is either empty or consists only of whitespaces

The `@FactoryName` annotation is designed to be used on classes implementing either the `CapabilitiesFactory` or the `WebDriverFactory` interface,
the value given in that annotation is the name that these particular capabilities and webdriver types may be referenced configured for FluentLenium. 

| Compliant code | Non-compliant code |
|---|---|
| <pre>@FactoryName("saucelabs")<br>public class SauceLabsDriverFactory implements WebDriverFactory {<br>}</pre> | <pre>@FactoryName("")<br>public class SauceLabsDriverFactory {<br>}</pre> |
| <pre>@FactoryName("browserstack")<br>public class BrowserStackCapabilitiesFactory implements CapabilitiesFactory {<br>}</pre> | <pre>@FactoryName("    ")<br>public class BrowserStackCapabilitiesFactory{<br>}</pre> |

**Template:**

```xml
<searchConfiguration name="The value of the @FactoryName annotation is either empty or consists only of whitespaces." text="@$FACTORY_NAME$ ($VALUE$)&#10;public class $FACTORY_IMPLEMENTATION$ implements $FACTORY_INTERFACE$ {}" recursive="true" caseInsensitive="true" type="JAVA">
    <constraint name="__context__" within="" contains="" />
    <constraint name="FACTORY_NAME" regexp="org\.fluentlenium\.configuration\.FactoryName" within="" contains="" />
    <constraint name="FACTORY_IMPLEMENTATION" within="" contains="" />
    <constraint name="FACTORY_INTERFACE" regexp="org\.fluentlenium\.configuration\.CapabilitiesFactory|org\.fluentlenium\.configuration\.WebDriverFactory" within="" contains="" />
    <constraint name="VALUE" regexp="^&quot;(( +)|)&quot;$" target="true" within="" contains="" />
</searchConfiguration>
```

## If a JUnit test class is annotated as @RunWith(Cucumber.class), having @FluentConfiguration will have no effect.

In case of Cucumber tests the `@FluentConfiguration` annotation is not meant to be used on JUnit test classes, rather on step definition classes
that extend `FluentCucumberTest`.

As for this inspection having the Cucumber's `@CucumberOptions` annotation on the JUnit test class is optional.

| Compliant code | Non-compliant code |
|---|---|
| <pre>@FluentConfiguration(driverLifecycle = DriverLifecycle.JVM)<br>public class BaseSteps extends FluentCucumberTest {<br>}</pre> | <pre>@RunWith(Cucumber.class)<br>@CucumberOptions(glue = "com.project", features = "some/features/path", plugin = "pretty")<br>@FluentConfiguration(driverLifecycle = DriverLifecycle.JVM)<br>public class SauceLabsTest extends FluentCucumberTest {<br>}</pre> |

**Template:**

```xml
<searchConfiguration name="JUnit test class with Cucumber runner is annotated as @FluentConfiguration. @FluentConfiguration in such case doesn't have effect. It may be removed and applied on a base step definitions class." text="@$RunWith$($Cucumber$.class)&#10;@$CucumberOptions$ ( )&#10;@$FluentConfiguration$ ( )&#10;class $TestClass$ {&#10;}" recursive="true" caseInsensitive="true" type="JAVA">
    <constraint name="__context__" within="" contains="" />
    <constraint name="TestClass" within="" contains="" />
    <constraint name="FluentConfiguration" regexp="org\.fluentlenium\.configuration\.FluentConfiguration" target="true" within="" contains="" />
    <constraint name="CucumberOptions" regexp="cucumber\.api\.CucumberOptions" minCount="0" within="" contains="" />
    <constraint name="RunWith" regexp="org\.junit\.runner\.RunWith" within="" contains="" />
    <constraint name="Cucumber" regexp="cucumber\.api\.junit\.Cucumber" within="" contains="" />
</searchConfiguration>
```

## FluentList declaration (using @Find… annotation) should be parameterized

The `FluentList` type has a generic parameter that specifies the type of element it will contain. Although not defining the generic type explicitly may be valid,
some projects may still want to enforce specifying it explicitly.

| Compliant code | Non-compliant code |
|---|---|
| <pre>public class Homepage extends FluentPage {<br>@FindBy(className = "hero")<br>public FluentList<FluentWebElement> heroImages;<br>}</pre> | <pre>public class Homepage extends FluentPage {<br>@FindBy(className = "hero")<br>public FluentList heroImages;<br>}</pre> |

**Template:**

```xml
<searchConfiguration name="FluentList field in a page object class doesn't have generic type" text="class $Class$ {&#10;    @$FindAnnotation$( )&#10;    $FluentList$ &lt;$GenericType$&gt; $Field$;&#10;}" recursive="true" caseInsensitive="true" type="JAVA">
    <constraint name="__context__" within="" contains="" />
    <constraint name="Class" within="" contains="" />
    <constraint name="Field" maxCount="2147483647" target="true" within="" contains="" />
    <constraint name="FluentList" regexp="org\.fluentlenium\.core\.domain\.FluentList" within="" contains="" />
    <constraint name="GenericType" minCount="0" maxCount="0" within="" contains="" />
    <constraint name="FindAnnotation" regexp="org\.openqa\.selenium\.support\.(FindBy|FindBys|FindAl)" within="" contains="" />
</searchConfiguration>
```

## Element attribute isEqualTo() assertion may be replaced with hasAttributeValue()

There are certain `FluentWebElement` properties whose assertion with `isEqualTo()` can be change to a more specific, more concise version
of that assertion.

| Compliant code | Non-compliant code |
|---|---|
| <pre>assertThat(homepage.hero).hasAttributeValue("data", "This is the data attribute.");</pre> | <pre>assertThat(homepage.hero.attribute("data")).isEqualTo("This is the data attribute.");</pre> |
| <pre>assertThat(homepage.search).hasValue("Search form value.");</pre> | <pre>assertThat(homepage.search.value()).isEqualTo("Search form value.");</pre> |

All these inspections can handle not just literal String values as arguments but String variables and constants as well. 

**Template (hasAttributeValue()):**

```xml
<replaceConfiguration name="Element attribute isEqualTo() assertion may be replaced with hasAttributeValue()" text="$Assertions$.assertThat($element$.attribute($attribute$)).isEqualTo($expectedValue$);" recursive="false" caseInsensitive="true" type="JAVA" pattern_context="default" reformatAccordingToStyle="false" shortenFQN="true" useStaticImport="true" replacement="org.fluentlenium.assertj.FluentLeniumAssertions.assertThat($element$).hasAttributeValue($attribute$, $expectedValue$);">
    <constraint name="__context__" target="true" within="" contains="" />
    <constraint name="attribute" nameOfExprType="java\.lang\.String" expressionTypes="java.lang.String" within="" contains="" />
    <constraint name="element" nameOfExprType="org\.fluentlenium\.core\.domain\.FluentWebElement" expressionTypes="org.fluentlenium.core.domain.FluentWebElement" within="" contains="" />
    <constraint name="expectedValue" within="" contains="" />
    <constraint name="Assertions" regexp="org\.assertj\.core\.api\.Assertions|org\.fluentlenium\.assertj\.FluentLeniumAssertions" minCount="0" within="" contains="" />
</replaceConfiguration>
```

**Template (hasValue()):**

```xml
<replaceConfiguration name="Element value isEqualTo() assertion may be replaced with hasValue()." text="$Assertions$.assertThat($element$.value()).isEqualTo($expectedValue$);" recursive="true" caseInsensitive="true" type="JAVA" reformatAccordingToStyle="false" shortenFQN="true" useStaticImport="true" replacement="org.fluentlenium.assertj.FluentLeniumAssertions.assertThat($element$).hasValue($expectedValue$);">
    <constraint name="__context__" target="true" within="" contains="" />
    <constraint name="element" nameOfExprType="org\.fluentlenium\.core\.domain\.FluentWebElement" expressionTypes="org.fluentlenium.core.domain.FluentWebElement" within="" contains="" />
    <constraint name="expectedValue" within="" contains="" />
    <constraint name="Assertions" regexp="org\.assertj\.core\.api\.Assertions|org\.fluentlenium\.assertj\.FluentLeniumAssertions" minCount="0" within="" contains="" />
</replaceConfiguration>
```

## Unnecessary double validation of element presence. Apply quick fix to remove the second assertion.

When waiting for an element to be displayed and the wait succeeds (the element is found displayed), right after that there is no need to
validate whether the element is displayed, the wait has already validated it.

| Compliant code | Non-compliant code |
|---|---|
| <pre>await().atMost(5, SECONDS).until(homepage.hero).displayed();</pre> | <pre>await().atMost(5, SECONDS).until(homepage.hero).displayed();<br>assertThat(homepage.hero).isDisplayed();</pre> |
| <pre>await().until(homepage.hero).displayed();</pre> | <pre>await().until(homepage.hero).displayed();<br>assertThat(homepage.hero).isDisplayed();</pre> | 

**Template (including an atMost() call):**

```xml
<replaceConfiguration name="Unnecessary double validation of element presence. Apply quick fix to remove the second assertion." text="await().atMost($time$, $timeUnit$).until($element$).displayed();&#10;assertThat($element$).isDisplayed();" recursive="false" caseInsensitive="true" type="JAVA" pattern_context="default" reformatAccordingToStyle="false" shortenFQN="true" useStaticImport="true" replacement="await().atMost($time$, $timeUnit$).until($element$).displayed();">
    <constraint name="__context__" within="" contains="" />
    <constraint name="element" within="" contains="" />
    <constraint name="time" within="" contains="" />
    <constraint name="timeUnit" nameOfExprType="java\.util\.concurrent\.TimeUnit" expressionTypes="java.util.concurrent.TimeUnit" within="" contains="" />
</replaceConfiguration>
```

**Template (without an atMost() call):**

```xml
<replaceConfiguration name="Unnecessary double validation of element presence. Apply quick fix to remove the second assertion." text="await().until($element$).displayed();&#10;assertThat($element$).isDisplayed();" recursive="false" caseInsensitive="true" type="JAVA" pattern_context="default" reformatAccordingToStyle="false" shortenFQN="true" useStaticImport="true" replacement="await().until($element$).displayed();">
    <constraint name="__context__" within="" contains="" />
    <constraint name="element" within="" contains="" />
</replaceConfiguration>
```

## FluentPage.hasExpectedUrl() call is not valid without @PageUrl annotation

When calling the `hasExpectedUrl` assertion on a `FluentPage` implementation, it is valid only when that FluentPage object is annotated as `@PageUrl`.

| Compliant code | Non-compliant code |
|---|---|
| <pre>@PageUrl("/")<br>public class Homepage extends FluentPage {<br>}<br><br>public class TestClass {<br>    @Page<br>    private Homepage homepage;<br><br>    public void testMethod() {<br>        FluentLeniumAssertions.assertThat(homepage).hasExpectedUrl();<br>    }<br>}</pre> | <pre>public class Homepage extends FluentPage {<br>}<br><br>public class TestClass {<br>    @Page<br>    private Homepage homepage;<br><br>    public void testMethod() {<br>        FluentLeniumAssertions.assertThat(homepage).hasExpectedUrl();<br>    }<br>}</pre> |

For now this works only when the `hasExpectedUrl()` is called immediately after `FluentLeniumAssertions.assertThat(fluentPage)` but not when there are other assertions between the two.

**Script filter ($FluentPage$)**

```groovy
!FluentPage.getType().resolve().hasAnnotation("org.fluentlenium.core.annotation.PageUrl")
```

**Template:**

```xml
<searchConfiguration name="FluentPage.hasExpectedUrl() call is not valid without @PageUrl annotation." text=" org.fluentlenium.assertj.FluentLeniumAssertions.assertThat($FluentPage$).hasExpectedUrl()" recursive="true" caseInsensitive="true" type="JAVA">
    <constraint name="__context__" within="" contains="" />
    <constraint name="FluentPage" script="&quot;!FluentPage.getType().resolve().hasAnnotation(&quot;org.fluentlenium.core.annotation.PageUrl&quot;)&quot;" nameOfExprType="org\.fluentlenium\.core\.FluentPage" expressionTypes="org.fluentlenium.core.FluentPage" exprTypeWithinHierarchy="true" within="" contains="" />
</searchConfiguration>
```

## FluentPage.hasExpectedElements() call is not valid without @FindBy annotation

When calling the `hasExpectedUrl` assertion on a `FluentPage` implementation, it is valid only when that FluentPage object is annotated as `@PageUrl`.

| Compliant code | Non-compliant code |
|---|---|
| <pre>@FindBy(css = ".homepage")<br>public class Homepage extends FluentPage {<br>}<br><br>public class TestClass {<br>    @Page<br>    private Homepage homepage;<br><br>    public void testMethod() {<br>        FluentLeniumAssertions.assertThat(homepage).hasExpectedElements();<br>    }<br>}</pre> | <pre>public class Homepage extends FluentPage {<br>}<br><br>public class TestClass {<br>    @Page<br>    private Homepage homepage;<br><br>    public void testMethod() {<br>        FluentLeniumAssertions.assertThat(homepage).hasExpectedElements();<br>    }<br>}</pre> |

For now this works only when the `hasExpectedElements()` is called immediately after `FluentLeniumAssertions.assertThat(fluentPage)` but not when there are other assertions between the two.

**Script filter ($FluentPage$)**

```groovy
!FluentPage.getType().resolve().hasAnnotation("org.openqa.selenium.support.FindBy")
```

**Template:**

```xml
<searchConfiguration name="FluentPage.hasExpectedElements() call is not valid without @FindBy annotation." text=" org.fluentlenium.assertj.FluentLeniumAssertions.assertThat($FluentPage$).hasExpectedElements()" recursive="true" caseInsensitive="true" type="JAVA">
    <constraint name="__context__" within="" contains="" />
    <constraint name="FluentPage" script="&quot;!FluentPage.getType().resolve().hasAnnotation(&quot;org.openqa.selenium.support.FindBy&quot;)&quot;" nameOfExprType="org\.fluentlenium\.core\.FluentPage" expressionTypes="org.fluentlenium.core.FluentPage" exprTypeWithinHierarchy="true" within="" contains="" />
</searchConfiguration>
```
