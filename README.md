# IntelliJ inspections

This project is a collection of IntelliJ Structural Search and Replace Templates for various Java libraries, and a storage place for the blog posts on https://ijnspector.wordpress.com.

These templates are created via IntelliJ's built-in creation dialogs, that are stored in .xml config.

You can find all the inspections created in the tutorials in the [tutorial-inspections.md](tutorial/summary/tutorial-inspections.md).

For the whole collection of inspections you can browse through the **inspections** folder, templates are organized by Java libraries.

## How to add them?

All you need to do is to copy the desired `searchConfiguration`s and `replaceConfiguration`s into
* your project's IDEA config under `<Your project>\.idea\inspectionProfiles\Project_Default.xml`, or
* to the default IDEA config under `<User home>\.IdeaIC2017.3\config\inspection\Default.xml`
