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

## FindBy: id based CSS selector value should not start with \#

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

## FindBy: className based CSS selector value should not start with a dot

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

## By: id based CSS selector value should not start with \#

This inspection highlights elements like the one below:

```java
By.id("#some-id")
```

**Template:**

```xml
<searchConfiguration name="By: id based CSS selector value should not start with #." text="org.openqa.selenium.By.id(&quot;$Locator$&quot;)" recursive="true" caseInsensitive="true" type="JAVA">
    <constraint name="__context__" within="" contains="" />
    <constraint name="Locator" regexp="^#.*" target="true" within="" contains="" />
</searchConfiguration>
```

## By: className based CSS selector value should not start with a dot

This inspection highlights elements like the one below:

```java
By.className(".some-class")
```

**Template:**

```xml
<searchConfiguration name="By: className based CSS selector value should not start with a dot." text="org.openqa.selenium.By.className(&quot;$Locator$&quot;)" recursive="true" caseInsensitive="true" type="JAVA">
    <constraint name="__context__" within="" contains="" />
    <constraint name="Locator" regexp="^\..*" target="true" within="" contains="" />
</searchConfiguration>
```

## FindBy: id, className and tagName selector values should not contain whitespace

These values (in `@org.openqa.selenium.FindBy`) are either incorrect selectors or were put together as a selector for the css locator strategy.

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

## By: id, className and tagName selector values should not contain whitespace

These values (in `org.openqa.selenium.By`) are either incorrect selectors or were put together as a selector for the css locator strategy.

**Script filter ($Locator$):**

```groovy
Locator.text.contains(" ")
```

**Template:**

```xml
<searchConfiguration name="By: id, className and tagName selector values should not contain whitespace" text="org.openqa.selenium.By.$LocatorStrategy$(&quot;$Locator$&quot;)" recursive="true" caseInsensitive="true" type="JAVA">
    <constraint name="__context__" within="" contains="" />
    <constraint name="Locator" script="&quot;Locator.text.contains(&quot; &quot;)&quot;" target="true" within="" contains="" />
    <constraint name="LocatorStrategy" regexp="id|className|tagName" within="" contains="" />
</searchConfiguration>
```

## CSS selector matching a tag name may be defined with tagName locator strategy

This inspection would signal a code snippet like the following, as incorrect:

```java
@FindBy(css = "div")
public WebElement gallery;
```

