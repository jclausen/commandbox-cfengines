# CommandBox CFEngine Workbench

This repository is used as a workbench for updating and modifying the CF Engines


## Engine Upgrade Instructions

1.  Install Docker and Docker Compose
2.  Start the commandbox server for the CF-Engine you wish to upgrade: `server start name=cfengine-acf[ major version number ]`
3.  Login to the administrator ( admin/commandbox ), click "Updates" and download the latest hotfix
4.  Stop the commandbox server `server stop name=cfengine-acf[ major version number ]`
5.  Copy the downloaded .jar file in `docker/ACF[ major version number ]/engine/WEB-INF/hf-updates` and copy it to a `/hotfix` directory within the root of the project ( this directory will be mounted by docker to `/user/local/tomcat/cf-hotfix` ). Them remove the exploded `engine` directory - the one exploded by commandbox is not compatibile with Tomcat.
6.  Use docker-compose to  build and start up a Tomcat installation of the engine: `docker-compose build cfengine-acf[ major version number] && docker up cfengine-acf[ major version number]`.  Catalina will log to stdout, so you'll need to open a new terminal tab for the next steps.
7.  Hit the CFIDE/administrator URL for that container ( e.g. `http://192.168.99.100:8016/Engine/CFIDE/administrator` ) to ensure the WAR is completely expanded.
8.  Type `docker ps` to see the process list.  Note the process ID for your target container ( the first column )
9.  Log in to the docker shell for that container `docker exec -it [ process id from setp #8 ] bash`
10. Run the hotfix jar manually:  `java -jar /usr/local/tomcat/cf-hotfix/[ name of jar file ]` using `/usr/local/tomcat/webapps/Engine` as your Coldfusion path.
11. Once the hotfix has been applied, use `Ctrl+C` or `Command+C` to stop the container, then restart again and hit the Administrator url to make sure all is working properly.
12. Copy the version number from the admin and use it to update the box.json file for the ACF version, which will be packaged with our release.
13. Once verified, stop the container ( `Ctrl+C` or `Command+C` ), `cd` to the `Engine` directory and create a new WAR `jar -cvf ../Engine.war *`
14. Zip the WAR and the box.json together.  Use the naming conventions already in place, according to the each major ACF version.
15. Upload the newly zipped file to `downloads.ortussolutions.com`
16. Log in to Forgebox and create a new release, using the updated box.json file for the engine.
