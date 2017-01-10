# Introduction
The Parabank demo web application and associated web services (SOAP and REST) from Parasoft.

# Build and Install
1. Build the Parabank application using Maven (`mvn clean install`). After a successful build, deploy the `parabank.war` (located in `/target`) onto a Tomcat 8.5.5 container.

NOTE: if using the coverage agent when running the functional/manual tests (see below), execute the build using "mvn -Dmaven.test.skip=true clean install jtest:monitor"

1. Parabank uses a built-in HyperSQL database. You must shut down all running instances of **Parabank** and *HyperSQL* for the build to succeed   
Otherwise several tests may fail, since there are a number of ports that are shared between the test instances and the real thing.  
  
## Tomcat notes
1. The minimum Java JDK version expected is `1.8.0`. Oracle JDK is preferred. 
1. The minimum Tomcat version is now 8.5.5, this is the expected default build compliance, if earlier version is preferred use the     
`-Dtomcat.version=X.X.X` .     
1. The version built with 8.5.5 was validated against both `8.5.5` and `9.0.M10`.
 1. At the time of this writing the version of 9.0 tested was __M10__  (emphasis on M meaning milestone and therefore unstable). I'm assuming that in the future the **Parabank** will no longer function with `9.0.x` use the  `-Dtomcat.version=9.0.x` when building for now, to insure stability and compliance. Once Tomcat 9.0 is GA the default will be updated to reflect that fact. 
 1. Example:	
 ```bash
	#replace the X.X.X with the correct version of tomcat like 8.0.37 
	mvn clean install -Dtomcat.version=X.X.X
 ```	

1. To prevent verbose cache warnings in the tomcat log::
        
 ```text
 Oct 06, 2016 3:53:33 PM org.apache.catalina.webresources.Cache getResource
 WARNING: Unable to add the resource at [/WEB-INF/lib/wss4j-ws-security-stax-2.1.3.jar] to the cache because there was insufficient free space available after evicting expired cache entries - consider increasing the maximum size of the cache
 ```

   Add the following to the `<tomcat install>/config/context.xml` make sure to place this block before `</Context>` tag. This setting will provide `100 MB`  for caching 

  ```xml
  <Resources cachingAllowed="true" cacheMaxSize="102400" />
  ```
1. To support clean re-deployments of **Parabank** please add the following to the `<Context>` tag of the `<tomcat install>/config/context.xml`

 ```xml
 <Context antiResourceLocking="true">
 ```

# Test scripts 
* All scripts exist in two flavors (.bat and .sh) for Windows and Linux respectively. 
* All scripts should be executed from project directory and require the following Parasoft products (and versions)
** Parasoft DTP 5.3.0
** Parasoft Jtest 10.3.0
** Parasoft SOAtest 9.10.0

 Script                                |Description   
:------------------------------------- |:---    
__deploy-jtest-monitor(.sh\|.bat)__    | Deploys the Jtest Monitor package (created using mvn goal jtest:monitor) into directory specified by APP_COVERAGE_DIR in `set-vars(.sh|.bat)` 
__jtest-ft-cov(.sh\|.bat)__            | Executes Parasoft Jtest DTP Engine for processing monitored coverage during functional testing with SOAtest
__jtest-mt-cov(.sh\|.bat)__            | Uploads results from manual testing efforts (managed using the Parasoft Coverage Agent Manager) and processing coverage data captured during manual testing
__jtest-sa-ut(.sh\|.bat)__             | Executes Parasoft Jtest DTP Engine for Static Analysis and Unit Testing results/coverage 
__jtest-sa-ut-delta(.sh\|.bat)__       | Same operation as `jtest-sa-ut(.sh|.bat)` but used to rescan code based for localized changes - used for demonstration purposes when scanning 'dirty' branch
__set-vars(.sh\|.bat)__                | Utility script called by other scripts to consistently set BUILD_ID and the Project name sent to DTP
__soatest(.sh\|.bat)__                 | Executes Parasoft SOAtest API and Web functional tests (including integration with Jtest DTP Engine for monitoring code coverage)
