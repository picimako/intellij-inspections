# IntelliJ inspections

This project is a collection of IntelliJ Structural Search and Replace Templates for various Java libraries,
and a storage place for the blog posts on the [IJnspector blog](https://ijnspector.wordpress.com).

These templates are created via IntelliJ's built-in creation dialogs, that are stored in `.xml` configuration.

You can find all the inspections created in the tutorials in [tutorial-inspections.md](tutorial/summary/tutorial-inspections.md).

For the whole collection of inspections you can browse through the **inspections** folder. Templates are organized by Java libraries.

## How to use them?

All you need to do is copy the desired `searchConfiguration`s and `replaceConfiguration`s into
* your project's IDEA config under `<Your project>\.idea\inspectionProfiles\Project_Default.xml`, or
* to the default IDEA global config under `<User home>\.IdeaIC2019.1\config\inspection\Default.xml`

To configure your git project for these inspections please read through the [How to add project specific inspections to your Git project](https://ijnspector.wordpress.com/2018/11/21/how-to-add-project-specific-inspections-to-your-git-project/) article.

## Contribution and support

If you find any issues in the articles, tutorials or in any of the inspections, please create a GitHub issue in this project.
