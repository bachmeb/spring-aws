# spring-aws

Build routine to deploy a new webapp using Spring on AWS

### References
* https://spring.io/guides/gs/rest-service/
* http://exponential.io/blog/2015/03/30/install-gradle-on-ubuntu-linux/
* http://www.tldp.org/LDP/Linux-Filesystem-Hierarchy/html/opt.html
* http://serverfault.com/questions/664643/how-can-i-upgrade-to-java-1-8-on-an-amazon-linux-server

##### Create a new vm
https://aws.amazon.com/ec2/

##### Step 1: Choose an Amazon Machine Image (AMI)
	Amazon Linux AMI 2015.09.1 (HVM), SSD Volume Type - ami-f0091d91

##### Step 2: Choose an Instance Type
	Family: General purpose
	Type: t2.micro
	vCPUs: 1
	Memory: 1
	Instance Storage: EBS only
	EBS-Optimized: -
	Network Performance: Low to Moderate

##### Step 3: Configure Instance Details
	Number of instances: 	1
	Network: (default)
	Subnet: No preference (default subnet in any Availability Zone)
	EBS-optimized: No
	Monitoring: No
	Termination protection: YES <---- UPDATE THIS
	Shutdown behavior: Stop
	IAM role: None
	Tenancy: default
	Host ID: 
	Affinity: Off
	Kernel ID: Use default
	RAM disk ID: Use default
	User data: 
	Assign Public IP: Use subnet setting (Enable)
	Network interfaces: 
	Purchasing option: On demand

##### Step 4: Add Storage
	Volume Type: Root
	Device: /dev/xvda
	Snapshot: snap-ad8e61f8
	Size (GiB): 8
	Volume Type: General Purpose SSD (GP2)
	IOPS: 24 / 3000
	Delete on Termination: Yes
	Encrypted: Not Encrypted

##### Step 5: Tag Instance
	Key: Name
	Value:

##### Step 6: Configure Security Group 
*Allow ICMP, SSH, and TCP traffic from your IP address*

    Type	    Protocol	Port Range	Source
    All TCP	    TCP	        0 - 65535	your ip address/32
    SSH	        TCP	        22	        your ip address/32
    All ICMP	All	        N/A         	your ip address/32

##### Download the key pair and change the mode to 400
	chmod 400 pemfile.pem

##### Ping the IP address to confirm that ICMP traffic is allowed from your IP address
	ping [ec2.ipa.ddr.ess]

##### Connect via SSH
	ssh -i pemfile.pem ec2-user@[ec2.ipa.ddr.ess]

##### Check the time
	date
	
##### List the available time zones
	ls /usr/share/zoneinfo/

##### Update the /etc/sysconfig/clock file with your time zone
	sudo vim /etc/sysconfig/clock
```
ZONE="America/New_York"
UTC=false
```

##### Create a symbolic link between /etc/localtime and your time zone file
	sudo ln -sf /usr/share/zoneinfo/America/New_York /etc/localtime

##### Reboot the system to pick up the new time zone information in all services and applications
	reboot

##### Connect via SSH
	ssh -i pemfile.pem ec2-user@[ec2.ipa.ddr.ess]

##### Check the time
	date

##### Switch to root 
	sudo su

##### Ask who am I?
	whoami
	
##### Update yum
	yum update

##### Create a user account for development
	useradd [your new account name]
    
##### Set the password for your development account
	passwd [your new account name]

##### Edit sudoers file
	vim /etc/sudoers
	:$

##### Add development account to sudoers file
	## LET ME SUDO
	[your new account name] ALL=(ALL) ALL

##### Switch to development user
	su [your new account name]
    
##### Ask who am I?
	whoami
    
##### Check the value of the home directory environment variable
	echo $HOME

##### Go home
	cd ~

##### Check the Linux distro version
	cat /proc/version

##### Check the hostname of the ec2 instance
	hostname

##### Check the DNS domain name of the ec2 instance
	dnsdomainname

##### Check the internal IP address
	ifconfig

##### Check the external IP address
	wget http://ipinfo.io/ip -qO -

