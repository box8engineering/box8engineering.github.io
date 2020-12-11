---
layout: post
title:  "Publish and Manage Android Libraries using Amazon S3 Buckets"
date:   2020-12-11 23:22:43 +0530
categories: jekyll update
author: "Shivansh Prakash"
permalink: shivansh-prakash/Publish-and-Manage-Android-Libraries-using-Amazon-S3-Buckets/
description: Using s3 buckets to manage your internal android library applications
imageUrl: assets/image/shivansh_s3_blog_image.png
---
<p align="center">
  <img src= "/assets/image/shivansh_s3_blog_image.png" alt="Image1"/>
</p>

Recently I was working on some library modules which are shared across multiple projects and faced some difficulties when updating the modules across various projects. Every time I made an update to a module, developers across various teams had to separately download the updated aar files and place them in their own repositories/branch to work with them as there was no repository/library publication system in-place. Even after that there was the issue of merge-conflicts between old and new aar files. Sometimes gradle cache also interferes with updating changes in android studio upon aar file update.

To resolve this issue I looked into some package repositories. I found that an S3 bucket can be used to publish and manage your library projects and unlike existing publishing platforms, like jitpack, for a private repository it will not cost any money. Moreover using S3 bucket can give more control over your libraries in terms of access and maintenance.

You first need to set up your S3 bucket that you will be using as a package repository. Create a new IAM user or update existing user policies for access to read/write files from your bucket.

You can use maven-publish plugin for gradle for uploading your artifacts. We define the artifacts of the projects as the files the project provides to the outside world. The Maven Publish Plugin provides the ability to publish build artifacts to a Maven repository. A maven repository uses a declarative file called pom.xml to control dependency management, site publication, and distribution publication. A Project Object Model or POM is an XML file that contains information about the project and configuration details. An example of a POM file with the minimum required element -
```sh
   <project>
    <modelVersion>4.0.0</modelVersion>
    <groupId>com.mycompany.app</groupId>
    <artifactId>my-app</artifactId>
    <version>1</version>
  </project>
```

#### Let us Begin (Pic or Gif)
- Add the code given in the following steps in your library's build.gradle file-
    1. ```sh 
       apply plugin: 'maven-publish' 
        ```
    2. Create publishing task
        ```sh
      publishing {
          repositories {
              maven {
                  url "s3://your-bucket-name.s3.amazonaws.com"
                  credentials(AwsCredentials) {
                      accessKey <myAccessKey>
                      secretKey <mySecretKey>
                  }
              }
          }
      }
      ```
      This will create a gradle task named publishing for your module. Update the url with the name of the S3 bucket you are using and the credentials of your IAM user to access the bucket.
    
    3. Define the groupId for your artifact. A groupId is a unique identifier of your project and should follow Java’s package name rules (eg. org.apache.maven, com.box8.libraryapplication, etc).
       ```sh
        group = 'com.mycompany.app'
        ```
    
    4.  Add the version of module
      ``` sh
      version = <version>
      ```
    
    5. Create a gradle task which outputs source artifacts of your module
        ```sh
        task sourceJar(type: Jar) {
            from android.sourceSets.main.java.srcDirs
            classifier "source"
        }
        ```
        
    6.  Loop over all variants available over build variants. The following code will generate pom file and publish the build for only release variant.
        ```sh
      android.libraryVariants.all { variant ->
          if (variant.buildType.name == "release") {
              variant.outputs.all { output ->
                  // This creates a publication for each variant
                  publishing.publications.create(variant.name, MavenPublication) {
                      
                      // The sources artifact generated from the gradle task sourceJar
                      artifact sourceJar
    
                      // You can use classifier field for variant dependent artifact, e.g. release, debug
                      artifact source: output.outputFile, classifier: null
    
                      // Go through all the dependencies for each variant and add them to the POM file as dependencies
                      pom.withXml {
                          def dependencies = asNode().appendNode('dependencies')
                          configurations.getByName(variant.name + "CompileClasspath").allDependencies
                                  .findAll { it instanceof ExternalDependency }
                                  .each {
                                      def dependency = dependencies.appendNode('dependency')
                                      dependency.appendNode('groupId', it.group)
                                      dependency.appendNode('artifactId', it.name)
                                      dependency.appendNode('version', it.version)
                                  }
                      }
                  }
              }
          }
      }
        ```
      You can further customise the generated pom file for artifacts by adding license information, developers information, etc.


- If your project contains multiple modules that is to be published you can include a file xyz.gradle (given below) in each of their build.gradle file by using -
    ```sh
  apply from: getRootDir().getAbsolutePath()+'/prePublishTask.gradle'
  ```
  Considering you have kept the file xyz.gradle in the root directory of your project.
    ### xyz.gradle
    ```sh
        apply plugin: 'maven-publish'
        group = 'com.mycompany.app'
        
        task sourceJar(type: Jar) {
           from android.sourceSets.main.java.srcDirs
           classifier "source"
        }
        
        android.libraryVariants.all { variant ->
           if (variant.buildType.name == "release") {
               variant.outputs.all { output ->
                   publishing.publications.create(variant.name, MavenPublication) {
                       artifact sourceJar
                       artifact source: output.outputFile, classifier: null
                       pom.withXml {
                           def dependencies = asNode().appendNode('dependencies')
                           configurations.getByName(variant.name + "CompileClasspath").allDependencies
                                   .findAll { it instanceof ExternalDependency }
                                   .each {
                                       def dependency = dependencies.appendNode('dependency')
        
                                       dependency.appendNode('groupId', it.group)
                                       dependency.appendNode('artifactId', it.name)
                                       dependency.appendNode('version', it.version)
                                   }
                       }
                   }
               }
           }
        }
        
        tasks.all { task ->
           if (task instanceof AbstractPublishToMaven) {
               task.dependsOn assemble
           }
        }
        
        publishing {
           repositories {
               maven {
                   url "s3://<your-bucket-name>.s3.amazonaws.com"
                   credentials(AwsCredentials) {
                       accessKey <myAccessKey>
                       secretKey <mySecretKey>
                   }
               }
           }
        }
    ```

- To start the publish task -
    1. Open the gradle tab (from the top-right side of Android Studio window).
    2. Select the module you want to publish. From the tasks section, expand the publishing section and select publish.


- Once the above task is finished successfully you can see folders and files related to your published library in S3 bucket.


- You can use your published libraries in Android projects by adding the following in your project level's build.gradle file in repositories of all allprojects.
    ```sh
    allprojects {
        repositories {
            ...
            maven {
                url "s3://<your-bucket-name>.s3.amazonaws.com"
                credentials(AwsCredentials) {
                    accessKey <readAccessKey>
                    secretKey <readSecretKey>
                }
            }
            ...
        }
    }

    ```
    
    Then from you app's build.gradle file you can add the dependency as follows -
    ```sh
    implementation '<groupId>:<artifactId>:<version>'
    ```
