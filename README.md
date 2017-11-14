# [Table of Contents]

## Getting Started
- [Installing Nexus](#installing-nexus)
- [Installing Maven](#installing-maven)
- [Logging into Nexus](logging-into-nexus)

Before you start reading how to use or configure Nexus, please read the section: [Getting Started](#Getting-Started)
If you're doing any configuration on the Nexus server itself, please see section 1 titled: [Configuration](#Configuration)
If you want to learn how to use nexus from a development standpoint, please see section 2: [Development](#Development)
Finally, if you still have questions, there is a [Helpful Links](#helpful-links) section that will act as a library for all the useful links that I found when doing my research


# Getting Started

### Installing Nexus
   IF AND ONLY IF NEXUS IS NOT CURRENTLY INSTALLED ANYWHERE, AND YOU WANT TO GET AN INSTANCE UP AND RUNNING, FOLLOW THESE STEPS:
        - Download Nexus OSS from the Sonatype website: https://www.sonatype.com/download-oss-sonatype
        - Unzip the file to either Program Files or Program Files(x86) directory on windows. (This is where it will run from)
        - Open up a Command Prompt AS AN ADMINISTRATOR and cd to the unzipped folder.
        - when you're there, run this command: nexus.exe /install <whateveryouwanttheservicetobecalled>
            - This installs the nexus server as a service in windows, so it can be managed by the services utility. (Super useful)
        - Nexus defaults to running on port 8081. 
            - If that port is currently in use, you can stop the service and go into the sonatype-work/nexus3/etc/nexus.properties file and change application-port
        - Congratulations. Nexus is installed and running


### Installing Maven
If you have Netbeans installed, chances are you already have it, though it is a much older version than current. 
This also means there's no telling if that version is supported or not. Because of that, I recommend finding the newest version of maven currently: <$maven-version$>.
Once you have Maven downloaded and the bin folder on your path, you're ready to get to the next step!


### Logging in to Nexus
So, now you have maven and you know where nexus is located and running at. Now you need a user account ON nexus to access it!
Contact <$nexusadmin$> so that they can give you a nexus account with the proper permissions.


# Development

### Proxying Maven Central
Out of the box, Nexus comes with pretty much everything we need for development. When you click on the Components Section on the left gutter, you should see all of the repositories offered by Nexus out of the box. We're going to focus on the ones with Maven in the name. 

Clicking on maven-public shows that this repository is going out to the repo1.maven.org/maven2 url. That means, whenever a program you use needs something from a 3rd party library, maven will query the intra-Nexus server first, and if the library isn't found, then it will go to the Central Maven repo and try to find it. If the library is found, it will be downloaded and cached in the intra-Nexus server, and then served to your program. This reduces the strain and bandwidth costs on the central nexus repo as well as our own intranet.

To use the intranet hosted Nexus server instead of going to Maven Central, you need to change two things: 
    Your pom.xml for the project you're working on and user settings for the maven tool.

First, we'll change the maven settings.
    Go to wherever you unzipped your maven installation, and cd into the conf folder. 
    You should see a settings.xml file there. Copy that settings.xml into the .m2 directory, which resides in your user-home directory usually. 
        (Generally C:\User\{username}\.m2\{here})
    Now, you'll want to add a <servers> attribute, with a sub-attribute specifying three <server>'s (make sure you close the tags!)
    In that <server> add the following 3 attributes and fill them out
        <id> name of the maven component </id>              (make one for maven-releases, maven-snapshots, and WsfsBuild)
        <username> your assigned username </username>       (this and the pwd will be the same for each <id> tag)
        <password> your assigned password </username>

Now we have to change the pom of your project.
            
    
