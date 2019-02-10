## How to add project specific inspections to your Git project

Though it is nice to create inspections, it matters whether you create them globally, having them applied to all your projects used in IntelliJ, or on a per project basis. The latter can be useful for two reasons:
- your projects may differ in a way that not all your inspections are needed for all your projects,
- you can store the project specific inspections in the project's code repository, therefore anyone who clones the project has access to those inspections, and also they are automatically in place to be used.

In this article I would like to go through how you can add project specific inspections to your git projects.

## Project specific inspections
Inspections specific to an IntelliJ project are stored in the **.idea/inspectionProfiles/** folder probably under the file name **Project_Default.xml**, if you didn't rename it or didn't add a new one.

![profile](images/13a-How-to-add-project-specific-inspections-to-your-Git-project_Profile.PNG)

To have inspections in that file, you can either create ones via the editor, having the Profile set to Project Default, or copy their xml representations from different sources, directly into **Project_Default.xml**. Using the latter method, the
inspections appear automatically in the editor as well.

## Add to .gitignore
To make the inspections a part of your git project, you need to tweak .gitignore, so that **Project_Default.xml** becomes visible by git and you can commit and push it to the repository.

Ideally you already have the project **.iml** file and **.idea** folder in .gitignore:

```
*.iml
.idea/*
```

Additionally, you first need to include the **inspectionProfiles** folder itself

```
!/.idea/inspectionProfiles
```

then ignore all of its contents

```
/.idea/inspectionProfiles/*
```

and finally include only the files needed to be a part of the project, in this case only **Project_Default.xml**

```
!/.idea/inspectionProfiles/Project_Default.xml
```

This way you can include only the desired files, and ignore modifications in files in the same folder if someone does some in his/her local environment.
