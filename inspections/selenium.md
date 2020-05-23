# Selenium

- Selenium website: https://selenium.dev
- Selenium on GitHub: https://github.com/SeleniumHQ/selenium

## Table of contents

#### Locators
- [Use css locator strategy instead of xpath.](#use-css-locator-strategy-instead-of-xpath)
- [id, className and tagName selector values should not contain whitespace](#id-classname-and-tagname-selector-values-should-not-contain-whitespace)
- [CSS selector matching a tag name may be defined with tagName locator strategy](#css-selector-matching-a-tag-name-may-be-defined-with-tagname-locator-strategy)

#### @FindBy
- [FindBy: id based CSS selector value should not start with \#](#findby-id-based-css-selector-value-should-not-start-with-)
- [FindBy: className based CSS selector value should not start with a dot](#findby-classname-based-css-selector-value-should-not-start-with-a-dot)

#### By
- [By: id based CSS selector value should not start with \#](#by-id-based-css-selector-value-should-not-start-with-)
- [By: className based CSS selector value should not start with a dot](#by-classname-based-css-selector-value-should-not-start-with-a-dot)
- [By or WebElement is used in String concatenation](#by-or-webelement-is-used-in-string-concatenation)

#### Actions
- [The Actions#keyDown and Actions#keyUp methods accept only modifier keys](#the-actionskeydown-and-actionskeyup-methods-accept-only-modifier-keys)
- [Certain consecutive calls from Actions can be simplified](#certain-consecutive-calls-from-actions-can-be-simplified)

## Use css locator strategy instead of xpath.

Some projects may want to enforce using CSS selectors over XPath. This may happen because of a couple of things:
- developers'/testers' preference
- CSS selectors' better general performance over XPath
- Xpath engines may differ between browsers
- based on the application under test, CSS selectors are enough (maybe combined with JQuery selectors), and there's no need for xpath's capabilities.

This inspection highlights `@FindBy` annotations using xpath as locator strategy:

| Compliant code | Non-compliant code |
|---|---|
| <pre>@FindBy(css = "div > p")<br>public WebElement element;</pre> | <pre>@FindBy(xpath = "//div/p")<br>public WebElement element;</pre> |


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

In CSS selectors the # character marks an ID attribute identifier but when id locator strategy is used directly
adding the # also is invalid.

| Compliant code | Non-compliant code |
|---|---|
| <pre>@FindBy(id = "some-id")<br>public WebElement element;</pre> | <pre>@FindBy(id = "#some-id")<br>public WebElement element;</pre> |

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

In CSS selectors the . character marks a class attribute identifier but when className locator strategy is used directly
adding the . also is invalid.

| Compliant code | Non-compliant code |
|---|---|
| <pre>@FindBy(className = "some-class")<br>public WebElement element;</pre> | <pre>@FindBy(className = ".some-class")<br>public WebElement element;</pre> |

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

In CSS selectors the # character marks an ID attribute identifier but when id locator strategy is used directly
adding the # also is invalid.

| Compliant code | Non-compliant code |
|---|---|
| `By.id("some-id")` | `By.id("#some-id")` |

**Template:**

```xml
<searchConfiguration name="By: id based CSS selector value should not start with #." text="org.openqa.selenium.By.id(&quot;$Locator$&quot;)" recursive="true" caseInsensitive="true" type="JAVA">
    <constraint name="__context__" within="" contains="" />
    <constraint name="Locator" regexp="^#.*" target="true" within="" contains="" />
</searchConfiguration>
```

## By: className based CSS selector value should not start with a dot

In CSS selectors the . character marks a class attribute identifier but when className locator strategy is used directly
adding the . also is invalid.

| Compliant code | Non-compliant code |
|---|---|
| `By.className("some-class")` | `By.className(".some-class")` |

**Template:**

```xml
<searchConfiguration name="By: className based CSS selector value should not start with a dot." text="org.openqa.selenium.By.className(&quot;$Locator$&quot;)" recursive="true" caseInsensitive="true" type="JAVA">
    <constraint name="__context__" within="" contains="" />
    <constraint name="Locator" regexp="^\..*" target="true" within="" contains="" />
</searchConfiguration>
```

## id, className and tagName selector values should not contain whitespace

A whitespace between parts of a CSS selector means it *Selects all X elements inside Y elements*, see the official [CSS Selector Reference](https://www.w3schools.com/cssref/css_selectors.asp).
Therefore these values (defined either in `@org.openqa.selenium.FindBy` annotation or `org.openqa.selenium.By` objects) are either incorrect selectors or were created to be used with the **css** locator strategy.

| Compliant code | Non-compliant code |
|---|---|
| <pre>@FindBy(className = "some-class")<br>public WebElement element;</pre> | <pre>@FindBy(className = "some class")<br>public WebElement element;</pre> |
| <pre>@FindBy(id = "some-id")<br>public WebElement element;</pre> | <pre>@FindBy(id = "some id")<br>public WebElement element;</pre> |
| <pre>@FindBy(tagName = "div")<br>public WebElement element;</pre> | <pre>@FindBy(tagName = "custom tag")<br>public WebElement element;</pre> |
| `By.className("some-class")` | `By.className("some class")` |
| `By.id("some-id")` | `By.id("some id")` |
| `By.tagName("div")` | `By.tagName("custom tag")` |

**Script filter ($Locator$):**

```groovy
Locator.text.contains(" ")
```

**Template for @FindBy:**

```xml
<searchConfiguration name="FindBy: id, className and tagName selector values should not contain whitespace" text="@org.openqa.selenium.support.FindBy($LocatorStrategy$ = &quot;$Locator$&quot;)&#10;@Modifier(&quot;Instance&quot;) $FieldType$ $Element$;" recursive="true" caseInsensitive="true" type="JAVA">
    <constraint name="__context__" within="" contains="" />
    <constraint name="Locator" script="&quot;Locator.text.contains(&quot; &quot;)&quot;" target="true" within="" contains="" />
    <constraint name="FieldType" within="" contains="" />
    <constraint name="Element" within="" contains="" />
    <constraint name="LocatorStrategy" regexp="id|className|tagName" within="" contains="" />
</searchConfiguration>
```

**Template for By:**

```xml
<searchConfiguration name="By: id, className and tagName selector values should not contain whitespace" text="org.openqa.selenium.By.$LocatorStrategy$(&quot;$Locator$&quot;)" recursive="true" caseInsensitive="true" type="JAVA">
    <constraint name="__context__" within="" contains="" />
    <constraint name="Locator" script="&quot;Locator.text.contains(&quot; &quot;)&quot;" target="true" within="" contains="" />
    <constraint name="LocatorStrategy" regexp="id|className|tagName" within="" contains="" />
</searchConfiguration>
```

## CSS selector matching a tag name may be defined with tagName locator strategy

In some cases it might be more readable to use the tagName locator strategy when targeting elements by their tag name
instead of using the css locator strategy.

However tagName and css locator strategies may be used in a mixed manner within projects, this inspection would
highlight code snippets in places too where you intentionally not use `tagName`.

| Compliant code | Non-compliant code |
|---|---|
| <pre>@FindBy(tagName = "div")<br>public WebElement gallery;</pre> | <pre>@FindBy(css = "div")<br>public WebElement gallery;</pre> |
| `By gallery = By.tagName("div");` | `By gallery = By.cssSelector("div");` |

This inspection has support for all standard HTML tags listed on the [W3C Schools Tags page](https://www.w3schools.com/TAGs/) except:
- `<!-- -->`
- `<!DOCTYPE>`
- all other tags that are marked as not supported in HTML5

**Template for @FindBy:**

```xml
<searchConfiguration name="CSS selector matching a tag name may be defined with tagName locator strategy." text="@org.openqa.selenium.support.FindBy($locatorStrategy$ = &quot;$Locator$&quot;)&#10;@Modifier(&quot;Instance&quot;) $FieldType$ $Element$;" recursive="true" caseInsensitive="true" type="JAVA" pattern_context="default">
    <constraint name="__context__" within="" contains="" />
    <constraint name="FieldType" within="" contains="" />
    <constraint name="Element" within="" contains="" />
    <constraint name="Locator" regexp="^a|abbr|address|area|article|aside|audio|b|base|bdi|bdo|blockquote|body|br|button|canvas|caption|cite|code|col|colgroup|data|datalist|dd|del|details|dfn|dialog|div|dl|dt|em|embed|fieldset|figcaption|figure|footer|form|h1|h2|h3|h4|h5|h6|head|header|hr|html|i|iframe|img|input|ins|kbd|label|legend|li|link|main|map|mark|meta|meter|nav|noscript|object|ol|optgroup|option|output|p|param|picture|pre|progress|q|rp|rt|ruby|s|samp|script|section|select|small|source|span|strong|style|sub|summary|sup|svg|table|tbody|td|template|textarea|tfoot|th|thead|time|title|tr|track|u|ul|var|video|wbr$" target="true" within="" contains="" />
    <constraint name="locatorStrategy" regexp="css" within="" contains="" />
</searchConfiguration>
```

**Template for By:**

```xml
<replaceConfiguration name="By: CSS selector matching a tag name may be defined with tagName locator strategy." text="org.openqa.selenium.By.$locatorStrategy$(&quot;$Locator$&quot;)" recursive="false" caseInsensitive="true" type="JAVA" pattern_context="default" reformatAccordingToStyle="true" shortenFQN="true" replacement="org.openqa.selenium.By.tagName(&quot;$Locator$&quot;)">
    <constraint name="__context__" within="" contains="" />
    <constraint name="Locator" regexp="^a|abbr|address|area|article|aside|audio|b|base|bdi|bdo|blockquote|body|br|button|canvas|caption|cite|code|col|colgroup|data|datalist|dd|del|details|dfn|dialog|div|dl|dt|em|embed|fieldset|figcaption|figure|footer|form|h1|h2|h3|h4|h5|h6|head|header|hr|html|i|iframe|img|input|ins|kbd|label|legend|li|link|main|map|mark|meta|meter|nav|noscript|object|ol|optgroup|option|output|p|param|picture|pre|progress|q|rp|rt|ruby|s|samp|script|section|select|small|source|span|strong|style|sub|summary|sup|svg|table|tbody|td|template|textarea|tfoot|th|thead|time|title|tr|track|u|ul|var|video|wbr$" within="" contains="" />
    <constraint name="locatorStrategy" regexp="cssSelector" target="true" within="" contains="" />
</replaceConfiguration>
```

## By or WebElement is used in String concatenation

It can happen that you want to concatenate multiple CSS selector snippets because of parameterization or such.
However, one may make the mistake of concatenating a `By` or `WebElement` object to a String which won't result in a valid CSS selector.
These inspections are meant to mark this issue.

| Compliant code | Non-compliant code |
|---|---|
| <pre>String gallery = ".gallery";<br>String selectorRight = "div.some-other-selector " + gallery;<br>String selectorLeft = gallery + " div.some-other-selector";</pre> | <pre>By gallery = By.cssSelector(".gallery");<br>String selectorRight = "div.some-other-selector " + gallery;<br>String selectorLeft = gallery + " div.some-other-selector";</pre> |
| <pre>String gallery = ".gallery";<br>String selectorRight = "div.some-other-selector " + gallery;<br>String selectorLeft = gallery + " div.some-other-selector";</pre> | <pre>@FindBy(css = ".gallery")<br>WebElement gallery;<br>String selectorRight = "div.some-other-selector " + gallery;<br>String selectorLeft = gallery + " div.some-other-selector";</pre> |

These templates allow having additional concatenations on each side.

**Templates:**

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

Based on their javadoc both `Actions#keyDown` and `Actions#keyUp` accept only modifier keys such as `Keys.SHIFT`, `Keys.ALT` and `Keys.CONTROL`.

| Compliant code | Non-compliant code |
|---|---|
| `Actions actions = new Actions(driver).keyDown(Keys.ALT);` | `Actions actions = new Actions(driver).keyDown(Keys.CANCEL);` |
| `Actions actions = new Actions(driver).keyDown(aWebElement, Keys.ALT);` | `Actions actions = new Actions(driver).keyDown(aWebElement, Keys.CANCEL);` |

This template has support for both signatures of `keyDown` and `keyUp` as well.

**Script filter ($modifierKey$)**

```groovy
"org.openqa.selenium.Keys".equals(modifierKey?.getType()?.getCanonicalText())
    && !modifierKey?.getQualifiedName()?.matches("(Keys\\.)?(SHIFT|ALT|CONTROL)")
```

**Template:**

```xml
<searchConfiguration name="Invalid key provided. The keyDown and keyUp methods accept only modifier keys: SHIFT, ALT, CONTROL" text="$actions$.$keyDownUp$($WebElement$, $modifierKey$)" recursive="true" caseInsensitive="true" type="JAVA" pattern_context="default">
    <constraint name="__context__" within="" contains="" />
    <constraint name="actions" nameOfExprType="org\.openqa\.selenium\.interactions\.Actions" expressionTypes="org.openqa.selenium.interactions.Actions" within="" contains="" />
    <constraint name="modifierKey" script="&quot;&quot;org.openqa.selenium.Keys&quot;.equals(modifierKey?.getType()?.getCanonicalText()) &amp;&amp; !modifierKey?.getQualifiedName()?.matches(&quot;(Keys\\.)?(SHIFT|ALT|CONTROL)&quot;)&quot;" target="true" within="" contains="" />
    <constraint name="keyDownUp" regexp="keyDown|keyUp" within="" contains="" />
    <constraint name="WebElement" minCount="0" within="" contains="" />
</searchConfiguration>
```

## Certain consecutive calls from Actions can be simplified

According to the method level javadocs in the `Actions` class some methods are equivalent to other multiple other consecutive calls. These are:

| Method call | Equivalent to |
|---|---|
| `Actions.click(WebElement)` | `Actions.moveToElement(WebElement).click()` |
| `Actions.clickAndHold(WebElement)` | `Actions.moveToElement(WebElement).clickAndHold()` |
| `Actions.doubleClick(WebElement)` | `Actions.moveToElement(WebElement).doubleClick()` |
| `Actions.release(WebElement)` | `Actions.moveToElement(WebElement).release()` |
| `Actions(driver).sendKeys(WebElement, String)` | `Actions(driver).click(WebElement).sendKeys(String)` |
| `Actions.perform()` | `Actions.build().perform()` |

All the templates below provide quick fixes to replace the consecutive calls to their simpler variants.

If the chained methods have other methods than the two mentioned consecutive calls, they might be highlighted as well by IntelliJ due to
the type match with `Actions`, but the quick fix works properly regardless of that.

**Examples:**

| Non-compliant code | Replacement |
|---|---|
| <pre>WebDriver driver = new FirefoxDriver();<br>WebElement element;<br>Actions actions = new Actions(driver).moveToElement(element).click();</pre> | <pre>WebDriver driver = new FirefoxDriver();<br>WebElement element;<br>Actions actions = new Actions(driver).click(element);</pre> |
| <pre>WebDriver driver = new FirefoxDriver();<br>WebElement element;<br>Actions actions = new Actions(driver).moveToElement(element).clickAndHold();</pre> | <pre>WebDriver driver = new FirefoxDriver();<br>WebElement element;<br>Actions actions = new Actions(driver).clickAndHold(element);</pre> |
| <pre>WebDriver driver = new FirefoxDriver();<br>WebElement element;<br>Actions actions = new Actions(driver).moveToElement(element).doubleClick();</pre> | <pre>WebDriver driver = new FirefoxDriver();<br>WebElement element;<br>Actions actions = new Actions(driver).doubleClick(element);</pre> |
| <pre>WebDriver driver = new FirefoxDriver();<br>WebElement element;<br>Actions actions = new Actions(driver).moveToElement(element).release();</pre> | <pre>WebDriver driver = new FirefoxDriver();<br>WebElement element;<br>Actions actions = new Actions(driver).release(element);</pre> |
| <pre>WebDriver driver = new FirefoxDriver();<br>WebElement element;<br>Actions actions = new Actions(driver).click(element).sendKeys("some keys");</pre> | <pre>WebDriver driver = new FirefoxDriver();<br>WebElement element;<br>Actions actions = new Actions(driver).sendKeys(element, "some keys");</pre> |
| <pre>WebDriver driver = new FirefoxDriver();<br>WebElement element;<br>Actions actions = new Actions(driver).click(element).build().perform();</pre> | <pre>WebDriver driver = new FirefoxDriver();<br>WebElement element;<br>Actions actions = new Actions(driver).click(element).perform();</pre> |

**Templates:**

**moveToElement(element).click() -> click(element)**

```xml
<replaceConfiguration name="Consecutive moveToElement(element) and click() calls can be simplified to click(element)." text="$actions$.moveToElement($element$).click()" recursive="false" caseInsensitive="true" type="JAVA" pattern_context="default" reformatAccordingToStyle="true" shortenFQN="true" replacement="$actions$.click($element$)">
    <constraint name="__context__" within="" contains="" />
    <constraint name="element" nameOfExprType="org\.openqa\.selenium\.WebElement" expressionTypes="org.openqa.selenium.WebElement" within="" contains="" />
    <constraint name="actions" nameOfExprType="org\.openqa\.selenium\.interactions\.Actions" expressionTypes="org.openqa.selenium.interactions.Actions" within="" contains="" />
</replaceConfiguration>
```

**moveToElement(element).clickAndHold() -> clickAndHold(element)**

```xml
<replaceConfiguration name="Consecutive moveToElement(element) and clickAndHold() calls can be simplified to clickAndHold(element)." text="$actions$.moveToElement($element$).clickAndHold()" recursive="false" caseInsensitive="true" type="JAVA" pattern_context="default" reformatAccordingToStyle="true" shortenFQN="true" replacement="$actions$.clickAndHold($element$)">
    <constraint name="__context__" within="" contains="" />
    <constraint name="element" nameOfExprType="org\.openqa\.selenium\.WebElement" expressionTypes="org.openqa.selenium.WebElement" within="" contains="" />
    <constraint name="actions" nameOfExprType="org\.openqa\.selenium\.interactions\.Actions" expressionTypes="org.openqa.selenium.interactions.Actions" within="" contains="" />
</replaceConfiguration>
```

**moveToElement(element).doubleClick() -> doubleClick(element)**

```xml
<replaceConfiguration name="Consecutive moveToElement(element) and doubleClick() calls can be simplified to doubleClick(element)." text="$actions$.moveToElement($element$).doubleClick()" recursive="false" caseInsensitive="true" type="JAVA" pattern_context="default" reformatAccordingToStyle="true" shortenFQN="true" replacement="$actions$.doubleClick($element$)">
    <constraint name="__context__" within="" contains="" />
    <constraint name="element" nameOfExprType="org\.openqa\.selenium\.WebElement" expressionTypes="org.openqa.selenium.WebElement" within="" contains="" />
    <constraint name="actions" nameOfExprType="org\.openqa\.selenium\.interactions\.Actions" expressionTypes="org.openqa.selenium.interactions.Actions" within="" contains="" />
</replaceConfiguration>
```

**moveToElement(element).release() -> release(element)**

```xml
<replaceConfiguration name="Consecutive moveToElement(element) and release() calls can be simplified to release(element)." text="$actions$.moveToElement($element$).release()" recursive="false" caseInsensitive="true" type="JAVA" pattern_context="default" reformatAccordingToStyle="true" shortenFQN="true" replacement="$actions$.release($element$)">
    <constraint name="__context__" within="" contains="" />
    <constraint name="element" nameOfExprType="org\.openqa\.selenium\.WebElement" expressionTypes="org.openqa.selenium.WebElement" within="" contains="" />
    <constraint name="actions" nameOfExprType="org\.openqa\.selenium\.interactions\.Actions" expressionTypes="org.openqa.selenium.interactions.Actions" within="" contains="" />
</replaceConfiguration>
```

**click(element).sendKeys("string") -> sendKeys(element, "string")**

```xml
<replaceConfiguration name="Consecutive click(element) and sendKeys(keys) calls can be simplified to sendKeys(element, keys)." text="$actions$.click($element$).sendKeys($keys$)" recursive="false" caseInsensitive="true" type="JAVA" pattern_context="default" reformatAccordingToStyle="true" shortenFQN="true" replacement="$actions$.sendKeys($element$, $keys$)">
    <constraint name="__context__" within="" contains="" />
    <constraint name="element" nameOfExprType="org\.openqa\.selenium\.WebElement" expressionTypes="org.openqa.selenium.WebElement" within="" contains="" />
    <constraint name="actions" nameOfExprType="org\.openqa\.selenium\.interactions\.Actions" expressionTypes="org.openqa.selenium.interactions.Actions" within="" contains="" />
    <constraint name="keys" within="" contains="" />
</replaceConfiguration>
```

**build().perform() -> perform()**

```xml
<replaceConfiguration name="Consecutive build() and perform() calls can be simplified to perform()." text="$actions$.build().perform()" recursive="false" caseInsensitive="true" type="JAVA" pattern_context="default" reformatAccordingToStyle="true" shortenFQN="true" replacement="$actions$.perform()">
    <constraint name="__context__" within="" contains="" />
    <constraint name="actions" nameOfExprType="org\.openqa\.selenium\.interactions\.Actions" expressionTypes="org.openqa.selenium.interactions.Actions" within="" contains="" />
</replaceConfiguration>
```
