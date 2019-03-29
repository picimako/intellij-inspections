# IntelliJ inspections

This project is a collection of IntelliJ Structural Search and Replace Templates for various Java libraries, and a storage place for the blog posts on https://ijnspector.wordpress.com.

These templates are created via IntelliJ's built-in creation dialogs, that are stored in .xml config.

You can find all the inspections created in the tutorials in the [tutorial-inspections.md](tutorial/summary/tutorial-inspections.md).

For the whole collection of inspections you can browse through the **inspections** folder, templates are organized by Java libraries.

## How to add them?

All you need to do is to copy the desired `searchConfiguration`s and `replaceConfiguration`s into
* your project's IDEA config under `<Your project>\.idea\inspectionProfiles\Project_Default.xml`, or
* to the default IDEA config under `<User home>\.IdeaIC2017.3\config\inspection\Default.xml`

     FluentLenium
        Page annotált class-ban olyan FindBy field van hasznalva, ami nem WebElement, vagy nem abbol szarmazik. Ez igaz a listákban levo object tipusokra is.
        ??? Page annotált class-ban nincs semmilyen webelement.
        await() hivasnal a timeout ertekehez lehet egy max erteket rendelni.
        await hivasanal van egy default timeout. Ha az van explicit megsdva, akkor lehetne jelezni.
    Java MessageBuilder
        MessageBuilder chained append() calls have better performance than multiple individual calls to append().

---------------------------------------------------------
IDEA inspection plugin how-to: https://stackoverflow.com/questions/26463322/intellij-idea-how-to-create-a-custom-inspection-rule#42598803
---------------------------------------------------------
Gherkin plugin inspections:
    https://github.com/JetBrains/intellij-plugins/tree/master/cucumber
    https://github.com/JetBrains/intellij-plugins/commit/b316b11455dfbc98f4dc30ba42a6df58db1b94ba
---------------------------------------------------------
TODO
It occurred to me that although the target of the search influences how and to what the quick fix is applied to in case of Replace templates,
it might also affect how the Minimum and Maximum counts of certain variables need to be configured. The counts may change depending on what is the target of the search.
However I need to do some experimenting with this.

# Ami van eddig
Class name text
Class name + annotation
Class name + no annotation
Class with fields + no annotation
Class extension
Method call (replace)
Static method call without static import
Static method call in assignment
Static method call in statement
Method parameter count is too many
Method parameter not necessary
Consecutive method calls (to be simplified)
No action method is called (AssertJ assert)
Field is not annotated as something
Field should be replaced (with annotation)
Field is not initialized
Static field is annotated
Annotation attribute value is invalid
Annotation attribute value is unnecessary

# Ami kellene még
Interface extension
Generic class types
?? Instance method call in assignment
?? Instance method call in statement
Field is annotated as something
Field is initialized
Static field is not annotated
?? An annotation attribute should not be used

### TODO:
Groovy scripts: how to?
Text constraint containing symbol references: .*$annotation$.*$
Reference target constraints
Expression constraints
Script constraints
@Modifier annotations
Comment inspections
If statement
try-catch block
Finding all descendants of a class or all classes that implement a certain interface
Finding all such methods
Using @Modifier for finding package local and instance methods
Using 'Contained in Constraints' field in a search