##### Show all of the environment variables
	declare

##### Look for JAVA in the list of environmental variables
	env | grep JAVA

##### Echo the current JAVA_HOME
	echo $JAVA_HOME

##### Ask where is Java?
	whereis java

##### Check the Java version
*As of Feb 2016, the standard Java version installed on Amazon Linux AMI is 1.7*  
```
java -version
```

##### See if the Java compiler is installed
	whereis javac

##### Search yum for openjdk
	yum search openjdk

##### Install the Open JDK version 1.8
	sudo yum install java-1.8.0-openjdk-devel

##### Check the Java compiler version
*Should be javac 1.8.0_65*
```
javac -version
```

##### Check the Java version
*Should still be java version 1.7.0_91*  
```
java -version
```
```
java version "1.7.0_91"
OpenJDK Runtime Environment (amzn-2.6.2.2.63.amzn1-x86_64 u91-b00)
OpenJDK 64-Bit Server VM (build 24.91-b01, mixed mode)
```

##### Tell Linux to use the JDK 1.8 Java interpreter
    sudo alternatives --config java

##### Check the Java version
*Should now be 1.8*  
```
java -version
```
```
openjdk version "1.8.0_65"
OpenJDK Runtime Environment (build 1.8.0_65-b17)
OpenJDK 64-Bit Server VM (build 25.65-b01, mixed mode)
```

##### Echo the $JAVA_HOME environment variable
	echo $JAVA_HOME

##### Read the symlinks in /usr/lib/jvm/
	ls -l /usr/lib/jvm/

##### Confirm that /usr/lib/jvm/java points to etc/alternatives/java_sdk
	ls -l /usr/lib/jvm/java

##### Confirm that /etc/alternatives/java_sdk points to /usr/lib/jvm/java-1.8.0-openjdk.x86_64
	ls -l /etc/alternatives/java_sdk

##### Confirm that /usr/lib/jvm/java-1.8.0-openjdk.x86_64 is the installation directory for Java SDK 1.8
	ls -l /usr/lib/jvm/java-1.8.0-openjdk.x86_64

##### Set the JAVA_HOME environment variable to point to the JDK 1.8 directory
	export JAVA_HOME='/usr/lib/jvm/java'

##### Echo the $JAVA_HOME environment variable
	echo $JAVA_HOME

##### List the contents of the $JAVA_HOME/ directory
	ls -l $JAVA_HOME/

##### Install git
	sudo yum install git
	
##### Create a project folder
	mkdir -p ~/git/sp-aws

##### Initialize project repository
	cd ~/git/sp-aws
	git init
    
##### Make a .gitignore file
	nano .gitignore
```
*.class

# Mobile Tools for Java (J2ME)
.mtj.tmp/

# Package Files #
*.jar
*.war
*.ear

# virtual machine crash logs, see http://www.java.com/en/download/help/error_hotspot.xml
hs_err_pid*

# AWS 
*.pem

# Tomcat 
build.properties

# Gradle
build/
.gradle/
```

##### Create remote repository
    http://github.com/

##### Configure repository
    git config user.name {username}
    git config user.email {emailaddress@example.com}

##### Add remote origin to local repository
    git remote add origin http://github.com/{username}/{projectname}.git

##### Pull from remote
    git pull origin master

##### Check status
    git status

##### Add files to the git index
	git add --all

##### Commit files
	git commit -m "write your message"
    
##### Push to remote
    git push --set-upstream origin master

##### Search yum for available Gradle packages
    yum search gradle

##### Make an installation directory for Gradle
    sudo mkdir -p /opt/packages/gradle && cd $_
    pwd

##### Download Gradle 2.10
    sudo wget https://services.gradle.org/distributions/gradle-2.10-bin.zip
    
##### Unzip the Gradle package
    sudo unzip gradle-2.10-bin.zip
    ls

##### Create a symlink 
    sudo ln -s /opt/packages/gradle/gradle-2.10/ /opt/gradle

##### Set GRADLE_HOME
    export GRADLE_HOME="/opt/gradle"

##### Set the PATH variable
    PATH="$PATH:$GRADLE_HOME/bin"

