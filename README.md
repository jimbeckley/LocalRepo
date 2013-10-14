# Appian Configuration Manager #
## Purpose ##
The Appian Configuration Manager represents a standard deployment process for Appian platform configurations and customizations. It is a standardized tool that prepares, packages and deploys configuration files, plugins and customizations from a standard structure into an Appian instance.The Configuration Manager provides these main benefits: 

- Provides a standard way to manage configuration, plugins and additional customizations
- Eliminates the possibility of human error when merging/renaming files
- Speeds up the deployment process
- Enforces agreed upon deployment best practices and standardized techniques
- Allows for source control of environment configurations and customizations
- Works across operating systems and environments

## Requirements ##
- Appian 7.1+ 
- Apache Ant 1.8.x+ ([download](http://ant.apache.org/))

## Technology ##
### Scripting Language  ###
The Appian Configuration Manager is implemented using the [Apache Ant](http://ant.apache.org/)™ scripting language. The minimum Ant version that is required is 1.8.x. Additional libraries used by the Appian Configuration Manager are embedded in the package to ensure that all features work properly for all project implementations.

### Version Control System ###
In order to reap the full benefits of the Configuration Manager, a VCS is highly recommended for all project implementations. The tool can be readily committed and managed in a version controlled repository (see details below).

## Initial Setup ##
In order to use the Manager, you need to download it from Appian Forum and then extract it into your project’s version controlled repository:

1. Navigate to the Appian Software application of Appian Forum
2. Locate the Add-Ons section.
3. Click the appropriate Appian Configuration Manager link to download the archive file. NOTE: updated versions of the Appian Configuration Manager will be released periodically. Check back often!
4. Extract the archive file into your version controlled repository and commit.

## Recent Changes ## 
7.3 Initial Release - 2013-10-07

- initial release for Appian 7.3

5.5 -7.2 PS Build Releases

- The Manager is a tool that has enjoyed many releases and years of support as PS Build.

## Features ##
The Appian Configuration Manager contains script that will:

- Install and configure JBoss EAP 6.1 for use with Appian (JBoss installer file needs to be provided separately)
- Deploy custom.properties file with environment specific values
- Deploy Spring Security configuration files
- Compile and deploy plugins

## Process Overview ##
The Appian Configuration Manager prepares a new enterprise application deployment package based on the base product files and the custom configurations:

HERE GOES ACMIMAGE1

Diagram Steps:

1. Creates a new folder runtime_ear in the Appian install folder
2. Copies contents of the folder <appian>/ear into the folder runtime_ear
	1. Prepare and package the contents of the Manager’s folders
3. Copies the packaged contents in the folder runtime_ear

The benefits of this procedure is that the base web application files are never modified.

The application server must be reconfigured to use the files located into the folder runtime_ear and this is done automatically by the Manager when using JBoss application server.

The Manager should be executed on the server running Appian. If using a dedicated build machine, additional steps are necessary, for example changing the location of ear in application server configuration, or deploying data source configuration files.

## How to use the Manager ##
### Prerequisites ###
#### Configure ANT_HOME ####
1. Create environment variable ANT_HOME and point to the extracted Ant directory
2. Add 
	1. [Windows] %ANT_HOME%\bin to the Path variable
	2. [Unix based OS] $ANT_HOME/bin to the PATH variable

#### Configure environment specific files ####

1. Use <project.home>/build/*local.properties.example* file as a guiding tool to create a properties file specific to your environment.
	1. Four environments are available for selection by default - local, dev, staging and prod (for example, dev.properties needs to be created for the Development environment)
2. Fill out the properties the same way you would be filling out custom.properties according to [https://forum.appian.com/suite/wiki/latest/Post-Install_Configurations](https://forum.appian.com/suite/wiki/latest/Post-Install_Configurations).
	1. NOTE: Any additional files that are required for your install need to be added to the Manager’s folder structure to be correctly deployed (for example Spring Security configuration, incoming email setup, etc.). Any environment specific values can be tokenized (@my.tokenized.property@) and the value set in your [environment].properties file.
	2. NOTE:  Any additional properties not covered under the example file can be added to your [environment].properties file. Corresponding keys need to be added to custom.properties so that they get deployed correctly to /runtime_ear
	3. NOTE: The Manager does not generate encrypted data source passwords. This step needs to be completed manually and values provided in [environment].properties file.
	4. NOTE: The Manager does not deploy or configure cleanup scripts. These need to be configured according to documentation ([https://docswiki.appian.com/suite/wiki/73/Required_Configurations#Required_Configurations](https://docswiki.appian.com/suite/wiki/73/Required_Configurations#Required_Configurations))
	5. NOTE: The Manager does not generate a unique security token ([https://docswiki.appian.com/suite/wiki/73/Appian_Engine_Connection_Restrictions#Generating_a_Custom_Security_Token](https://docswiki.appian.com/suite/wiki/73/Appian_Engine_Connection_Restrictions#Generating_a_Custom_Security_Token)). This file should exist outside of the Manager folder structure and needs to be manually created.
3. If you are installing JBoss EAP, provide the path to the JBoss install archive and the installation path as well as the credentials for management user
4. [Custom Topology] Create a topology file for your environment in <project.home>/ear/suite.ear/conf (for example appian-topology.xml.dev)
5. Make sure to define properties for your data sources in [environment].properties files
### Executing the Manager ###

1. On Windows, open Command Prompt.
2. Navigate to the your <project.home>
3. Execute the ant command.

HERE GOES ACMIMAGE2

4. Choose your environment.

HERE GOES ACMIMAGE3

5. Choose appropriate menu action (see section below for detailed description of each action). Depending on the speed of your system and the action chosen, the execution can take a few minutes.

HERE GOES ACMIMAGE4

6. If a problem with the deployment was detected the build will show an error message

HERE GOES ACMIMAGE5

## Menu Actions ##
The Standard Build Process packs seven actions ready to be used. The objective is to ensure a safe and repeatable deployment from a shared development environment to a final stage production setting. Some of these actions include doing a full deployment or just deploy the latest modifications.


The Standard Build Process is environment aware, so the code deployed is exactly the same across all environments. Differences between the environments are established through environment specific properties files (local.properties, staging.properties, etc.).

### 0) Install JBoss and Deploy all ###
This option needs to be used first when using JBoss as application server. This action performs the following tasks:

1. Unzips the provided JBoss application server archive in the specified location.
2. Creates JBoss management user with the credentials provided.
3. Executes “Deploy all” action (see below).

### 1) Deploy all  ###
This option is used most frequently as it performs a complete cleanup and deployment
Deploy all performs the following tasks:

1. Deletes the folder runtime_ear and all its contents.
2. Clears application server cache.
3. Creates a new runtime_ear folder.
4. [JBoss] Deploys basic JBoss configuration files. See [https://docswiki.appian.com/suite/wiki/latest/Configuring_JBoss](https://docswiki.appian.com/suite/wiki/latest/Configuring_JBoss) on deployed files.
5. [JBoss] Modifies JBoss configuration to run the Appian application from the folder /runtime_ear instead of base product /ear.
6. Copies the content from /ear to /runtime_ear.
7. Copies and filters all the customized files with the parameterized values from each environment from <project.home>/ear to /runtime\_ear, <project.home>/\_admin to <appian.home>/\_admin, <project.home>/server <appian.home>/server and [JBoss] <project.home>/<project.appserver.home> to <appserver.home>.
8. Creates a jar from all custom java files and copies it to <appian.home>/lib.
9. Copies plugins from <project.home>/\_admin/plugins to <appian.home>/\_admin/plugins.
10. Copies the static content from <project.webapp> to <appserver.webapp>.
### 2) Deploy modifications ###
This menu action provides a faster execution time as it skips steps 1-5 of “Deploy all”. This is used when a minor configuration change needs to be deployed to the server. 
NOTES: 

1. Deploy all needs to be executed before Deploy modifications can be run.
2. Since this option does not delete any files from <appian.home> any files removed from configuration in <project.home> will not be removed from your deployment.

### 3) Deploy engines configuration files only ###
This option is used in distributed environments only on the server running Appian engines only.
Tasks performed:

1. Copies and filters three customized files used by Appian engines
	- <appian.home>/ear/suite.ear/conf/custom.properties
	- <appian.home>/ear/suite.ear/conf/appian-topology.xml
	- <appian.home>/server/\_conf/gateway-config-\*.xml
	- [Windows] <appian.home>/server/\_conf/partitions-\*.xml
	- [Windows] <appian.home>/server/\_conf/forums-config-\*.xml
	- NOTE: Since there are no application server assets on the engine server, the files are copied into <appian.home>/ear

### 4) Deploy all (static) ### 
This target is intended to deploy static web server assets such as images, html and css files into the folder <appian.static.webapp>. This folder should have been configured to be the root directory of the Appian application on the web server.
### 5) Deploy modifications (static) ### 
Similar action than Deploy All (Static) but only updates the target files based on timestamp.
### 6) Deploy plugins ###
If you have custom plugin projects within <project.home>/plugin_projects, this option will:

1. Clean <appian.home>/\_admin/plugins directory
2. Compile and jar plugins defined in <project.home>/plugin_projects
3. Copy new jars to <appian.home>/\_admin/plugins
4. Copy existing plugin jars from <project.home>/\_admin/plugins to <appian.home>/\_admin/plugins

## Extending the Build Process ##
### Adding Environments  ###
One of the most important features of the Standard Build Process is its environment aware platform. Configuring and adding environments to the build process ensures that the risk associated with introducing changes between environments is minimized.
To be able to add a new environment, there are three simple steps to follow.

1. Define a new environment, i.e. production-apollo
2. Add the option in <project.home>/build/menu.xml (see bold in example below)

```
<target name="menu-environment" description="Entry point for build script" unless="menu.environment">
 <!-- display the build choices -->
 <echo message="Available environments:"/>
 	<echo message=" 1) Local build"/>
 	<echo message=" 2) Dev build"/>
 	<echo message=" 3) Staging build"/>
 	<echo message=" 4) Production build"/>
 	<echo message=" 5) Production-apollo build"/>
 	<echo message=" Q) Quit"/>
	<input message="Choose an environment: " validargs="1,2,3,4,5,Q" addproperty="menu.environment"/>
 	...
 	<condition property="environment" value="production-apollo">
 		<equals arg1="5" arg2="${menu.environment}"/> 
 	</condition>
```

Create the property file based on local.properties
<project.home>/build/production-apollo.properties
[Distributed Environment] Create the configuration file based on appian-topology.xml.local
<project.home>/ear/suite/ear/conf/appian-topology.xml.production-apollo

### Adding Menu Actions  ###

The Standard Build Process ships with 7 configured menu actions. Following three simple steps, it is possible to add more specific tasks related to your project.
To be able to add a new menu action, there are three simple steps to follow.
Define a new action, i.e. JUnit
Add the option in <project.home>/build/menu.xml (see bold in example below)


```
<target name="menu" description="Entry point for build script" depends="menu-init,menu-environment">
<echo message="menu environment: ${menu.environment}" />
<!-- display available actions -->
<echo message="Choose an action:"/>
<echo message="  0) Install JBoss and Deploy all"/>
...
<echo message=" 7) JUnit"/>
<echo message=" Q) Quit"/>
<echo message=" "/>
<echo message="You can avoid this menu by setting the environment variable"/>
<echo message="APPIAN_PROJECT_ACTION to be equal to the value you select here"/>
<echo message=" "/>
<input message="Choose an action: "
validargs="0,1,2,3,4,5,6,7,Q"
addproperty="menu.action"/>
...
<condition property="action" value="plugins">
<equals arg1="6" arg2="${menu.action}"/>
</condition>
<condition property="action" value="JUnit">
<equals arg1="7" arg2="${menu.action}"/>
</condition>
<condition property="overwrite" value="false" else="true">
<or>
...
```

1. Add the new target to <project.home>/build/build.xml:

```
<target name="JUnit">
...
<!-- Do JUnit -->
...
</target>
```



