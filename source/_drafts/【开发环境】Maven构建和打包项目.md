---
title: 【开发环境】Maven构建和打包项目
cover_picture: images/cover/notes.jpg
author: Veni
categories: notes
tags:
  - maven
  - java
date: 2023-10-14 18:41:03
---

Remember to write a excerpt.<!--more-->

[TOC]

# 【开发环境】Maven构建和打包项目

## 什么是Maven

根据Maven官网的介绍，Apache Maven是一个软件项目管理和和构建工具，主要用于Java语言。Maven通过一个中央信息清单（Project Object Model，POM）来描述项目的结构、依赖关系和构建过程。也就是说，当你看到一个Java项目中有pom.xml这个文件的时候，这个项目大概率就是使用Maven构建的。

### pom.xml示例

一个典型的pom.xml如下：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>org.veni</groupId>
    <artifactId>JavaHomework1</artifactId>
    <version>1.0</version>

    <dependencies>
    </dependencies>

    <properties>
    </properties>
    
    <build>
    </build>

</project>
```

### pom.xml的各元素详解

下面将详细描述pom.xml的各元素的作用。

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>org.veni</groupId>
    <artifactId>JavaHomework1</artifactId>
    <version>1.0</version>
    
</project>
```

上面的配置是一个Maven项目的基本要素。首先是Pom的各项配置，包括模式文件，命名空间等等。

- `<?xml version="1.0" encoding="UTF-8"?>`: 这是XML声明，指定了XML文档的版本和编码方式。在这个示例中，版本为1.0，编码方式为UTF-8。

- `xmlns="http://maven.apache.org/POM/4.0.0"`: 这是命名空间声明，指定了XML元素的命名空间。它告诉解析器在解析元素时要使用的命名空间。
- `xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"`: 这是另一个命名空间声明，它为XML实例命名空间指定了一个前缀"xsi"。
- `xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd"`: 这个属性指定了模式的位置，用于验证XML文档的结构。在这个示例中，它告诉解析器使用"Maven POM模型的版本4.0.0"的模式文件（XSD）来验证XML文档。
- `<modelVersion>4.0.0</modelVersion>`: 这个元素指定了POM模型的版本。在这个示例中，模型版本为4.0.0，表示使用Maven 4的POM模型。

接下来是项目本身的坐标和表示设置：

- `<groupId>`: 这个元素表示项目的组织或团队的唯一标识符。在这个示例中，`org.veni`是项目的`groupId`，通常以反向域名的形式命名，用于确保唯一性。
- `<artifactId>`: 这个元素简单来说就是项目名（唯一标识符），用于区分不同的项目。在这个示例中，`JavaHomework1`是项目的`artifactId`，可以根据项目的实际情况进行命名。
- `<version>`: 这个元素指定了项目的版本号。

总之，上面的两部分文字定义了使用的POM版本，以及项目本身的属性和版本，是一个pom.xml的基本属性。此外，通过pom.xml可以完成依赖管理，项目打包等等功能，后面将会逐一介绍。

## Maven的使用

### 依赖管理

Maven的依赖管理通过`<dependencies>`属性管理该项目用到的所有依赖。



