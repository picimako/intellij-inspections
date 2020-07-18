# Fields of class with the same annotation values
 
There are occasions when you have multiple fields of a class annotated with the same annotation, but it is
incorrect or invalid to have more than one field having the same value set in that particular annotation.

## An example

One such example may be Selenium WebDriver's `@FindBy` annotation in which you can specify a locator strategy,
and the locator itself for related `WebElement`s. However, multiple `WebElement`s or list of `WebElement`s with the same locator strategy and locator
are incorrect, or may be misleading:

```java
public class APageObject {

  @FindBy(id = "header")
  private WebElement header;

  @FindBy(id = "header")
  private WebElement head;
}
```

Of course, in case of WebDriver it can still be valid if you have a `WebElement` and a separate `List<WebElement>` field
with the same locator strategy and value because the `WebElement` type field will store the first match for that locator,
meanwhile the `List<WebElement>` type field will store all matches for it:

```java
public class APageObject {

  @FindBy(className = "table-row")
  private WebElement firstRow;

  @FindBy(className = "table-row")
  private List<WebElement> tableRow;
}
```

This logic is specific to WebDriver. Other libraries and annotations (like Spring's `@Value` annotation) will probably work differently.

## Creating the template

Of course implementing a custom Groovy script in the Script filter is always a good option, but here I'm not going to go into the details of that, instead
I'm going to show you how this check may be achieved without the Script filter.

In the template text you'll need two fields of class (they might also be marked as instance fields using the `@Modifier("Instance")` annotation), e.g:

```java
class $Class$ {
    @$FindBy$(css = "$value$")
    $FieldType$ $field$ = $init$;

    @$FindBy$(css = "$value$")
    $FieldType2$ $field2$ = $init2$;
}
```

In this first approach, although we are looking for same-type fields, the template variables representing the field types may have different names but their text filters
must be configured with the same value/type.

This template might also be useful when you don't want to allow even different type objects for the same locator.

**Configuration:**
- `FieldType`, `FieldType2`: Text filter: org\\.openqa\\.selenium\\.WebElement
- `FindBy`: Text filter: org\\.openqa\\.selenium\\.support\\.FindBy
- `init`, `init2`: Count filter: 0,1

The other approach is almost identical to the previous one, but here the two field types are handled with the same template variable (the filter configuration remains the same):

```java
class $Class$ {
    @$FindBy$(css = "$value$")
    $FieldType$ $field$ = $init$;

    @$FindBy$(css = "$value$")
    $FieldType$ $field2$ = $init2$;
}
```

In this latter case, if there are more than 2 matches, using `$FieldType$` as the search target, only the first two matches will be highlighted.

## Additional resources

Related support and YouTrack tickets:
- [Find fields in class annotated with the same values (Structural Search ?)](https://intellij-support.jetbrains.com/hc/en-us/community/posts/360000018059-Find-fields-in-class-annotated-with-the-same-values-Structural-Search-)
- [Finding same values does not work](https://youtrack.jetbrains.com/issue/IDEA-195166)