This inspection has support for all standard HTML tags listed on the [W3C Schools Tags page](https://www.w3schools.com/TAGs/) except:
- `<!-- -->`
- `<!DOCTYPE>`
- all other tags that are marked as not supported in HTML5

**Template:**

```xml
<searchConfiguration name="CSS selector matching a tag name may be defined with tagName locator strategy." text="@org.openqa.selenium.support.FindBy($locatorStrategy$ = &quot;$Locator$&quot;)&#10;@Modifier(&quot;Instance&quot;) $FieldType$ $Element$;" recursive="true" caseInsensitive="true" type="JAVA" pattern_context="default">
    <constraint name="__context__" within="" contains="" />
    <constraint name="FieldType" within="" contains="" />
    <constraint name="Element" within="" contains="" />
    <constraint name="Locator" regexp="^a|abbr|address|area|article|aside|audio|b|base|bdi|bdo|blockquote|body|br|button|canvas|caption|cite|code|col|colgroup|data|datalist|dd|del|details|dfn|dialog|div|dl|dt|em|embed|fieldset|figcaption|figure|footer|form|h1|h2|h3|h4|h5|h6|head|header|hr|html|i|iframe|img|input|ins|kbd|label|legend|li|link|main|map|mark|meta|meter|nav|noscript|object|ol|optgroup|option|output|p|param|picture|pre|progress|q|rp|rt|ruby|s|samp|script|section|select|small|source|span|strong|style|sub|summary|sup|svg|table|tbody|td|template|textarea|tfoot|th|thead|time|title|tr|track|u|ul|var|video|wbr$" target="true" within="" contains="" />
    <constraint name="locatorStrategy" regexp="css" within="" contains="" />
</searchConfiguration>
```

## By: CSS selector matching a tag name may be defined with tagName locator strategy

This inspection would signal a code snippet like the following, as incorrect:

```java
By gallery = By.cssSelector("div");
```

And also provides a replacement option, so that the code snippet above can be replaced with the following:

```java
By gallery = By.tagName("div");
```

This inspection has support for all standard HTML tags listed on the [W3C Schools Tags page](https://www.w3schools.com/TAGs/) except:
- `<!-- -->`
- `<!DOCTYPE>`
- all other tags that are marked as not supported in HTML5

**Template:**

```xml
<replaceConfiguration name="By: CSS selector matching a tag name may be defined with tagName locator strategy." text="org.openqa.selenium.By.$locatorStrategy$(&quot;$Locator$&quot;)" recursive="false" caseInsensitive="true" type="JAVA" pattern_context="default" reformatAccordingToStyle="true" shortenFQN="true" replacement="org.openqa.selenium.By.tagName(&quot;$Locator$&quot;)">
    <constraint name="__context__" within="" contains="" />
    <constraint name="Locator" regexp="^a|abbr|address|area|article|aside|audio|b|base|bdi|bdo|blockquote|body|br|button|canvas|caption|cite|code|col|colgroup|data|datalist|dd|del|details|dfn|dialog|div|dl|dt|em|embed|fieldset|figcaption|figure|footer|form|h1|h2|h3|h4|h5|h6|head|header|hr|html|i|iframe|img|input|ins|kbd|label|legend|li|link|main|map|mark|meta|meter|nav|noscript|object|ol|optgroup|option|output|p|param|picture|pre|progress|q|rp|rt|ruby|s|samp|script|section|select|small|source|span|strong|style|sub|summary|sup|svg|table|tbody|td|template|textarea|tfoot|th|thead|time|title|tr|track|u|ul|var|video|wbr$" within="" contains="" />
    <constraint name="locatorStrategy" regexp="cssSelector" target="true" within="" contains="" />
</replaceConfiguration>
```

## By element is used in String concatenation

It can happen that you want to concatenate multiple CSS selector snippets because of parameterization or such.
However one may make the mistake of concatenating a `By` object to a String which won't result in a valid CSS selector.
This inspection is meant to mark this kind of issues.

This inspection would signal a code snippet like the following, as incorrect:

```java
By gallery = By.cssSelector(".gallery");

String selectorRight = "div.some-other-selector " + gallery;
String selectorLeft = gallery + " div.some-other-selector";
```

The template allows to have additional concatenations on either side.

**Template:**

For when the `By` object is at the right-hand side of the concatenation:

```xml
<searchConfiguration name="By element is used in String concatenation. You probably wanted to concatenate the CSS selector string." text="$other$ + $astring$ + $byElement$" recursive="true" caseInsensitive="true" type="JAVA" pattern_context="default">
  <constraint name="__context__" within="" contains="" />
  <constraint name="byElement" nameOfExprType="org\.openqa\.selenium\.By" expressionTypes="org.openqa.selenium.By" exprTypeWithinHierarchy="true" within="" contains="" />
  <constraint name="astring" nameOfExprType="java\.lang\.String" expressionTypes="java.lang.String" within="" contains="" />
  <constraint name="other" minCount="0" maxCount="2147483647" within="" contains="" />
</searchConfiguration>
```

For when the `By` object is at the left-hand side of the concatenation:

```xml
<searchConfiguration name="By element is used in String concatenation. You probably wanted to concatenate the CSS selector string." text="$byElement$ + $astring$+ $other$" recursive="true" caseInsensitive="true" type="JAVA" pattern_context="default">
  <constraint name="__context__" within="" contains="" />
  <constraint name="byElement" nameOfExprType="org\.openqa\.selenium\.By" expressionTypes="org.openqa.selenium.By" exprTypeWithinHierarchy="true" within="" contains="" />
  <constraint name="astring" nameOfExprType="java\.lang\.String" expressionTypes="java.lang.String" within="" contains="" />
  <constraint name="other" minCount="0" maxCount="2147483647" within="" contains="" />
</searchConfiguration>
```

## WebElement is used in String concatenation

It can happen that you want to concatenate multiple CSS selector snippets because of parameterization or such.
However one may make the mistake of concatenating a `WebElement` object to a String which won't result in a valid CSS
selector. This inspection is meant to mark this kind of issues.

This inspection would signal a code snippet like the following, as incorrect:

```java
@FindBy(className = "gallery")
WebElement gallery;

String selectorRight = "div.some-other-selector " + gallery;
String selectorLeft = gallery + " div.some-other-selector";
```

The template allows to have additional concatenations on either side.

**Template:**

For when the `WebElement` object is at the right-hand side of the concatenation:

```xml
<searchConfiguration name="WebElement is used in String concatenation. You probably wanted to concatenate the CSS selector string." text="$other$ + $astring$ + $webElement$" recursive="true" caseInsensitive="true" type="JAVA" pattern_context="default">
  <constraint name="__context__" within="" contains="" />
  <constraint name="astring" nameOfExprType="java\.lang\.String" expressionTypes="java.lang.String" within="" contains="" />
  <constraint name="webElement" nameOfExprType="org\.openqa\.selenium\.WebElement" expressionTypes="org.openqa.selenium.WebElement" exprTypeWithinHierarchy="true" within="" contains="" />
  <constraint name="other" minCount="0" maxCount="2147483647" within="" contains="" />
</searchConfiguration>
```

For when the `WebElement` object is at the left-hand side of the concatenation:

```xml
<searchConfiguration name="WebElement is used in String concatenation. You probably wanted to concatenate the CSS selector string." text="$webElement$ + $astring$ + $other$" recursive="true" caseInsensitive="true" type="JAVA" pattern_context="default">
  <constraint name="__context__" within="" contains="" />
  <constraint name="astring" nameOfExprType="java\.lang\.String" expressionTypes="java.lang.String" within="" contains="" />
  <constraint name="webElement" nameOfExprType="org\.openqa\.selenium\.WebElement" expressionTypes="org.openqa.selenium.WebElement" exprTypeWithinHierarchy="true" within="" contains="" />
  <constraint name="other" minCount="0" maxCount="2147483647" within="" contains="" />
</searchConfiguration>
```

## The Actions#keyDown and Actions#keyUp methods accept only modifier keys

Based on their javadoc they both accept only modifier keys such as `Keys.SHIFT`, `Keys.ALT` and `Keys.CONTROL`, thus
this inspection will signal a code snippet like the following, as incorrect:

```java
Actions actions = new Actions(driver).keyDown(Keys.CANCEL);
```

This template has support for both signatures of `keyDown` and `keyUp` as well.

**Script filter ($modifierKey$)**

```groovy
"org.openqa.selenium.Keys".equals(modifierKey?.getType()?.getCanonicalText())
    && !modifierKey?.getQualifiedName().matches("(Keys\\.)?(SHIFT|ALT|CONTROL)")
```

**Template:**

```xml
<searchConfiguration name="Invalid key provided. The keyDown and keyUp methods accept only modifier keys: SHIFT, ALT, CONTROL" text="$actions$.$keyDownUp$($WebElement$, $modifierKey$)" recursive="true" caseInsensitive="true" type="JAVA" pattern_context="default">
    <constraint name="__context__" within="" contains="" />
    <constraint name="actions" nameOfExprType="org\.openqa\.selenium\.interactions\.Actions" expressionTypes="org.openqa.selenium.interactions.Actions" within="" contains="" />
    <constraint name="modifierKey" script="&quot;&quot;org.openqa.selenium.Keys&quot;.equals(modifierKey?.getType()?.getCanonicalText()) &amp;&amp; !modifierKey?.getQualifiedName().matches(&quot;(Keys\\.)?(SHIFT|ALT|CONTROL)&quot;)&quot;" target="true" within="" contains="" />
    <constraint name="keyDownUp" regexp="keyDown|keyUp" within="" contains="" />
    <constraint name="WebElement" minCount="0" within="" contains="" />
</searchConfiguration>
```
