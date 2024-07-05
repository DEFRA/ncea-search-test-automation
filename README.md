# NCEA-Geonetwork

This repository is a cloned copy of the repository https://github.com/geonetwork/core-geonetwork on the tag 4.4.0
Source: https://github.com/geonetwork/core-geonetwork/commit/e669c78201896f0b5ff3504195be7c799cff227c

This is a Java project that uses JDK 1.8, and Spring version 5.3.30
The project is built in Maven.



**How to run the project locally:**

1. To open the project for development it is best to have Intellij Community Edition:  
https://www.jetbrains.com/idea/download/download-thanks.html?platform=macM1&code=IIC  


2. Load the project by loading the pom.xml file, and select Open as project.  


3. In the terminal you will have to execute the following commands:
git submodule init   
git submodule update  
git submodule update --init  


4. Make sure the project is executing with Java 8, so File -> Project Structure -> SDK: Coretto 1.8 


5. Change the Maven configurations for install: 
In the Panel on the right side click on the big letter M,   
to open the maven configurations.  
Choose GeoNetwork opensource -> Lifecycle -> right click on "install" -> Modify run configurations ...  
In the field Run, replace the contents with this: clean install -DskipTests -DschemasCopy=true -T 2C -f pom.xml  
Apply, close. Now your modified configuration will be available to execute from:  
GeoNetwork opensource -> Run Configurations -> geonetwork[install]

  
6. Change the Maven configurations for running locally:  
In the Panel on the right side click on the big letter M, to open the maven configurations.  
Choose GeoNetwork opensource -> GeoNetwork Web Module -> Plugins -> jetty -> right click on "jetty:run" -> Modify run configurations ...  
In the field Run, replace the contents with this: jetty:run -Penv-dev -f pom.xml  
Apply, close. Now your modified configuration will be available to execute from:  
GeoNetwork opensource -> GeoNetwork Web Module -> Run Configurations -> gn-web-app[jetty:run]    


7. To run the project locally you have to first build the project, with the first run configuration you created  
geonetwork[install]. After this finishes successfully, click on the second run configuration you created gn-web-app[jetty:run]