##### See if Gradle is installed correctly
    gradle -version

##### Add GRADLE_HOME/bin to your bash profile script
    nano ~/.bash_profile
```bash
# Gradle
if [ -d "$HOME/opt/gradle" ]; then
    export GRADLE_HOME="/opt/gradle"
    PATH="$PATH:$GRADLE_HOME/bin"
fi
```

##### Create a Gradle build file in the project root directory
    cd ~/git/sp-aws
    vim build.gradle
```gradle
buildscript {
    repositories {
        mavenCentral()
    }
    dependencies {
        classpath("org.springframework.boot:spring-boot-gradle-plugin:1.3.2.RELEASE")
    }
}

apply plugin: 'java'
apply plugin: 'eclipse'
apply plugin: 'idea'
apply plugin: 'spring-boot'

jar {
    baseName = 'gs-rest-service'
    version =  '0.1.0'
}

repositories {
    mavenCentral()
}

sourceCompatibility = 1.8
targetCompatibility = 1.8

dependencies {
    compile("org.springframework.boot:spring-boot-starter-web")
    testCompile("junit:junit")
}

task wrapper(type: Wrapper) {
    gradleVersion = '2.3'
}
```

##### Make a subdirectory for Java class files
    mkdir -p ~/git/sp-aws/src/main/java/hello
    
##### Create a resource representation class
    vim  ~/git/sp-aws/src/main/java/hello/Greeting.java
    
```java
package hello;

public class Greeting {

    private final long id;
    private final String content;

    public Greeting(long id, String content) {
        this.id = id;
        this.content = content;
    }

    public long getId() {
        return id;
    }

    public String getContent() {
        return content;
    }
}
```
##### Create a resource controller
    vim  ~/git/sp-aws/src/main/java/hello/GreetingController.java
```java
package hello;

import java.util.concurrent.atomic.AtomicLong;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestParam;
import org.springframework.web.bind.annotation.RestController;

@RestController
public class GreetingController {

    private static final String template = "Hello, %s!";
    private final AtomicLong counter = new AtomicLong();

    @RequestMapping("/greeting")
    public Greeting greeting(@RequestParam(value="name", defaultValue="World") String name) {
        return new Greeting(counter.incrementAndGet(),
                            String.format(template, name));
    }
}
```
##### Make the application executable
    vim  ~/git/sp-aws/src/main/java/hello/Application.java
```java
package hello;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication
public class Application {

    public static void main(String[] args) {
        SpringApplication.run(Application.class, args);
    }
}
```
##### Run Gradle from the same directory where build.gradle is saved
    cd ~/git/sp-aws
    gradle
