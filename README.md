mvn-repo
========

To use reposititory add to pom:
<repositories>
        <repository>
            <id>mvn-repo</id>
            <url>https://raw.github.com/xvik/mvn-repo/master</url>
            <releases>
                <enabled>true</enabled>
            </releases>
            <snapshots>
                <enabled>true</enabled>
                <updatePolicy>daily</updatePolicy>
            </snapshots>
        </repository>
 </repositories>

To upload single jar:
1. Checkout repository, e.g. into ~/mvn-repo
2. Uplaod jar
 mvn deploy:deploy-file \
 	-Dfile=path_to_jar \
	-DgroupId=... \
	-DartifactId=...\
	-Dversion=... \
	-Dpackaging=jar \
	-DgeneratePom=true \
	-DcreateChecksum=true \
	-Durl=file://~/mvn-repo
3. Push changes

To deploy artifacts with mvn deploy:
1. Add github plugin
<plugin>
                <groupId>com.github.github</groupId>
                <artifactId>site-maven-plugin</artifactId>
                <version>0.7</version>
                <configuration>
                    <message>Maven artifacts for ${project.artifactId} ${project.version}</message>  <!-- git commit message -->
                    <noJekyll>true</noJekyll>                                  <!-- disable webpage processing -->
                    <merge>true</merge> <!-- to not clean repo contents on each deploy -->
                    <outputDirectory>${project.build.directory}/mvn-repo</outputDirectory> <!-- matches distribution management repository url above -->
                    <branch>refs/heads/master</branch>                       <!-- remote branch name -->
                    <includes><include>**/*</include></includes>
                    <repositoryName>mvn-repo</repositoryName>      <!-- github repo name -->
                    <repositoryOwner>xvik</repositoryOwner>    <!-- github username  -->
                </configuration>
                <executions>
                    <!-- run site-maven-plugin's 'site' target as part of the build's normal 'deploy' phase -->
                    <execution>
                        <goals>
                            <goal>site</goal>
                        </goals>
                        <phase>deploy</phase>
                    </execution>
                </executions>
            </plugin>
2. Add property into properties section
<github.global.server>github</github.global.server>
3.Add
<distributionManagement>
        <repository>
            <id>mvn-repo-github</id>
            <name>Temporary Staging Repository</name>
            <url>file://${project.build.directory}/mvn-repo</url>
        </repository>
    </distributionManagement>
4. Set github credentials in ~/.m2/settings.xml (servers section):
<server>
      <id>github</id>
      <username>USER</username>
      <password>PASSWORD</password>
    </server>
 5. perform mvn deploy