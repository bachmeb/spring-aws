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
