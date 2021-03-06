# Installing and using Nexus 3.6.x for Windows

# Table of Contents

## Getting Started
- [Installing Nexus](#installing-nexus)
- [Installing Maven](#installing-maven)
- [Logging into Nexus](#logging-into-nexus)

## Configuration
- [Proxying Maven Central](#proxying-maven-central)
- [Encrypting Password in Settings.xml](#encrypt-password)
- [Change Default Accounts](#change-default-accounts)
- [Add Maven Repo Group](#add-maven-repo-group)
- [SSL Setup](#ssl-setup)
- [Active Directory (LDAP) Setup](#ad-setup)

## Developing
- [IDE Integration](#ide-integration)
- [Deploying to Nexus](#deploying-to-nexus)
- [WebHooks](#webhooks)

## [Helpful Links](#useful-links)

# Getting Started

### Installing Nexus
   IF AND ONLY IF NEXUS IS NOT CURRENTLY INSTALLED ANYWHERE, AND YOU WANT TO GET AN INSTANCE UP AND RUNNING, FOLLOW THESE STEPS:
  - Download Nexus OSS from the Sonatype website: https://www.sonatype.com/download-oss-sonatype

  - Unzip the file to either Program Files or Program Files(x86) directory on Windows. (This is where it will run from)

  - Open up a Command Prompt *AS AN ADMINISTRATOR* and cd to the unzipped folder.

  - when you're there, run this command: `nexus.exe /install <whateveryouwanttheservicetobecalled>`

      - This installs the nexus server as a service in windows, so it can be managed by the services utility. (Super useful)

 - Nexus defaults to running on port `8081`. 

      - If that port is currently in use, you can stop the service and go into the `sonatype-work/nexus3/etc/nexus.properties` file and change the `application-port` value to whatever you want (above 1024)

  - Congratulations. Nexus is installed and running as a service on Windows.


### Installing Maven
- If you have Netbeans (or any IDE, really) installed, chances are you already have it, though it is an older version than current. 
This means there's no telling if that version is supported or not. Because of that, I recommend finding the newest version of maven currently: `<$maven-version$>`.

- Once you have Maven downloaded and the bin folder on your path, you're ready to get to the next step! [ROTFO]


### Logging into Nexus
So, now you have maven and you know where nexus is located and running at. Now you need a user account ON nexus to access it!
Contact <$nexusadmin$> so that they can give you a nexus account with the proper permissions. If you're the one setting Nexus up, the default admin account \ password is: `admin` \ `admin123`. We'll go over how to change these later, as it's *exremely* insecure to keep it like this.

# Configuration

### Proxying Maven Central
- Out of the box, Nexus comes with pretty much everything we need for development. When you click on the Components Section on the left gutter, you should see all of the repositories offered by Nexus out of the box. We're going to focus on the ones with Maven in the name. 

- Clicking on maven-public shows that this repository is going out to the repo1.maven.org/maven2 url. That means, whenever a program you use needs something from a 3rd party library, maven will query the intra-Nexus server first, and if the library isn't found, it will go to the Central Maven repo and try to find it. If the library is found, it will be downloaded and cached in the intra-Nexus server, and then served to your program. This reduces the strain and bandwidth costs on the central nexus repo as well as our own intranet.

- To use the intranet hosted Nexus server instead of going to Maven Central, you need to change two things: 
    1. User settings for the maven tool
    2. The pom.xml for the project you're working on
    
1. First, we'll change the maven settings.
    - Go to wherever you unzipped your maven installation, and cd into the conf folder. 
    - You should see a settings.xml file there. Copy that settings.xml into the .m2 directory, which resides in your user-home directory (generally C:\User\{username}\.m2\{here})
    - Now, you'll want to add a <servers> attribute, with a sub-attribute specifying three <server>'s (make sure you close the tags!)
    - In that <server> add the following 3 attributes and fill them out
```xml
   <servers>
      <server>
         <id> name of the maven component </id>              (make one for maven-releases, maven-snapshots, and repository group)
         <username> your assigned username </username>       (this and the pwd will be the same for each <id> tag)
         <password> your assigned password </username>
      </server>
   </servers>
```
   - You will also need to add a `mirror` tag to tell nexus to mirror the central repository, it should look something like this: 
```xml
<mirrors>
   <mirror>
   <!--This sends everything else to /public-->
      <id>central</id>
      <name>central</name>
      <url>http://dogsarecute:1234/repository/repogroup/</url>
      <mirrorOf>*</mirrorOf>
   </mirror>
</mirrors>
```
    
2. Now we have to change the pom of your project.
    - There are 2 specific attributes you'll most likely need to add to your project pom
       - `<repositories>`
       - `<distributionManagement>`
    - For the `repositories` attribute, add a `repository` tag, and then inside that add an id and url tag that correlate to your repository that you set up. See an example below: 

```xml
<repositories>
   <repository>
      <id>repogroup</id>
      <url>http://dogsarecute:1234/repository/repogroup/</url>
   </repository>
</repositories>
```

   - For the `distributionManagement` tag, you'll need to add 2 sub tags, `snapshot repository` and `repository`.
      - `snapshot repository` defines the repository where snapshots will be stored.
      - `repository` defines where releases for libraries will be stored.
      - See example below: 
```xml
<distributionManagement>
   <snapshotRepository>
      <id>maven-snapshots</id>
         <url>http://dogsarecute:1234/repository/maven-snapshots/</url>
   </snapshotRepository>
   <repository>
      <id>maven-releases</id>
      <url>http://dogsarecute:1234/repository/maven-releases/</url>
   </repository>
</distributionManagement>
```

## Change Default Accounts
   - You're going to want to set up an admin account with *actually* secure credentials once you get nexus running. 

# Deploying To Nexus            

# Useful Links
