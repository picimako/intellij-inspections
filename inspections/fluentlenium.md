# FluentLenium

FluentLenium inspections are now also available in [FluentLenium's repository](https://github.com/FluentLenium/FluentLenium/tree/develop/fluentlenium-ide-support/intellij/inspections).

The changes were added in [FluentLenium/711](https://github.com/FluentLenium/FluentLenium/issues/711).

## Inspections

#### FluentConfiguration has remoteUrl but webDriver is not defined as "remote"
```xml
<searchConfiguration name="FluentConfiguration has remoteUrl but webDriver is not defined as &quot;remote&quot;" text="@org.fluentlenium.configuration.FluentConfiguration(webDriver = &quot;$NOT_REMOTE$&quot;, remoteUrl = $REMOTE_URL$)" recursive="true" caseInsensitive="true" type="JAVA">
    <constraint name="__context__" within="" contains="" />
    <constraint name="NOT_REMOTE" regexp="remote" target="true" negateName="true" within="" contains="" />
    <constraint name="REMOTE_URL" within="" contains="" />
</searchConfiguration>
```

#### FluentConfiguration has webDriver defined as "remote" but there is no remoteUrl
```xml
<searchConfiguration name="FluentConfiguration has webDriver defined as &quot;remote&quot; but there is no remoteUrl." text="@org.fluentlenium.configuration.FluentConfiguration(webDriver = &quot;$REMOTE$&quot;, $REMOTE_URL_ATTR$ = $REMOTE_URL$)" recursive="true" caseInsensitive="true" type="JAVA">
    <constraint name="__context__" within="" contains="" />
    <constraint name="REMOTE_URL" minCount="0" maxCount="0" within="" contains="" />
    <constraint name="REMOTE" regexp="remote" target="true" within="" contains="" />
    <constraint name="REMOTE_URL_ATTR" regexp="remoteUrl" minCount="0" maxCount="0" within="" contains="" />
</searchConfiguration>
```

#### CapabilitiesFactory and WebDriverFactory implementations must be annotated as @FactoryName to provide a reference to them
```xml
<searchConfiguration name="CapabilitiesFactory and WebDriverFactory implementations must be annotated as @FactoryName to provide a reference to them." text="@$FACTORY_NAME$&#10;public class $FACTORY_IMPLEMENTATION$ implements $FACTORY_INTERFACE$ {}" recursive="true" caseInsensitive="true" type="JAVA">
    <constraint name="__context__" within="" contains="" />
    <constraint name="FACTORY_INTERFACE" regexp="org\.fluentlenium\.configuration\.CapabilitiesFactory|org\.fluentlenium\.configuration\.WebDriverFactory" within="" contains="" />
    <constraint name="FACTORY_NAME" regexp="org\.fluentlenium\.configuration\.FactoryName" minCount="0" maxCount="0" within="" contains="" />
    <constraint name="FACTORY_IMPLEMENTATION" target="true" within="" contains="" />
</searchConfiguration>
```

#### PageUrl annotated class should extend FluentPage
```xml
<searchConfiguration name="PageUrl annotated class should extend FluentPage." text="@$PAGE_URL_ANNOTATION$( )&#10;public class $PAGE_OBJECT$ extends $FLUENT_PAGE$ { }" recursive="true" caseInsensitive="true" type="JAVA">
    <constraint name="__context__" within="" contains="" />
    <constraint name="PAGE_OBJECT" target="true" within="" contains="" />
    <constraint name="PAGE_URL_ANNOTATION" regexp="org\.fluentlenium\.core\.annotation\.PageUrl" within="" contains="" />
    <constraint name="FLUENT_PAGE" regexp="org\.fluentlenium\.core\.FluentPage" minCount="0" maxCount="0" within="" contains="" />
</searchConfiguration>
```

#### You can call id() on a FluentWebElement instead of attribute("id")
```xml
<replaceConfiguration name="You can call id() on a FluentWebElement instead of attribute(&quot;id&quot;)." text="$FluentWebElement$.attribute(&quot;id&quot;)" recursive="false" caseInsensitive="true" type="JAVA" reformatAccordingToStyle="false" shortenFQN="false" replacement="$FluentWebElement$.id()">
    <constraint name="__context__" within="" contains="" />
    <constraint name="FluentWebElement" nameOfExprType="org\.fluentlenium\.core\.domain\.FluentWebElement" expressionTypes="org.fluentlenium.core.domain.FluentWebElement" within="" contains="" />
</replaceConfiguration>
```

#### You can call textContent() on a FluentWebElement instead of attribute("textContent")
```xml
<replaceConfiguration name="You can call textContent() on a FluentWebElement instead of attribute(&quot;textContent&quot;)." text="$FluentWebElement$.attribute(&quot;textContent&quot;)" recursive="false" caseInsensitive="true" type="JAVA" reformatAccordingToStyle="false" shortenFQN="false" replacement="$FluentWebElement$.textContent()">
    <constraint name="__context__" within="" contains="" />
    <constraint name="FluentWebElement" nameOfExprType="org\.fluentlenium\.core\.domain\.FluentWebElement" expressionTypes="org.fluentlenium.core.domain.FluentWebElement" within="" contains="" />
</replaceConfiguration>
```

#### You can call value() on a FluentWebElement instead of attribute("value")
```xml
<replaceConfiguration name="You can call value() on a FluentWebElement instead of attribute(&quot;value&quot;)." text="$FluentWebElement$.attribute(&quot;value&quot;)" recursive="false" caseInsensitive="true" type="JAVA" reformatAccordingToStyle="false" shortenFQN="false" replacement="$FluentWebElement$.value()">
    <constraint name="__context__" within="" contains="" />
    <constraint name="FluentWebElement" nameOfExprType="org\.fluentlenium\.core\.domain\.FluentWebElement" expressionTypes="org.fluentlenium.core.domain.FluentWebElement" within="" contains="" />
</replaceConfiguration>
```

#### You can call name() on a FluentWebElement instead of attribute("name")
```xml
<replaceConfiguration name="You can call name() on a FluentWebElement instead of attribute(&quot;name&quot;)." text="$FluentWebElement$.attribute(&quot;name&quot;)" recursive="false" caseInsensitive="true" type="JAVA" reformatAccordingToStyle="false" shortenFQN="false" replacement="$FluentWebElement$.name()">
    <constraint name="__context__" within="" contains="" />
    <constraint name="FluentWebElement" nameOfExprType="org\.fluentlenium\.core\.domain\.FluentWebElement" expressionTypes="org.fluentlenium.core.domain.FluentWebElement" within="" contains="" />
</replaceConfiguration>
```

#### You can call first() on a FluentList instead of get(0)
```xml
<replaceConfiguration name="You can call first() on a FluentList instead of get(0)." text="$FluentList$.get(0)" recursive="false" caseInsensitive="true" type="JAVA" reformatAccordingToStyle="false" shortenFQN="false" replacement="$FluentList$.first()">
    <constraint name="__context__" within="" contains="" />
    <constraint name="FluentList" nameOfExprType="org\.fluentlenium\.core\.domain\.FluentList" expressionTypes="org.fluentlenium.core.domain.FluentList" within="" contains="" />
</replaceConfiguration>
```

#### The value of the @PageUrl annotation must start with /, http://, https://
```xml
<searchConfiguration name="The value of the @PageUrl annotation must start with /, http://, https://." text="@$PageUrl$ ($Value$)&#10;class $Class$ { }" recursive="true" caseInsensitive="true" type="JAVA">
    <constraint name="__context__" within="" contains="" />
    <constraint name="PageUrl" regexp="org\.fluentlenium\.core\.annotation\.PageUrl" target="true" within="" contains="" />
    <constraint name="Value" regexp="(^(&quot;\/|&quot;https?:\/\/).*&quot;$)|(value = (&quot;\/|&quot;https?:\/\/).*&quot;)" negateName="true" within="" contains="" />
    <constraint name="Class" within="" contains="" />
</searchConfiguration>
```

#### There is no actual assertion called from FluentLeniumAssertions
```xml
<searchConfiguration name="There is no actual assertion called from FluentLeniumAssertions." text="org.fluentlenium.assertj.FluentLeniumAssertions.$ASSERTION_METHOD$($PARAMETER$);" recursive="true" caseInsensitive="true" type="JAVA">
    <constraint name="ASSERTION_METHOD" regexp="assertThat" within="" contains="" />
    <constraint name="PARAMETER" within="" contains="" />
    <constraint name="__context__" within="" contains="" />
</searchConfiguration>
```

#### @Page annotated field is instantiated explicitly. Instantiation can be removed as the field is injected by FluentLenium
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

#### @Page annotated field is static. It should be used as an instance field
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

#### The class is annotated as @FactoryName but doesn't implement WebDriverFactory or CapabilitiesFactory
```xml
<searchConfiguration name="The class is annotated as @FactoryName but doesn't implement WebDriverFactory or CapabilitiesFactory." text="@$FACTORY_NAME$&#10;public class $FACTORY_IMPLEMENTATION$ implements $FACTORY_INTERFACE$ {}" recursive="true" caseInsensitive="true" type="JAVA">
    <constraint name="__context__" within="" contains="" />
    <constraint name="FACTORY_NAME" regexp="org\.fluentlenium\.configuration\.FactoryName" within="" contains="" />
    <constraint name="FACTORY_IMPLEMENTATION" target="true" within="" contains="" />
    <constraint name="FACTORY_INTERFACE" regexp="org\.fluentlenium\.configuration\.CapabilitiesFactory|org\.fluentlenium\.configuration\.WebDriverFactory" minCount="0" maxCount="0" within="" contains="" />
</searchConfiguration>
```

#### The value of the @FactoryName annotation is either empty or consists only of whitespaces
```xml
<searchConfiguration name="The value of the @FactoryName annotation is either empty or consists only of whitespaces." text="@$FACTORY_NAME$ ($VALUE$)&#10;public class $FACTORY_IMPLEMENTATION$ implements $FACTORY_INTERFACE$ {}" recursive="true" caseInsensitive="true" type="JAVA">
    <constraint name="__context__" within="" contains="" />
    <constraint name="FACTORY_NAME" regexp="org\.fluentlenium\.configuration\.FactoryName" within="" contains="" />
    <constraint name="FACTORY_IMPLEMENTATION" within="" contains="" />
    <constraint name="FACTORY_INTERFACE" regexp="org\.fluentlenium\.configuration\.CapabilitiesFactory|org\.fluentlenium\.configuration\.WebDriverFactory" within="" contains="" />
    <constraint name="VALUE" regexp="^&quot;(( +)|)&quot;$" target="true" within="" contains="" />
</searchConfiguration>
```

#### Selenium Find... annotated class should extend FluentPage
Since: 4.2.2
```xml
<searchConfiguration name="Selenium Find... annotated class should extend FluentPage." text="@$FIND_ANNOTATION$( )&#10;public class $PAGE_OBJECT$ extends $FLUENT_PAGE$ { }" recursive="true" caseInsensitive="true" type="JAVA">
    <constraint name="__context__" within="" contains="" />
    <constraint name="PAGE_OBJECT" target="true" within="" contains="" />
    <constraint name="FLUENT_PAGE" regexp="org\.fluentlenium\.core\.FluentPage" minCount="0" maxCount="0" within="" contains="" />
    <constraint name="FIND_ANNOTATION" regexp="org\.openqa\.selenium\.support\.(FindBy|FindBys|FindAll)" within="" contains="" />
</searchConfiguration>
```

#### If a JUnit test class is annotated as @RunWith(Cucumber.class) and optionally as @CucumberOptions, having @FluentConfiguration will have no effect.

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

#### FluentList declaration (using @Find… annotation) should be parameterized

This might not be generally valid, but some projects may want to enforce specifying the generic type.

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