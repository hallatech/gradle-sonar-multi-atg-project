gradle-sonar-multi-atg-project
==============================

A workaround solution for running SonarQube with Gradle in hierarchical source project (ATG / Oracle Commerce style) 

Synopsis
--------
1. Current releases of SonarQube (as of 4.5.1) do not support analysing source code in parent projects (including the root).
Oracle Web Commerce (ATG) style projects have their modules in a hierarchical order with source potentially at every level

2. Gradle (as of 2.2.1) generates sonar project properties according to its own structure but no in accordance with Sonar limitations

3. Manipulation of the sonarRunner { sonarProperties {} } properties gets you some way to changing the defaults but you cannot currently
change the sonar.modules property which is the key to making the structure appear differently to Sonar.

4. Some manipulation may be required during gradle project configuration phase to access the sonarRunner task but this throws
future deprecation warnings for Gradle 3.0

5. We need to exclude those sub-projects which don't have source code as well as exclude the root project as a source project (for Sonar)

6. We want to see the modules in SonarQube named accordingly to the pattern <rootProject.name>.[<(sub)project.name>...].<(leaf)project.name>

7. We need to make all source code sub-projects appear as a leaf-node project including the root project itself (by copying root as a
named module)

Configuration and Execution
---------------------------
1. Include this file into your build e.g. copy into gradle sub-directory  
``apply from: 'gradle/sonar.gradle'``
2. Execute the default gradle sonar-runner task but in preview mode (i.e. doesn't persist results to the database)  
``gradle sonarRunner -Dsonar.analysis.mode=preview``
3. Refactor the generated properties file  
``gradle refactorSonarProperties``
4. Copy the file to the root of the project (Assumes you are in the root)  
``cp build/tmp/sonarRunner/sonar-project.properties .``
5. Execute the pre-installed sonar-runner (See sonar runner installation)  
``sonar-runner``
6. Delete the properties. If you leave it here it will interfere with the next gradle sonarRunner execution  
``rm sonar-project.properties``

