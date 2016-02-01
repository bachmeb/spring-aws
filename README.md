# spring-aws

Build routine to deploy a new webapp using Spring on AWS

### References
* https://spring.io/guides/gs/rest-service/
* http://exponential.io/blog/2015/03/30/install-gradle-on-ubuntu-linux/
* http://www.tldp.org/LDP/Linux-Filesystem-Hierarchy/html/opt.html
* http://serverfault.com/questions/664643/how-can-i-upgrade-to-java-1-8-on-an-amazon-linux-server

##### Create a new vm
https://aws.amazon.com/ec2/

##### Configure the security group to allow ICMP, SSH, and TCP traffic from your IP address
    Type	    Protocol	Port Range	Source
    All TCP	    TCP	        0 - 65535	your ip address/32
    SSH	        TCP	        22	        your ip address/32
    All ICMP	All	        N/A         your ip address/32

##### Download the key pair and change the mode to 400
    chmod 400 pemfile.pem

##### Ping the IP address to confirm that ICMP traffic is allowed from your IP address
    ping [ec2.ipa.ddr.ess]

##### Update the DNS record at the domain registrar to point the domain name to the new IP address
    ; A Records
    @ 600 IN A [000.000.000.000] <-- The EC2 IP address
    mail 600 IN A [000.000.000.000] <-- The EC2 IP address
    
    ; CNAME Records
    ftp 600 IN CNAME @
    www 600 IN CNAME @
    
    ; MX Records
    @ 600 IN MX 1 [mail.somedomain.whatever]

##### Connect via SSH
    ssh -i pemfile.pem ec2-user@[ec2.ipa.ddr.ess]

##### Switch to root 
    sudo su

##### Check the Linux distro version
    cat /proc/version

##### Edit the sysconfig network file
    nano /etc/sysconfig/network
    
##### Change the hostname and save the file
    HOSTNAME=[subddomain?].[somedomain.whatever]

##### Change the command prompt to show the full hostname
    export PS1='[\u@\H \W]\$'

##### Reboot
    reboot

##### Ping the domain name to confirm the DNS update.
    ping [somedomain.whatever]

##### Reconnect
    ssh -i [pem file] ec2-user@[somedomain.whatever]

##### Check the hostname of the ec2 instance
    hostname

##### Check the DNS domain name of the ec2 instance
    dnsdomainname

##### Switch to root
    sudo su

##### Stop Sendmail
    /etc/init.d/sendmail stop

##### Update yum
    yum update

##### Show all of the environment variables
    printenv
    declare

##### Show the current JAVA_HOME
    env | grep JAVA_HOME

##### Check the Java version
    java -version

##### See if the Java compiler is installed
    javac -version

##### Install JDK 1.8
    yum search openjdk
    yum install java-1.8.0-openjdk.x86_64

##### Create a user account for development
    useradd [your new account name]
    
##### Set the password for your development account
    passwd [your new account name]

##### Edit sudoers file
    vim /etc/sudoers

##### Add development account to sudoers file
    ## ALLOW {MYACCOUNT} TO SUDO
    [your new account name] ALL=(ALL) ALL

##### Switch to development user
    su [your new account name]
    
##### Ask who am I?
    whoami
    
##### Check the value of the home directory environment variable
    echo $HOME

##### Go home
    cd $HOME
    
##### Install git
    sudo yum install git
    
##### Make a project directory
    mkdir -p ~/git/projectdirecory

##### Initialize the directory
    cd ~/git/projectdirectory
    git init

##### Make a remote repository
    http://github.com
    
##### Add the remote repository
    git add remote origin https://github.com/[username]/[reponame].git

##### Configure git
    git config user.name [username]
    git config user.email [your email address]
    
##### Fetch from the remote repository
    git fetch

##### Make a subdirectory for Java class files
    pwd
    mkdir -p src/main/java/hello

##### Create a Gradle build file
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
##### Search yum for available Gradle packages
    yum search gradle
    yum --enablerepo="*" list available | grep gradle

##### Make a directory for Gradle
    sudo mkdir -p /opt/packages/gradle && cd $_
    pwd

##### Download Gradle
    sudo wget https://services.gradle.org/distributions/gradle-2.10-bin.zip
    
##### Unzip the Gradle package
    sudo unzip gradle-2.10-bin.zip

##### Create a symlink 
    ln -s /opt/packages/gradle/gradle-2.10/ /opt/gradle

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

##### Create a resource representation class
    vim src/main/java/hello/Greeting.java
    
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
    vim src/main/java/hello/GreetingController.java
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
    vim src/main/java/hello/Application.java
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
##### Run Gradle
    cd ~/git/projectfolder
    gradle
