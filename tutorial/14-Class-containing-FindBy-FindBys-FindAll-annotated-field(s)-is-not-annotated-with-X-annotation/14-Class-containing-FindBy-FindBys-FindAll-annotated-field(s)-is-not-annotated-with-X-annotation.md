# Class containing FindBy/FindBys/FindAll annotated field(s) is not annotated with page object annotation
In the next couple of posts I'm going to dive into some Selenium specific test automation framework related templates, which are going to be a bit more complex than the previous examples, so expect them to be a little longer.

Let's say you design a test automation framework in which you are going to use Selenium and leverage the benefits of the Page Object pattern.

You design the framework in a way that whenever it finds a class with an annotation called `@Page` it handles it as a page object and initializes the `WebElement`s in it annotated
as `@FindBy`, `@FindBys` or `@FindAll`.

An example of that class might look like:

```java
@Page
public class Homepage {

    @FindBy(css = "#image")
    public WebElement image;

    @FindBys({
        @FindBy(className = "menu"),
        @FindBy(className = "element")
    })
    public List<WebElement> menuElements;

    @FindAll({
        @FindBy(css = ".footer"),
        @FindBy(css = ".element")
    })
    public List<WebElement> footerElements;
}
```

## Template creation
In this template I combined three predefined templates:

- *annotated classes*
    ```java
    @$Annotation$( )
    class $Class$ {}
    ```
- *instance fields of the class*
    ```java
    class $Class$ {
        @Modifier("Instance") $FieldType$ $Field$ = $Init$;
    }
    ```
    > *Using the `@Modifier` annotation you can target package local ("packageLocal") and instance ("Instance") fields and methods
    You can find a brief description about that [here](https://www.jetbrains.com/help/idea/structural-search-and-replace-examples.html).* 
- *variables of generic types*
    ```java
    $Type$ <$GenericArgument$>  $Var$ = $Init$;
    ```

First I took *instance fields of the class*, and added the class level annotation:

```java
@$PageAnnotation$( )
class $Class$ {
    @Modifier("Instance") $FieldType$ $Field$ = $Init$;
}
```

Then I also added an annotation to the field, since we are going to search for Selenium's `@Find...` annotations:

```java
@$PageAnnotation$( )
class $Class$ {
    @$FindAnnotation$( )
    @Modifier("Instance") $FieldType$ $Field$ = $Init$;
}
```

Finally, I removed the `$Init$` variable because initialization is done by Selenium,
and to be able to target not only `WebElement` type fields, but `List<WebElement>`s as well, I added the `<$GenericArgument$>` section:

```java
@$PageAnnotation$( )
class $Class$ {
    @$FindAnnotation$( )
    @Modifier("Instance") $FieldType$ <$GenericArgument$> $Field$;
}
```

![editor](images/14-Class-containing-FindBy-FindBys-FindAll-annotated-field(s)-is-not-annotated-with-X-annotation_Editor.PNG)

## PageAnnotation variable
This is one of the crucial parts of this template. In this we need to configure that the `@Page` annotation is missing.

To do that, first we need to specify the annotation itself in a Text filter which will be (using an arbitrary package):

```
picimako\.tutorial\.annotation\.Page
```

Then we need to add a Count filter with the minimum and maximum counts set to 0-0, so that it will be handled as missing.

![pageannotation](images/14-Class-containing-FindBy-FindBys-FindAll-annotated-field(s)-is-not-annotated-with-X-annotation_PageAnnotation.PNG)

## Class variable
Since the class name is not important in this template, I left almost everything at its default state.
The only thing I've changed is that I set it as the Search target, so this being a search template, it won't affect any quick fix,
but only which part of the code is highlighted.

## FindAnnotation variable
This is another important part of the template. Here we need to tell IntelliJ to look for any of the `@Find...` annotations provided by Selenium, that can be:
- @FindBy
- @FindBys
- @FindAll

Based on this list add a Text filter as following:

```
org\.openqa\.selenium\.support\.(FindBy|FindBys|FindAll)
```

![findannotation](images/14-Class-containing-FindBy-FindBys-FindAll-annotated-field(s)-is-not-annotated-with-X-annotation_FindAnnotation.PNG)

## FieldType and GenericArgument variables
Since by default a `@Find...` annotated field can be `WebElement` or `List<WebElement>` as well, both cases need to be handled somehow.
I couldn't manage to define them in only the `$FieldType$` variable, so I added the generic type separately and I handle the two together.

So the `$FieldType$` can be a `WebElement` or a `List`, thus its Text filter will be:
```
org\.openqa\.selenium\.WebElement|java\.util\.List
```

In case of the `$GenericArgument$` variable the value will be

```
org\.openqa\.selenium\.WebElement
```

to be able to validate `List<WebElement>` type fields.

One additional configuration is to set a Count filter with 0-1, making the `$GenericArgument$` variable optional. This is in order to be able to handle `WebElement` type fields,
which doesn't have a generic type.

![fieldtype](images/14-Class-containing-FindBy-FindBys-FindAll-annotated-field(s)-is-not-annotated-with-X-annotation_FieldType.PNG)

![genericargument](images/14-Class-containing-FindBy-FindBys-FindAll-annotated-field(s)-is-not-annotated-with-X-annotation_GenericArgument.PNG)

## Field variable
The field name is also not important, you can leave everything at its default state here too.

## Finalization
Of course if you have custom wrapper type(s) for `WebElement`, by specifying those types in the variables above you can align the template to your wrappers.

The code highlight in the IDE will look like as below:

![highlight](images/14-Class-containing-FindBy-FindBys-FindAll-annotated-field(s)-is-not-annotated-with-X-annotation_Highlight.PNG)

Below you can find the XML representation of the template created, so that you can easily copy and paste it into your template collection.

```xml
<searchConfiguration name="Class containing FindBy/FindBys/FindAll annotated field(s) is not annotated as Page." text="@$PageAnnotation$( )&#10;class $Class$ {&#10;    @$FindAnnotation$( )&#10;    @Modifier(&quot;Instance&quot;) $FieldType$ &lt;$GenericArgument$&gt; $Field$;&#10;}" recursive="false" caseInsensitive="true" type="JAVA">
    <constraint name="PageAnnotation" regexp="picimako\.tutorial\.annotation\.Page" minCount="0" maxCount="0" within="" contains="" />
    <constraint name="Class" target="true" within="" contains="" />
    <constraint name="FindAnnotation" regexp="org\.openqa\.selenium\.support\.(FindBy|FindBys|FindAll)" within="" contains="" />
    <constraint name="FieldType" regexp="org\.openqa\.selenium\.WebElement|java\.util\.List" within="" contains="" />
    <constraint name="Field" within="" contains="" />
    <constraint name="__context__" within="" contains="" />
    <constraint name="GenericArgument" regexp="org\.openqa\.selenium\.WebElement" minCount="0" within="" contains="" />
</searchConfiguration>
```