```
Download https://repo1.maven.org/maven2/org/springframework/boot/spring-boot-gradle-plugin/1.3.2.RELEASE/spring-boot-gradle-plugin-1.3.2.RELEASE.pom
Download https://repo1.maven.org/maven2/org/springframework/boot/spring-boot-tools/1.3.2.RELEASE/spring-boot-tools-1.3.2.RELEASE.pom
Download https://repo1.maven.org/maven2/org/springframework/boot/spring-boot-parent/1.3.2.RELEASE/spring-boot-parent-1.3.2.RELEASE.pom
Download https://repo1.maven.org/maven2/org/springframework/boot/spring-boot-dependencies/1.3.2.RELEASE/spring-boot-dependencies-1.3.2.RELEASE.pom
Download https://repo1.maven.org/maven2/org/springframework/spring-framework-bom/4.2.4.RELEASE/spring-framework-bom-4.2.4.RELEASE.pom
Download https://repo1.maven.org/maven2/org/springframework/data/spring-data-releasetrain/Gosling-SR2A/spring-data-releasetrain-Gosling-SR2A.pom
Download https://repo1.maven.org/maven2/org/springframework/data/build/spring-data-build/1.7.3.RELEASE/spring-data-build-1.7.3.RELEASE.pom
Download https://repo1.maven.org/maven2/org/springframework/integration/spring-integration-bom/4.2.4.RELEASE/spring-integration-bom-4.2.4.RELEASE.pom
Download https://repo1.maven.org/maven2/org/springframework/security/spring-security-bom/4.0.3.RELEASE/spring-security-bom-4.0.3.RELEASE.pom
Download https://repo1.maven.org/maven2/org/springframework/boot/spring-boot-loader-tools/1.3.2.RELEASE/spring-boot-loader-tools-1.3.2.RELEASE.pom
Download https://repo1.maven.org/maven2/io/spring/gradle/dependency-management-plugin/0.5.4.RELEASE/dependency-management-plugin-0.5.4.RELEASE.pom
Download https://repo1.maven.org/maven2/org/springframework/spring-core/4.2.4.RELEASE/spring-core-4.2.4.RELEASE.pom
Download https://repo1.maven.org/maven2/commons-logging/commons-logging/1.2/commons-logging-1.2.pom
Download https://repo1.maven.org/maven2/org/apache/commons/commons-parent/34/commons-parent-34.pom
Download https://repo1.maven.org/maven2/org/apache/apache/13/apache-13.pom
Download https://repo1.maven.org/maven2/org/springframework/boot/spring-boot-gradle-plugin/1.3.2.RELEASE/spring-boot-gradle-plugin-1.3.2.RELEASE.jar
Download https://repo1.maven.org/maven2/org/springframework/boot/spring-boot-loader-tools/1.3.2.RELEASE/spring-boot-loader-tools-1.3.2.RELEASE.jar
Download https://repo1.maven.org/maven2/io/spring/gradle/dependency-management-plugin/0.5.4.RELEASE/dependency-management-plugin-0.5.4.RELEASE.jar
Download https://repo1.maven.org/maven2/org/springframework/spring-core/4.2.4.RELEASE/spring-core-4.2.4.RELEASE.jar
Download https://repo1.maven.org/maven2/commons-logging/commons-logging/1.2/commons-logging-1.2.jar
:help

Welcome to Gradle 2.10.

To run a build, run gradle <task> ...

To see a list of available tasks, run gradle tasks

To see a list of command-line options, run gradle --help

To see more detail about a task, run gradle help --task <task>

BUILD SUCCESSFUL

Total time: 12.12 secs
```

##### Build an executable JAR
    gradle build
