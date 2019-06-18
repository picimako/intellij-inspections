# Selenium

## Use css locator strategy instead of xpath.

Some projects may want to enforce using CSS selectors over XPath. This inspection highlights `@FindBy` annotations using xpath as locator strategy, e.g.:

```java
@FindBy(xpath = "//xpath/selector")
public WebElement element;
```

The element type ($FieldType$ template variable) is not restricted to anything, so that is may be used with any UI automation framework that builds on the `@FindBy` annotation.

**Template:**

```xml
<searchConfiguration name="Use css locator strategy instead of xpath in @FindBy annotation values." text="@org.openqa.selenium.support.FindBy($LocatorStrategy$ = &quot;$Locator$&quot;)&#10;@Modifier(&quot;Instance&quot;) $FieldType$ $Element$;" recursive="true" caseInsensitive="true" type="JAVA">
    <constraint name="__context__" within="" contains="" />
    <constraint name="Locator" within="" contains="" />
    <constraint name="FieldType" within="" contains="" />
    <constraint name="Element" within="" contains="" />
    <constraint name="LocatorStrategy" regexp="xpath" target="true" within="" contains="" />
</searchConfiguration>
```

## id based CSS selector value should not start with #

This inspection highlights elements like the one below:

```java
@FindBy(id = "#some-id")
public WebElement element;
```

**Template:**

```xml
<searchConfiguration name="id based CSS selector value should not start with #." text="@org.openqa.selenium.support.FindBy(id = &quot;$Locator$&quot;)&#10;@Modifier(&quot;Instance&quot;) $FieldType$ $Element$;" recursive="true" caseInsensitive="true" type="JAVA">
    <constraint name="__context__" within="" contains="" />
    <constraint name="FieldType" within="" contains="" />
    <constraint name="Element" within="" contains="" />
    <constraint name="Locator" regexp="^#.*" target="true" within="" contains="" />
</searchConfiguration>
```

## className based CSS selector value should not start with a dot

This inspection highlights elements like the one below:

```java
@FindBy(className = ".some-class")
public WebElement element;
```

**Template:**

```xml
<searchConfiguration name="className based CSS selector value should not start with a dot." text="@org.openqa.selenium.support.FindBy(className = &quot;$Locator$&quot;)&#10;@Modifier(&quot;Instance&quot;) $FieldType$ $Element$;" recursive="true" caseInsensitive="true" type="JAVA">
    <constraint name="__context__" within="" contains="" />
    <constraint name="FieldType" within="" contains="" />
    <constraint name="Element" within="" contains="" />
    <constraint name="Locator" regexp="^\..*" target="true" within="" contains="" />
</searchConfiguration>
```

## id, className and tagName selector values should not contain whitespace

These values are either incorrect selectors or were put together as a selector for the css locator strategy.

**Script filter ($Locator$):**

```groovy
Locator.text.contains(" ")
```

**Template:**

```xml
<searchConfiguration name="id, className and tagName selector values should not contain whitespace" text="@org.openqa.selenium.support.FindBy($LocatorStrategy$ = &quot;$Locator$&quot;)&#10;@Modifier(&quot;Instance&quot;) $FieldType$ $Element$;" recursive="true" caseInsensitive="true" type="JAVA">
    <constraint name="__context__" within="" contains="" />
    <constraint name="Locator" script="&quot;Locator.text.contains(&quot; &quot;)&quot;" target="true" within="" contains="" />
    <constraint name="FieldType" within="" contains="" />
    <constraint name="Element" within="" contains="" />
    <constraint name="LocatorStrategy" regexp="id|className|tagName" within="" contains="" />
</searchConfiguration>
```