```
Download https://repo1.maven.org/maven2/org/springframework/boot/spring-boot-gra                                                           dle-plugin/1.3.2.RELEASE/spring-boot-gradle-plugin-1.3.2.RELEASE.pom
Download https://repo1.maven.org/maven2/org/springframework/boot/spring-boot-too                                                           ls/1.3.2.RELEASE/spring-boot-tools-1.3.2.RELEASE.pom
Download https://repo1.maven.org/maven2/org/springframework/boot/spring-boot-par                                                           ent/1.3.2.RELEASE/spring-boot-parent-1.3.2.RELEASE.pom
Download https://repo1.maven.org/maven2/org/springframework/boot/spring-boot-dep                                                           endencies/1.3.2.RELEASE/spring-boot-dependencies-1.3.2.RELEASE.pom
Download https://repo1.maven.org/maven2/org/springframework/spring-framework-bom                                                           /4.2.4.RELEASE/spring-framework-bom-4.2.4.RELEASE.pom
Download https://repo1.maven.org/maven2/org/springframework/data/spring-data-rel                                                           easetrain/Gosling-SR2A/spring-data-releasetrain-Gosling-SR2A.pom
Download https://repo1.maven.org/maven2/org/springframework/data/build/spring-da                                                           ta-build/1.7.3.RELEASE/spring-data-build-1.7.3.RELEASE.pom
Download https://repo1.maven.org/maven2/org/springframework/integration/spring-i                                                           ntegration-bom/4.2.4.RELEASE/spring-integration-bom-4.2.4.RELEASE.pom
Download https://repo1.maven.org/maven2/org/springframework/security/spring-secu                                                           rity-bom/4.0.3.RELEASE/spring-security-bom-4.0.3.RELEASE.pom
Download https://repo1.maven.org/maven2/org/springframework/boot/spring-boot-loa                                                           der-tools/1.3.2.RELEASE/spring-boot-loader-tools-1.3.2.RELEASE.pom
Download https://repo1.maven.org/maven2/io/spring/gradle/dependency-management-p                                                           lugin/0.5.4.RELEASE/dependency-management-plugin-0.5.4.RELEASE.pom
Download https://repo1.maven.org/maven2/org/springframework/spring-core/4.2.4.RE                                                           LEASE/spring-core-4.2.4.RELEASE.pom
Download https://repo1.maven.org/maven2/commons-logging/commons-logging/1.2/comm                                                           ons-logging-1.2.pom
Download https://repo1.maven.org/maven2/org/apache/commons/commons-parent/34/com                                                           mons-parent-34.pom
Download https://repo1.maven.org/maven2/org/apache/apache/13/apache-13.pom
Download https://repo1.maven.org/maven2/org/springframework/boot/spring-boot-gra                                                           dle-plugin/1.3.2.RELEASE/spring-boot-gradle-plugin-1.3.2.RELEASE.jar
Download https://repo1.maven.org/maven2/org/springframework/boot/spring-boot-loa                                                           der-tools/1.3.2.RELEASE/spring-boot-loader-tools-1.3.2.RELEASE.jar
Download https://repo1.maven.org/maven2/io/spring/gradle/dependency-management-p                                                           lugin/0.5.4.RELEASE/dependency-management-plugin-0.5.4.RELEASE.jar
Download https://repo1.maven.org/maven2/org/springframework/spring-core/4.2.4.RE                                                           LEASE/spring-core-4.2.4.RELEASE.jar
Download https://repo1.maven.org/maven2/commons-logging/commons-logging/1.2/comm                                                           ons-logging-1.2.jar
:help

Welcome to Gradle 2.10.

To run a build, run gradle <task> ...

To see a list of available tasks, run gradle tasks

To see a list of command-line options, run gradle --help

To see more detail about a task, run gradle help --task <task>

BUILD SUCCESSFUL

Total time: 12.12 secs
```
##### Install JDK version 1.8
    sudo yum search openjdk
    sudo yum install java-1.8.0-openjdk-devel.x86_64

##### Check the Java compiler version
    javac -version
Should be:
    javac 1.8.0_65    

##### Build an executable JAR
    gradle build
```
:compileJava
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

Total time: 10.118 secs
```
##### Tell Linux to use the Java interpreter in the JDK 1.8 
    sudo /usr/sbin/alternatives --config java
    
##### Check the Java version
    java -version
Should be
```
openjdk version "1.8.0_65"
OpenJDK Runtime Environment (build 1.8.0_65-b17)
OpenJDK 64-Bit Server VM (build 25.65-b01, mixed mode)
```

##### Run the JAR file:
    java -jar build/libs/gs-rest-service-0.1.0.jar

##### Install lynx
    sudo yum install lynx

##### Open another console and connect to aws
    ssh -i [pemfile.pem] ec2-user@[ec2 ip address]
    
##### Switch to your developer account
    su [developer account]

##### Go home
    cd ~
    
##### Install lynx
    sudo yum install lynx

##### 
##### 

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
{"id":1,"content":"Hello, World!"}
```
##### Provide a name query string parameter
Open a web browser and go to http://[ec2 ip address]:8080/greeting?name=User
The reply should be 
```json
{"id":2,"content":"Hello, User!"}
```