```

:compileJava
Download https://repo1.maven.org/maven2/org/springframework/boot/spring-boot-starter-parent/1.3.2.RELEASE/spring-boot-starter-parent-1.3.2.RELEASE.pom
Download https://repo1.maven.org/maven2/org/springframework/boot/spring-boot-starter-web/1.3.2.RELEASE/spring-boot-starter-web-1.3.2.RELEASE.pom
Download https://repo1.maven.org/maven2/org/springframework/boot/spring-boot-starters/1.3.2.RELEASE/spring-boot-starters-1.3.2.RELEASE.pom
Download https://repo1.maven.org/maven2/org/springframework/boot/spring-boot-starter/1.3.2.RELEASE/spring-boot-starter-1.3.2.RELEASE.pom
Download https://repo1.maven.org/maven2/org/springframework/boot/spring-boot-starter-tomcat/1.3.2.RELEASE/spring-boot-starter-tomcat-1.3.2.RELEASE.pom
Download https://repo1.maven.org/maven2/org/springframework/boot/spring-boot-starter-validation/1.3.2.RELEASE/spring-boot-starter-validation-1.3.2.RELEASE.pom
Download https://repo1.maven.org/maven2/com/fasterxml/jackson/core/jackson-databind/2.6.5/jackson-databind-2.6.5.pom
Download https://repo1.maven.org/maven2/com/fasterxml/jackson/jackson-parent/2.6.2/jackson-parent-2.6.2.pom
Download https://repo1.maven.org/maven2/com/fasterxml/oss-parent/24/oss-parent-24.pom
Download https://repo1.maven.org/maven2/org/springframework/spring-web/4.2.4.RELEASE/spring-web-4.2.4.RELEASE.pom
Download https://repo1.maven.org/maven2/org/springframework/spring-webmvc/4.2.4.RELEASE/spring-webmvc-4.2.4.RELEASE.pom
Download https://repo1.maven.org/maven2/org/springframework/boot/spring-boot/1.3.2.RELEASE/spring-boot-1.3.2.RELEASE.pom
Download https://repo1.maven.org/maven2/org/springframework/boot/spring-boot-autoconfigure/1.3.2.RELEASE/spring-boot-autoconfigure-1.3.2.RELEASE.pom
Download https://repo1.maven.org/maven2/org/springframework/boot/spring-boot-starter-logging/1.3.2.RELEASE/spring-boot-starter-logging-1.3.2.RELEASE.pom
Download https://repo1.maven.org/maven2/org/yaml/snakeyaml/1.16/snakeyaml-1.16.pom
Download https://repo1.maven.org/maven2/org/apache/tomcat/embed/tomcat-embed-core/8.0.30/tomcat-embed-core-8.0.30.pom
Download https://repo1.maven.org/maven2/org/apache/tomcat/embed/tomcat-embed-el/8.0.30/tomcat-embed-el-8.0.30.pom
Download https://repo1.maven.org/maven2/org/apache/tomcat/embed/tomcat-embed-logging-juli/8.0.30/tomcat-embed-logging-juli-8.0.30.pom
Download https://repo1.maven.org/maven2/org/apache/tomcat/embed/tomcat-embed-websocket/8.0.30/tomcat-embed-websocket-8.0.30.pom
Download https://repo1.maven.org/maven2/org/hibernate/hibernate-validator/5.2.2.Final/hibernate-validator-5.2.2.Final.pom
Download https://repo1.maven.org/maven2/org/hibernate/hibernate-validator-parent/5.2.2.Final/hibernate-validator-parent-5.2.2.Final.pom
Download https://repo1.maven.org/maven2/org/jboss/arquillian/arquillian-bom/1.1.9.Final/arquillian-bom-1.1.9.Final.pom
Download https://repo1.maven.org/maven2/org/jboss/shrinkwrap/shrinkwrap-bom/1.2.2/shrinkwrap-bom-1.2.2.pom
Download https://repo1.maven.org/maven2/org/jboss/shrinkwrap/resolver/shrinkwrap-resolver-bom/2.1.1/shrinkwrap-resolver-bom-2.1.1.pom
Download https://repo1.maven.org/maven2/org/jboss/shrinkwrap/descriptors/shrinkwrap-descriptors-bom/2.0.0-alpha-7/shrinkwrap-descriptors-bom-2.0.0-alpha-7.pom
Download https://repo1.maven.org/maven2/com/fasterxml/jackson/core/jackson-annotations/2.6.5/jackson-annotations-2.6.5.pom
Download https://repo1.maven.org/maven2/com/fasterxml/jackson/jackson-parent/2.6.1/jackson-parent-2.6.1.pom
Download https://repo1.maven.org/maven2/com/fasterxml/oss-parent/23/oss-parent-23.pom
Download https://repo1.maven.org/maven2/com/fasterxml/jackson/core/jackson-core/2.6.5/jackson-core-2.6.5.pom
Download https://repo1.maven.org/maven2/org/springframework/spring-aop/4.2.4.RELEASE/spring-aop-4.2.4.RELEASE.pom
Download https://repo1.maven.org/maven2/org/springframework/spring-beans/4.2.4.RELEASE/spring-beans-4.2.4.RELEASE.pom
Download https://repo1.maven.org/maven2/org/springframework/spring-context/4.2.4.RELEASE/spring-context-4.2.4.RELEASE.pom
Download https://repo1.maven.org/maven2/org/springframework/spring-expression/4.2.4.RELEASE/spring-expression-4.2.4.RELEASE.pom
Download https://repo1.maven.org/maven2/ch/qos/logback/logback-classic/1.1.3/logback-classic-1.1.3.pom
Download https://repo1.maven.org/maven2/ch/qos/logback/logback-parent/1.1.3/logback-parent-1.1.3.pom
Download https://repo1.maven.org/maven2/org/slf4j/jcl-over-slf4j/1.7.13/jcl-over-slf4j-1.7.13.pom
Download https://repo1.maven.org/maven2/org/slf4j/slf4j-parent/1.7.13/slf4j-parent-1.7.13.pom
Download https://repo1.maven.org/maven2/org/slf4j/jul-to-slf4j/1.7.13/jul-to-slf4j-1.7.13.pom
Download https://repo1.maven.org/maven2/org/slf4j/log4j-over-slf4j/1.7.13/log4j-over-slf4j-1.7.13.pom
Download https://repo1.maven.org/maven2/javax/validation/validation-api/1.1.0.Final/validation-api-1.1.0.Final.pom
Download https://repo1.maven.org/maven2/org/jboss/logging/jboss-logging/3.3.0.Final/jboss-logging-3.3.0.Final.pom
Download https://repo1.maven.org/maven2/org/jboss/jboss-parent/15/jboss-parent-15.pom
Download https://repo1.maven.org/maven2/com/fasterxml/classmate/1.1.0/classmate-1.1.0.pom
Download https://repo1.maven.org/maven2/org/sonatype/oss/oss-parent/7/oss-parent-7.pom
Download https://repo1.maven.org/maven2/aopalliance/aopalliance/1.0/aopalliance-1.0.pom
Download https://repo1.maven.org/maven2/ch/qos/logback/logback-core/1.1.3/logback-core-1.1.3.pom
Download https://repo1.maven.org/maven2/org/slf4j/slf4j-api/1.7.13/slf4j-api-1.7.13.pom
Download https://repo1.maven.org/maven2/org/springframework/boot/spring-boot-starter-web/1.3.2.RELEASE/spring-boot-starter-web-1.3.2.RELEASE.jar
Download https://repo1.maven.org/maven2/org/springframework/boot/spring-boot-starter/1.3.2.RELEASE/spring-boot-starter-1.3.2.RELEASE.jar
Download https://repo1.maven.org/maven2/org/springframework/boot/spring-boot-starter-tomcat/1.3.2.RELEASE/spring-boot-starter-tomcat-1.3.2.RELEASE.jar
Download https://repo1.maven.org/maven2/org/springframework/boot/spring-boot-starter-validation/1.3.2.RELEASE/spring-boot-starter-validation-1.3.2.RELEASE.jar
Download https://repo1.maven.org/maven2/com/fasterxml/jackson/core/jackson-databind/2.6.5/jackson-databind-2.6.5.jar
Download https://repo1.maven.org/maven2/org/springframework/spring-web/4.2.4.RELEASE/spring-web-4.2.4.RELEASE.jar
Download https://repo1.maven.org/maven2/org/springframework/spring-webmvc/4.2.4.RELEASE/spring-webmvc-4.2.4.RELEASE.jar
Download https://repo1.maven.org/maven2/org/springframework/boot/spring-boot/1.3.2.RELEASE/spring-boot-1.3.2.RELEASE.jar
Download https://repo1.maven.org/maven2/org/springframework/boot/spring-boot-autoconfigure/1.3.2.RELEASE/spring-boot-autoconfigure-1.3.2.RELEASE.jar
Download https://repo1.maven.org/maven2/org/springframework/boot/spring-boot-starter-logging/1.3.2.RELEASE/spring-boot-starter-logging-1.3.2.RELEASE.jar
Download https://repo1.maven.org/maven2/org/yaml/snakeyaml/1.16/snakeyaml-1.16.jar
Download https://repo1.maven.org/maven2/org/apache/tomcat/embed/tomcat-embed-core/8.0.30/tomcat-embed-core-8.0.30.jar
Download https://repo1.maven.org/maven2/org/apache/tomcat/embed/tomcat-embed-el/8.0.30/tomcat-embed-el-8.0.30.jar
Download https://repo1.maven.org/maven2/org/apache/tomcat/embed/tomcat-embed-logging-juli/8.0.30/tomcat-embed-logging-juli-8.0.30.jar
Download https://repo1.maven.org/maven2/org/apache/tomcat/embed/tomcat-embed-websocket/8.0.30/tomcat-embed-websocket-8.0.30.jar
Download https://repo1.maven.org/maven2/org/hibernate/hibernate-validator/5.2.2.Final/hibernate-validator-5.2.2.Final.jar
Download https://repo1.maven.org/maven2/com/fasterxml/jackson/core/jackson-annotations/2.6.5/jackson-annotations-2.6.5.jar
Download https://repo1.maven.org/maven2/com/fasterxml/jackson/core/jackson-core/2.6.5/jackson-core-2.6.5.jar
Download https://repo1.maven.org/maven2/org/springframework/spring-aop/4.2.4.RELEASE/spring-aop-4.2.4.RELEASE.jar
Download https://repo1.maven.org/maven2/org/springframework/spring-beans/4.2.4.RELEASE/spring-beans-4.2.4.RELEASE.jar
Download https://repo1.maven.org/maven2/org/springframework/spring-context/4.2.4.RELEASE/spring-context-4.2.4.RELEASE.jar
Download https://repo1.maven.org/maven2/org/springframework/spring-expression/4.2.4.RELEASE/spring-expression-4.2.4.RELEASE.jar
Download https://repo1.maven.org/maven2/ch/qos/logback/logback-classic/1.1.3/logback-classic-1.1.3.jar
Download https://repo1.maven.org/maven2/org/slf4j/jcl-over-slf4j/1.7.13/jcl-over-slf4j-1.7.13.jar
Download https://repo1.maven.org/maven2/org/slf4j/jul-to-slf4j/1.7.13/jul-to-slf4j-1.7.13.jar
Download https://repo1.maven.org/maven2/org/slf4j/log4j-over-slf4j/1.7.13/log4j-over-slf4j-1.7.13.jar
Download https://repo1.maven.org/maven2/javax/validation/validation-api/1.1.0.Final/validation-api-1.1.0.Final.jar
Download https://repo1.maven.org/maven2/org/jboss/logging/jboss-logging/3.3.0.Final/jboss-logging-3.3.0.Final.jar
Download https://repo1.maven.org/maven2/com/fasterxml/classmate/1.1.0/classmate-1.1.0.jar
Download https://repo1.maven.org/maven2/aopalliance/aopalliance/1.0/aopalliance-1.0.jar
Download https://repo1.maven.org/maven2/ch/qos/logback/logback-core/1.1.3/logback-core-1.1.3.jar
Download https://repo1.maven.org/maven2/org/slf4j/slf4j-api/1.7.13/slf4j-api-1.7.13.jar
:processResources UP-TO-DATE
:classes
:findMainClass
:jar
:bootRepackage
:assemble
:compileTestJava UP-TO-DATE
:processTestResources UP-TO-DATE
:testClasses UP-TO-DATE
:test UP-TO-DATE
:check UP-TO-DATE
:build

BUILD SUCCESSFUL

Total time: 18.515 secs

This build could be faster, please consider using the Gradle Daemon: https://docs.gradle.org/2.10/userguide/gradle_daemon.html
```

##### Run the JAR file:
    java -jar build/libs/gs-rest-service-0.1.0.jar

##### Open another console and connect to aws
    ssh -i [pemfile.pem] ec2-user@[ec2 ip address]
    
##### Switch to your developer account
    sudo su [developer account]
    
##### Install lynx
    sudo yum install lynx

##### Test the service from AWS
    lynx http://localhost:8080/greeting
The response should be:
```json
{"id":1,"content":"Hello, World!"}
```
##### Provide a name query string parameter
    lynx http://localhost:8080/greeting?name=User
The reply should be 
```json
{"id":2,"content":"Hello, User!"}
```
##### Test the service from your local machine
    Open a web browser and go to http://[ec2 ip address]:8080/greeting
The response should be:
```json
{"id":3,"content":"Hello, World!"}
```
##### Provide a name query string parameter
    Open a web browser and go to http://[ec2 ip address]:8080/greeting?name=User
The reply should be 
```json
{"id":4,"content":"Hello, User!"}
```
##### Commit changes and push to the remote repository
    git status
    git add --all
    git commit -m "complete spring deployment"
    git push --set-upstream origin master
