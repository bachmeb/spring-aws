# spring-aws

Build routine to deploy a new webapp using Spring on AWS

### [References](/docs/references.md)

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
