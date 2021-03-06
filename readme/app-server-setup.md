
# JBOSS

# Installing Java
    sudo yum install java-11-openjdk-devel

# Installing the JBOSS JAR file
    sudo java -jar jboss-eap-7.2.0-installer.jar -console 
*Note: make sure your path to the .jar file is correct

    JBOSS Console Set-up
        Type the following in for the installer:
        0, 1, /opt/jboss-eap, 1, 0, 1, configure your admin username, configure your admin password, 1, 0, 1, n

# Creating JBOSS Service
    sudo useradd --no-create-home --shell /bin/false/ jboss-eap

# Set the JBOSS_HOME and JBOSS_USER
    sudo vim /opt/jboss-eap/bin/init.d/jboss-eap.conf
    Uncomment out JBOSS_HOME and JBOSS_USER - make sure JBOSS_USER is jboss-eap

# Copy service files
    sudo cp /opt/jboss-eap/bin/init.d/jboss-eap.conf /etc/default
    sudo cp /opt/jboss-eap/bin/init.d/jboss-eap-rhel.sh /etc/init.d

# Make scripts executable
    sudo chmod +x /etc/init.d/jboss-eap-rhel.sh

# Set the service to start automatically
    sudo chkconfig --add jboss-eap-rhel.sh
    sudo chkconfig jboss-eap-rhel.sh on

# Create the JBOSS EAP's run directory and set the ownership
    sudo mkdir /var/run/jboss-eap
    sudo chown -R jboss-eap:jboss-eap /var/run/jboss-eap/
    sudo chown -R jboss-eap:jboss-eap /opt/jboss-eap

# Install SELinux Policy Tools
    sudo yum install policycoreutils setools-console selinux-policy-devel

# Set SELinux Policy
    cd ~/
    mkdir selinux
    cd selinux
    sudo vim jboss-eap-rhel.te
Add this:

    module jboss-eap-rhel 1.0;

    require {
        type init_t;
        type var_log_t;
        class file create;
    }


    allow init_t var_log_t:file create;
    
# Create module
    sudo make -f /usr/share/selinux/devel/Makefile jboss-eap-rhel.pp
    sudo semodule -i jboss-eap-rhel.pp

# Start JBOSS
    cd ~/
    sudo systemctl start jboss-eap-rhel


---------------------------------------------------------------------------------------------
# NODE

    curl -sL https://rpm.nodesource.com/setup_13.x | sudo bash -
    sudo yum install -y nodejs

---------------------------------------------------------------------------------------------

# GRADLE

# Grabbing the Gradle distro
    wget -O ~/gradle-6.1-bin.zip https://services.gradle.org/distributions/gradle-6.1-bin.zip

# Java is already installed, assuming this comes after JBOSS

# Installing unzip
    sudo yum -y install unzip

# Creating a directory to unzip the distro
    sudo mkdir /opt/gradle
    sudo unzip -d /opt/gradle ~/gradle-6.1-bin.zip

# Update Gradle path
    sudo vim /etc/profile.d/gradle.sh
Add this:
    
    export PATH=$PATH:/opt/gradle/gradle-6.1/bin

# Set permissions on the gradle.sh
    sudo chmod 755 /etc/profile.d/gradle.sh

# Persist these changes
    logout

# Log back in with your credentials
