
# End-to-End DevSecOps Kubernetes Project



## Introduction:

In today’s rapidly evolving tech landscape, deploying applications using Kubernetes has become a crucial aspect of modern software development. This guide provides a detailed walkthrough for setting up an end-to-end Kubernetes project, covering everything from infrastructure provisioning to application deployment and monitoring.

## Prerequisites:

Before diving into the implementation, ensure you have the following in place:

1. Basic understanding of Kubernetes concepts.

1. Access to AWS or any other cloud provider for server instances.

1. A TMDB API key for accessing movie databases in your Netflix Clone application.

1. DockerHub account for pushing and pulling Docker images.

1. Gmail account for email notifications.

1. Jenkins, Kubernetes, Docker, and necessary plugins installed.

## High-Level Overview:

1. **Infrastructure Setup**: Provisioned servers for Jenkins, Monitoring, and Kubernetes nodes.

1. **Toolchain Integration**: Integrated essential tools like Jenkins, SonarQube, Trivy, Prometheus, Grafana, and OWASP Dependency-Check.

1. **Continuous Integration/Continuous Deployment (CI/CD)**: Automated workflows with Jenkins pipelines for code analysis, building Docker images, and deploying applications on Kubernetes.

1. **Security Scanning**: Implemented Trivy and OWASP Dependency-Check to scan for vulnerabilities in code and Docker images.

1. **Monitoring and Visualization**: Set up Prometheus and Grafana for real-time monitoring and visualization of both hardware and application metrics.

1. **Email Notifications**: Configured Jenkins for email alerts based on pipeline results.

You will get the Jenkinsfile and Kubernetes Manifest files along with the Dockerfile. Feel free to modify it accordingly

**Project GitHub Repo**- [https://github.com/AmanPathak-DevOps/Netflix-Clone-K8S-End-to-End-Project](https://github.com/AmanPathak-DevOps/Netflix-Clone-K8S-End-to-End-Project)

We need four servers for our today’s Project

**Jenkins Server-** On this Server, Jenkins will be installed with some other tools such as sonarqube(docker container), trivy, and kubectl.

**Monitoring Server-** This Server will be used for Monitoring where we will use Prometheus, Node Exporter, and Grafana.

**Kubernetes Master Server-** This Server will be used as the Kubernetes Master Cluster Node which will deploy the applications on worker nodes.

**Kubernetes Worker Server-** This Server will be used as the Kubernetes Worker Node on which the application will be deployed by the master node.

Let’s create the following instances.

**Jenkins Server**

Click on **Launch Instances.**

![](https://cdn-images-1.medium.com/max/3200/0*ljWXDG-bi_UTaVGT)

Provide the name of your Jenkins instance, and select the Ubuntu OS 22.04 version.

![](https://cdn-images-1.medium.com/max/3200/0*IavAgPMx_E1n3V9r)

We need to configure multiple things on the Jenkins instance. So, select the t2.large instance type, provide the key or you can create if you want.

Keep the networking things as it is. But make sure to open all inbound and outbound traffic in the selected security groups.

![](https://cdn-images-1.medium.com/max/3200/0*HiZ4e0xUJ2uz6XGH)

Increase the storage capacity for Jenkins Instance from 8GB to 35GB and click on **Launch Instance.**

![](https://cdn-images-1.medium.com/max/3200/0*_GxPQ1Izbh4jaHI-)

**Monitoring Server**

Provide the name of your Monitoring Instance, and select the Ubuntu 22.04 OS.

![](https://cdn-images-1.medium.com/max/3200/0*_lnD8Q7W66-gCT9x)

We need to configure the monitoring tools on this instance which needs a minimum of 4GB RAM. So, select the t2.medium instance type, provide the key or you can create if you want.

Keep the networking things as it is. But make sure to open all inbound and outbound traffic in the selected security groups.

![](https://cdn-images-1.medium.com/max/3200/0*M3OzpolZj3NK7p3d)

Increase the storage capacity for Jenkins Instance from 8GB to 15GB and click on **Launch Instance.**

![](https://cdn-images-1.medium.com/max/3200/0*irnhtKbVp3CNpnL8)

**Kubernetes Master & Worker Node**

We have to create two Kubernetes Nodes which need at least 2 CPUs.

Provide the name of your Kubernetes Master Instance, and select the Ubuntu 22.04 OS.

In the Number of Instances, replace 1 with 2 because we need two Kubernetes Nodes.

![](https://cdn-images-1.medium.com/max/3200/0*v7zIATGGQNcVjeCP)

Select the t2.medium instance type, provide the key or you can create if you want.

Keep the networking things as it is. But make sure to open all inbound and outbound traffic in the selected security groups then keep the rest of the things as it is and click on **Launch Instance.**

![](https://cdn-images-1.medium.com/max/3200/0*4hIzgXdiIpQNgv_R)

Rename the Kubernetes Servers and all four servers will look like the below snippet.

![](https://cdn-images-1.medium.com/max/3200/0*4xwdATSGQGbFKdPG)

Log in to the Jenkins Server

![](https://cdn-images-1.medium.com/max/3200/0*VwTLoG1ilD_gGD4_)

Download Open JDK and Jenkins

    # Intsalling Java
    sudo apt update -y
    sudo apt install openjdk-11-jre -y
    java --version
    
    # Installing Jenkins
    curl -fsSL https://pkg.jenkins.io/debian/jenkins.io-2023.key | sudo tee \
      /usr/share/keyrings/jenkins-keyring.asc > /dev/null
    echo deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc] \
      https://pkg.jenkins.io/debian binary/ | sudo tee \
      /etc/apt/sources.list.d/jenkins.list > /dev/null
    sudo apt-get update -y
    sudo apt-get install jenkins -y

![](https://cdn-images-1.medium.com/max/3200/0*dGEFk7WX3_GIExLm)

Check the status of the Jenkins server

![](https://cdn-images-1.medium.com/max/3200/0*nAstVZU4HcVeA-_x)

Copy your Jenkins Server Public IP and paste it into your favorite browser with port number 8080.

![](https://cdn-images-1.medium.com/max/3200/0*j6QmEgcN1RhXg9rj)

Run the command on your Jenkins server

    sudo cat /var/lib/jenkins/secrets/initialAdminPassword

Copy the output and paste it into your above snippet text field and click on Continue.

![](https://cdn-images-1.medium.com/max/3200/0*bV1Ghc7aQbT4nG1X)

Click on the **Install suggested plugins**

![](https://cdn-images-1.medium.com/max/3200/0*6O-pHg_VmajH7_dC)

Click on the **Skip and continue as admin**

![](https://cdn-images-1.medium.com/max/3200/0*Y4FUkKf7uNfsWX62)

Click on **Save and Finish**

![](https://cdn-images-1.medium.com/max/3200/0*rSrf_55fSz3yi0II)

Install Docker and configure on the **Jenkins Server**

    sudo apt update
    sudo apt install docker.io -y
    sudo usermod -aG docker jenkins
    sudo usermod -aG docker ubuntu
    sudo systemctl restart docker
    sudo chmod 777 /var/run/docker.sock

![](https://cdn-images-1.medium.com/max/3200/0*K0SyIwqh6r4twKcW)

Install Sonarqube on your **Jenkins Server**

We will use a docker container for Sonarqube

    docker run -d — name sonar -p 9000:9000 sonarqube:lts-community

![](https://cdn-images-1.medium.com/max/3200/0*zwjhEIeiGiuQqvPh)

Now, copy your Public IP of Jenkins Server and add 9000 Port on your browser.

The username and password will be admin

![](https://cdn-images-1.medium.com/max/3200/0*PECNuwWP66nVh3EV)

Reset the password and click on **Update**

![](https://cdn-images-1.medium.com/max/3200/0*UJzC-Dv1BZ8K21W4)

You will see your Sonarqube Server in the below snippet.

![](https://cdn-images-1.medium.com/max/3200/0*88ff1wNc5cASEnVA)

Install the Trivy tool on the **Jenkins Server**

    sudo apt-get install wget apt-transport-https gnupg lsb-release
    wget -qO - https://aquasecurity.github.io/trivy-repo/deb/public.key | sudo apt-key add -
    echo deb https://aquasecurity.github.io/trivy-repo/deb $(lsb_release -sc) main | sudo tee -a /etc/apt/sources.list.d/trivy.list
    sudo apt-get update
    sudo apt-get install trivy

![](https://cdn-images-1.medium.com/max/3200/0*5Ivm9VrzWGQVgpqo)

Install and Configure the Prometheus, Node Exporter, and Grafana on the **Monitoring Server**

Login to the **Monitoring Server**

![](https://cdn-images-1.medium.com/max/3200/0*klrP1kVMp1ZAh8lo)

Create Prometheus user

    sudo useradd \
     - system \
     - no-create-home \
     - shell /bin/false prometheus

![](https://cdn-images-1.medium.com/max/3200/0*g61upxXz7KeKTaIE)

Download the Prometheus file on the **Monitoring Server**

    wget https://github.com/prometheus/prometheus/releases/download/v2.49.0-rc.1/prometheus-2.49.0-rc.1.linux-amd64.tar.gz

![](https://cdn-images-1.medium.com/max/3200/0*oTo6GfEJLuGCubOi)

Untar the Prometheus downloaded package

    tar -xvf prometheus-2.49.0-rc.1.linux-amd64.tar.gz

![](https://cdn-images-1.medium.com/max/3200/0*yLFJJxek1ZC09bpC)

Create two directories /data and /etc/prometheus to configure the Prometheus

    sudo mkdir -p /data /etc/prometheus
    Now, enter into the prometheus package file that you have untar in the earlier step.
    cd prometheus-2.49.0-rc.1.linux-amd64/

![](https://cdn-images-1.medium.com/max/3200/0*REkXffXTd-0GMcnZ)

Move the prometheus and promtool files package in /usr/local/bin

    sudo mv prometheus promtool /usr/local/bin/

![](https://cdn-images-1.medium.com/max/3200/0*HyNcnLSzBW6IBr5T)

Move the console and console_libraries and prometheus.yml in the /etc/prometheus

    sudo mv consoles console_libraries/ prometheus.yml /etc/prometheus/

![](https://cdn-images-1.medium.com/max/3200/0*YdZwvmApIux38URG)

Provide the permissions to prometheus user

    sudo chown -R prometheus:prometheus /etc/prometheus/ /data/

![](https://cdn-images-1.medium.com/max/3200/0*WSPiLCoYK9lIBood)

Check and validate the Prometheus

    prometheus --version

![](https://cdn-images-1.medium.com/max/3200/0*PDVmVLxSsQ3i1beq)

Create a systemd configuration file for prometheus

Edit the file /etc/systemd/system/prometheus.service

    sudo vim /etc/systemd/system/prometheus.service

and paste the below configurations in your prometheus.service configuration file and save it

    [Unit]
    Description=Prometheus
    Wants=network-online.target
    After=network-online.target
    StartLimitIntervalSec=500
    StartLimitBurst=5
    [Service]
    User=prometheus
    Group=prometheus
    Type=simple
    Restart=on-failure
    RestartSec=5s
    ExecStart=/usr/local/bin/prometheus \
     - config.file=/etc/prometheus/prometheus.yml \
     - storage.tsdb.path=/data \
     - web.console.templates=/etc/prometheus/consoles \
     - web.console.libraries=/etc/prometheus/console_libraries \
     - web.listen-address=0.0.0.0:9090 \
     - web.enable-lifecycle
    [Install]
    WantedBy=multi-user.target

![](https://cdn-images-1.medium.com/max/3200/0*WW8OmHVHEZ9V7LsT)

Once you write the systemd configuration file for Prometheus, then enable it and start the Prometheus service.

    sudo systemctl enable prometheus.service
    sudo systemctl start prometheus.service
    systemctl status prometheus.service

![](https://cdn-images-1.medium.com/max/3200/0*69KdSuxIIWkvibTo)

Once the Prometheus service is up and running then, copy the public IP of your **Monitoring Server** and paste it into your favorite browser with a 9090 port.

![](https://cdn-images-1.medium.com/max/3200/0*eLZlyQ3LGYr7ZHDJ)

Now, we have to install a node exporter to visualize the machine or hardware level data such as CPU, RAM, etc on our Grafana dashboard.

To do that, we have to create a user for it.

    sudo useradd \
     - system \
     - no-create-home \
     - shell /bin/false node_exporter

![](https://cdn-images-1.medium.com/max/3200/0*pmXquTcZgumiT3Gl)

    Download the node exporter package
    wget https://github.com/prometheus/node_exporter/releases/download/v1.7.0/node_exporter-1.7.0.linux-amd64.tar.gz

![](https://cdn-images-1.medium.com/max/3200/0*QeCD46qE2xrHGR5C)

Untar the node exporter package file and move the node_exporter directory to the /usr/local/bin directory

    tar -xvf node_exporter-1.7.0.linux-amd64.tar.gz
    sudo mv node_exporter-1.7.0.linux-amd64/node_exporter /usr/local/bin/

![](https://cdn-images-1.medium.com/max/3200/0*Qw4fn9OCYOd_x70B)

Validate the version of the node exporter

    node_exporter --version

![](https://cdn-images-1.medium.com/max/3200/0*u4QE8y7YWG48uZCO)

Create the systemd configuration file for node exporter.

Edit the file

    sudo vim /etc/systemd/system/node_exporter.service

Copy the below configurations and paste them into the /etc/systemd/system/node_exporter.service file.

    [Unit]
    Description=Node Exporter
    Wants=network-online.target
    After=network-online.target
    StartLimitIntervalSec=500
    StartLimitBurst=5
    [Service]
    User=node_exporter
    Group=node_exporter
    Type=simple
    Restart=on-failure
    RestartSec=5s
    ExecStart=/usr/local/bin/node_exporter \
     - collector.logind
    [Install]
    WantedBy=multi-user.target

![](https://cdn-images-1.medium.com/max/3200/0*ozHg5sTLyV0vAjB0)

Enable the node exporter systemd configuration file and start it.

    sudo systemctl enable node_exporter
    sudo systemctl enable node_exporter
    systemctl status node_exporter.service

![](https://cdn-images-1.medium.com/max/3200/0*SGVdDCsTtP781KyR)

Now, we have to add a node exporter to our Prometheus target section. So, we will be able to monitor our server.

edit the file

    sudo vim /etc/prometheus/prometheus.yml

Copy the content in the file

      - job_name: "node_exporter"
        static_configs:
          - targets: ["localhost:9100"]

![](https://cdn-images-1.medium.com/max/3200/0*qw5IEs9nRY-YgdZA)

After saving the file, validate the changes that you have made using promtool.

    promtool check config /etc/prometheus/prometheus.yml

![](https://cdn-images-1.medium.com/max/3200/0*wlqsk1xZTACbELM8)

If your changes have been validated then, push the changes to the Prometheus server.

    curl -X POST http://localhost:9090/-/reload

![](https://cdn-images-1.medium.com/max/3200/0*LgmoZgKhvmOyomaL)

Now, go to your Prometheus server and this time, you will see one more target section as node_exporter which should be up and running.

![](https://cdn-images-1.medium.com/max/3200/0*iH2F5PQ_qEXNJnyj)

Now, install the Grafana tool to visualize all the data that is coming with the help of Prometheus.

    sudo apt-get install -y apt-transport-https software-properties-common wget
    sudo mkdir -p /etc/apt/keyrings/
    wget -q -O - https://apt.grafana.com/gpg.key | gpg - dearmor | sudo tee /etc/apt/keyrings/grafana.gpg > /dev/null
    echo "deb [signed-by=/etc/apt/keyrings/grafana.gpg] https://apt.grafana.com stable main" | sudo tee -a /etc/apt/sources.list.d/grafana.list
    echo "deb [signed-by=/etc/apt/keyrings/grafana.gpg] https://apt.grafana.com beta main" | sudo tee -a /etc/apt/sources.list.d/grafana.list
    sudo apt-get update

![](https://cdn-images-1.medium.com/max/3200/0*UdRBb9H2Pr1CYGsN)

Install the Grafana

    sudo apt-get install grafana

![](https://cdn-images-1.medium.com/max/3200/0*f-IGofHxCibltuz1)

Enable and start the Grafana Service

    sudo systemctl enable grafana-server.service
    sudo systemctl start grafana-server.service
    sudo systemctl status grafana-server.service

![](https://cdn-images-1.medium.com/max/3200/0*Cpdv9LTczYcBCfkD)

To access the Grafana dashboard, copy the public IP address of the **Monitoring Server **and paste it into your favorite browser with port 3000

username and password will be admin

![](https://cdn-images-1.medium.com/max/3200/0*HSBKIR4nW7lhvHwq)

Reset the password

![](https://cdn-images-1.medium.com/max/3200/0*4eYNpBKliwqjRZNI)

Click on **Data sources**

![](https://cdn-images-1.medium.com/max/3200/0*c8U8aAzAPuEIM4q8)

Select the **Prometheus**

![](https://cdn-images-1.medium.com/max/3200/0*KnDl6Adh7l4ahT4o)

Provide the **Monitoring Server Public IP **with port 9090 to monitor the **Monitoring Server.**

![](https://cdn-images-1.medium.com/max/3200/0*gLkEoegRTHSj5sRs)

Click on **Save and test.**

![](https://cdn-images-1.medium.com/max/3200/0*zddIflHAA2Bujac_)

Go to the dashboard section of Grafana and click on the Import** dashboard.**

![](https://cdn-images-1.medium.com/max/3200/0*YNlLRjkk78bIFENI)

Add 1860 for the node exporter dashboard and click on **Load**.

![](https://cdn-images-1.medium.com/max/3200/0*G_uSe_R27tj7US3o)

Then, select the Prometheus from the drop down menu and click on **Import**

![](https://cdn-images-1.medium.com/max/3200/0*F3GL6AId71sdCJQn)

The dashboard will look like this

![](https://cdn-images-1.medium.com/max/3200/0*PBeOJN2em9lGIesa)

Now, we have to monitor our **Jenkins Server **as well.

For that, we need to install the Prometheus metric plugin on our Jenkins.

Go to **Manage Jenkins** -> Plugin search for Prometheus metrics install it and restart your Jenkins.

![](https://cdn-images-1.medium.com/max/3200/0*AEJ7jr3iQEVOxRWb)

Edit the /etc/prometheus/prometheus.yml file

    sudo vim /etc/prometheus/prometheus.yml

    - job_name: "jenkins"
        static_configs:
          - targets: ["<jenkins-server-public-ip>:8080"]

![](https://cdn-images-1.medium.com/max/3200/0*A-XtsFObUNPkrsCs)

Once you add the Jenkins job, validate the Prometheus config file whether it is correct or not by running the below command.

    promtool check config /etc/prometheus/prometheus.yml

Now, push the new changes on the Prometheus server

    curl -X POST http://localhost:9090/-/reload

![](https://cdn-images-1.medium.com/max/3200/0*ZsfdagAaNPIURz-T)

Copy the public IP of your **Monitoring Server** and paste on your favorite browser with a 9090 port with /target. You will see the targets that you have added in the /etc/prometheus/prometheus.yml file.

![](https://cdn-images-1.medium.com/max/3200/0*HeQA4Z9kB8vSL2g4)

To add the Jenkins Dashboard on your Grafana server.

Click on **New -> Import.**

![](https://cdn-images-1.medium.com/max/3200/0*ADwMUfrySoYIgKIr)

Provide the 9964 to Load the dashboard.

![](https://cdn-images-1.medium.com/max/3200/0*JNvfDH2DcCQp756n)

Select the default Prometheus from the drop-down menu and click on **Import.**

![](https://cdn-images-1.medium.com/max/3200/0*DNwQRwgaEXGYCLCW)

You will see your Jenkins Monitoring dashboard in the below snippet.

![](https://cdn-images-1.medium.com/max/3200/0*vRZAd2wKmhjWjSiS)

Now, we have to integrate Email Alert. So, if our Jenkins pipeline will succeed or fail we will get a notification alert on our email.

To do that, we need to install the Jenkins Plugin, whose name is **Email Extension Template**.

Manage Jenkins -> Plugins and install the **Email Extension Template **plugin.

![](https://cdn-images-1.medium.com/max/3200/0*Gp3vTWgxIRgzujMP)

After installing the plugin, go to your email ID and click on Manage account and you will see what looks like the below snippet.

![](https://cdn-images-1.medium.com/max/3200/0*0NaTJplQJqdKIEwg)

In the **Security **section, search for **App passwords **and click on it.

![](https://cdn-images-1.medium.com/max/3200/0*2x-t1miJZjdnEh-N)

Gmail will prompt you for the password. Provide the password then you have to provide the name of your app where you are integrating email service.

![](https://cdn-images-1.medium.com/max/3200/0*EgtCqmTZVlVatfuy)

You will get your password below. Copy the password and keep it secure somewhere.

![](https://cdn-images-1.medium.com/max/2282/0*jWhQmNQb6S_adx90)

Add your email ID and the password that you have generated in the previous step.

Go to **Manage Jenkins -> Credentials.**

Click on **(global)**.

![](https://cdn-images-1.medium.com/max/3200/0*qh0sFHqogLmz125G)

Click on **Add credentials**

![](https://cdn-images-1.medium.com/max/3200/0*xdZRkZReBAzMHj0-)

Select the Username with password in Kind.

Provide your mail ID and generated password then provide the ID as mail to call both credentials.

![](https://cdn-images-1.medium.com/max/3200/0*3PnlKa2kcXNVnQiv)

You can see we have added the credentials for the mail.

![](https://cdn-images-1.medium.com/max/3200/0*5d4sgYf0wOuz91X7)

Now, we have to configure our mail for the alerts.

Go to Jenkins -> **Manage Jenkins **-> **System**

Search for Extend E-mail Notification.

Provide the smtp.gmail.com in the **SMTP server **and 465 in the **SMTP port.**

![](https://cdn-images-1.medium.com/max/3200/0*MoAcJbNX2I1zvTvE)

Then, On the same page Search for Extend E-mail Notification.

Provide the smtp.gmail.com in the **SMTP server **and 465 in the **SMTP port.**

Select **Use SMTP Authentication **and provide the Gmail ID and its password in the Username and password.

To validate whether Jenkins can send the emails to you or not, check the **Test configuration by sending a test e-mail.**

![](https://cdn-images-1.medium.com/max/3200/0*7Fs-u_sQolwzrowO)

You can see below for the reference.

![](https://cdn-images-1.medium.com/max/2000/0*Uznr1jz70hTwsa35)

Now, we will set up our Jenkins Pipeline. But there are some plugins required to work with them.

Download the following plugins

Eclipse Temurin installer

SonarQube Scanner

NodeJS

![](https://cdn-images-1.medium.com/max/3200/0*1-haMBx4x14cluSv)

Now, configure the plugins

Go to **Manage Jenkins -> Tools**

Click on Add JDK and provide the following things below

![](https://cdn-images-1.medium.com/max/3200/0*NVi1IeffQS7C8uGK)

Click on Add NodeJS and provide the following things below

![](https://cdn-images-1.medium.com/max/3200/0*8vNrWER04DFO1XBG)

Now, we will configure Sonarqube

To access the sonarqube, copy the Jenkins Server public IP with port number 9000

Then, click **Security **and click on **Users.**

![](https://cdn-images-1.medium.com/max/3200/0*cTuCvMOuZb7njZaf)

Click on the highlighted blue box on the right to generate the token.

![](https://cdn-images-1.medium.com/max/3200/0*vr-9GWm9bFRcFnd5)

Now provide the name of your token and click on **Generate.**

![](https://cdn-images-1.medium.com/max/3200/0*eQi2TbbHB6tLXNj-)

Copy the generated token and keep it somewhere.

![](https://cdn-images-1.medium.com/max/3200/0*H5WQmhd0QbGZgYi9)

Now, add the token to your Jenkins credentials

Go to **Manage Jenkins -> Credentials.**

Select the Secret text in Kind.

Provide your token then provide the ID as sonar-token to call the credentials.

![](https://cdn-images-1.medium.com/max/3200/0*Q1Fg4CgCyvdNZ04A)

Go to **Manage Jenkins -> System**

Click on **Add Sonarqube**

![](https://cdn-images-1.medium.com/max/3200/0*YNdYW-I4NmundFV6)

Provide the name sonar-server with the Server URL and select the credentials that we have added.

![](https://cdn-images-1.medium.com/max/3200/0*4_Bz3capP2tyRyCh)

Go to **Manage Jenkins -> Tools**

Find Sonarqube Scanner and click on **Add**

![](https://cdn-images-1.medium.com/max/3200/0*kQrYv1XUv3Fb9-Rn)

Provide the name sonar-server and select the latest version of Sonarqube.

![](https://cdn-images-1.medium.com/max/3200/0*qAmjmAWSYebLSp17)

To create a webhook, click on **Configuration **and select **Webhooks.**

![](https://cdn-images-1.medium.com/max/3200/0*D-iWit4S02oMCEv-)

Click on **Create.**

![](https://cdn-images-1.medium.com/max/3200/0*qhQszIC1dYywthxp)

Provide the name and Jenkins URL like below and click on **Create.**

![](https://cdn-images-1.medium.com/max/3200/0*26idM6DCOddjA88G)

The Webhook will be showing the below snippet.

![](https://cdn-images-1.medium.com/max/3200/0*AUit5gD_wYRq3GEX)

To create a project, click on **Manually.**

![](https://cdn-images-1.medium.com/max/3200/0*Az20K6tn3elsPbjM)

Provide the name of your project and click on **Set up.**

![](https://cdn-images-1.medium.com/max/3200/0*PZ9N3tC7WI-kAR7S)

Select the existing token and click on **continue.**

![](https://cdn-images-1.medium.com/max/3200/0*RiN5RYOfQPGrjY8y)

Select the Other as your build and Linux as OS.

![](https://cdn-images-1.medium.com/max/3200/0*4Xt2tPdkn9rRvAv0)

Now, we will create the Jenkins Pipeline

Click on **Create item.**

![](https://cdn-images-1.medium.com/max/3200/0*vRNS7zr8_ImDvphY)

Provide the name of your Jenkins Pipeline and select **Pipeline.**

![](https://cdn-images-1.medium.com/max/3200/0*rKSLJ0R4B7IDkZmY)

Currently, we are just creating a pipeline for Sonarqube analysis of the code, quality gate for Sonarqube, and installing the dependencies.

In the post-build, we have added email alerts for the success or failure of the pipeline.

    pipeline{
        agent any
        tools{
            jdk 'jdk'
            nodejs 'nodejs'
        }
        environment {
            SCANNER_HOME=tool 'sonar-server'
        }
        stages {
            stage('Workspace Cleaning'){
                steps{
                    cleanWs()
                }
            }
            stage('Checkout from Git'){
                steps{
                    git branch: 'master', url: 'https://github.com/AmanPathak-DevOps/Netflix-Clone-K8S-End-to-End-Project.git'
                }
            }
            stage("Sonarqube Analysis"){
                steps{
                    withSonarQubeEnv('sonar-server') {
                        sh ''' $SCANNER_HOME/bin/sonar-scanner -Dsonar.projectName=Netflix \
                        -Dsonar.projectKey=Netflix \
                        '''
                    }
                }
            }
            stage("Quality Gate"){
               steps {
                    script {
                        waitForQualityGate abortPipeline: false, credentialsId: 'sonar-token' 
                    }
                } 
            }
            stage('Install Dependencies') {
                steps {
                    sh "npm install"
                }
            }  
        }
        post {
         always {
            emailext attachLog: true,
                subject: "'${currentBuild.result}'",
                body: "Project: ${env.JOB_NAME}<br/>" +
                    "Build Number: ${env.BUILD_NUMBER}<br/>" +
                    "URL: ${env.BUILD_URL}<br/>",
                to: 'aman07pathak@gmail.com',
                attachmentsPattern: 'trivyfs.txt,trivyimage.txt'
            }
        }
    }

![](https://cdn-images-1.medium.com/max/3200/0*eZ3v6M-uaDhZmfd_)

Click on build pipeline and after getting the success of the pipeline.

You will see the Sonarqube code quality analysis which will look like the below snippet.

![](https://cdn-images-1.medium.com/max/3200/0*KZCBDeDaxQuFUewf)

Now, we have to add one more tool for our application named OWASP Dependency-check.

Go to **Manage Jenkins -> Plugins**

Search for OWASP Dependency-Check and install it.

![](https://cdn-images-1.medium.com/max/3200/0*0HyBKRMe5ddRGWgv)

After installing, make sure to configure the OWASP.

Provide the name select the latest version of OWASP and click on **Save.**

![](https://cdn-images-1.medium.com/max/3200/0*jrE3zjN_ROM04odj)

Now, add the OWASP dependency check stage in the Jenkins pipeline and click on **Save.**

    stage('OWASP DP SCAN') {
                steps {
                    dependencyCheck additionalArguments: '--scan ./ --disableYarnAudit --disableNodeAudit', odcInstallation: 'owasp-dp-check'
                    dependencyCheckPublisher pattern: '**/dependency-check-report.xml'
                }
            }
    stage('TRIVY FS SCAN') {
                steps {
                    sh "trivy fs . > trivyfs.txt"
                }
            }

![](https://cdn-images-1.medium.com/max/3200/0*OSfIU7IsgUKhmsEF)

Now, click on **Build Now.**

![](https://cdn-images-1.medium.com/max/3200/0*ooHv1_rHr-iQYmLD)

Once your pipeline is successful. Then, scroll down and you will see a dependency check. 
Click on it and you will see the output like the below snippet.

![](https://cdn-images-1.medium.com/max/3200/0*S9XpaB-ZomllxygT)

Now, we have to build our Docker Image and push it to DockerHub

To do that, we need to configure the following things.

Go to **Manage Jenkins -> Credentials**

Add Docker Credentials to your Jenkins

![](https://cdn-images-1.medium.com/max/3200/0*9ZBFiKmjLp1P6ZSD)

Add your credentials and click on **Create.**

![](https://cdn-images-1.medium.com/max/3200/0*R2s4LZzjKowLjzBO)

Install the following Docker plugins on your Jenkins

    Docker
    Docker Commons
    Docker Pipeline
    Docker API
    docker-build-step

![](https://cdn-images-1.medium.com/max/3200/0*lvIGsbJ-otOO716Y)

Restart your Jenkins

![](https://cdn-images-1.medium.com/max/3200/0*0-F7VPuQNo17qC_Z)

Configure the tool in Jenkins

Go to **Manage Jenkins** -> Tools and provide the below details.

![](https://cdn-images-1.medium.com/max/3200/0*IhNaDlbUnGFnlRFv)

Our application is Netflix Clone. So we need some movie databases on our application.

For that, we have one application that will provide the API. So, we can use the API to get the movies on our application.

TMDB is one of them

Go to this link [https://www.themoviedb.org/](https://www.themoviedb.org/)

Click on **Join TMDB**

![](https://cdn-images-1.medium.com/max/3200/0*BFt1z7f1xqSirxrt)

Enter the details and click on **SignUp**

![](https://cdn-images-1.medium.com/max/3200/0*51oBLh5fw71IMP0e)

Once you sign up, you will get a confirmation email on your account. Confirm it.

Log in to your TMDB account and go to the settings.

![](https://cdn-images-1.medium.com/max/3200/0*kxWiK46wzrD83Gpu)

Go to the **API **section.

![](https://cdn-images-1.medium.com/max/3200/0*FoaEAFvQYSL1QhVM)

Click on **Create **to generate API

![](https://cdn-images-1.medium.com/max/3200/0*3afDadk8jHPaRvfK)

Select **Developer.**

![](https://cdn-images-1.medium.com/max/3200/0*2iFIRMR4nP-GKUQs)

**Accept **the Terms & Conditions.

![](https://cdn-images-1.medium.com/max/2910/0*aq5LZpapiAJzPIqg)

Provide the basic details and click on **Submit.**

![](https://cdn-images-1.medium.com/max/3200/0*kQarBUstYYW4qxxa)

After clicking on **Submit**. You will get your **API.** Copy the API and keep it somewhere.

![](https://cdn-images-1.medium.com/max/3200/0*-TvV99Fauzlq59Pu)

Now, we have to configure our Docker images where we will build our image with the help of new code and then, push it to DockerHub.

After pushing the image, we will scan our DockerHub Image to find the vulnerabilities in the image.

Make sure to replace the API with your API and if you are pushing Dockerfile on your Dockerhub account then, replace my username of the Dockerhub with yours.

![](https://cdn-images-1.medium.com/max/3200/0*SsaDUI7-LJRh57MM)

Click on **Build**

![](https://cdn-images-1.medium.com/max/3200/0*-mmKRz7NqHcaF4MS)

As you can see Our Pipeline is successful.

![](https://cdn-images-1.medium.com/max/3200/0*F-Tu0hUEB12lqRSc)

Now, validate whether the docker image has been pushed to DockerHub or not.

Log in to your Dockerhub account.

As you can see in the below screenshot, Our Docker image is present on Docker Hub.

![](https://cdn-images-1.medium.com/max/3200/0*WrHAUAILtBMyVhfN)

Now, we have to deploy our application using Kubernetes.

To do that, we need to install kubectl on the Jenkins server.

    sudo apt update
    sudo apt install curl
    curl -LO https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl
    sudo install -o root -g root -m 0755 kubectl /usr/local/bin/kubectl
    kubectl version --client

![](https://cdn-images-1.medium.com/max/3200/0*nuW-uuhNitMOiIjz)

As you know, we have two Kubernetes Nodes of which one is the Master and the other one is the Worker Node.

Login to your both Kubernetes Master and Worker Nodes

**Master Node**

![](https://cdn-images-1.medium.com/max/3200/0*fH_hBvXug0EZfgvk)

**Worker Node**

![](https://cdn-images-1.medium.com/max/3200/0*nHRSnRD3MxWrNpgy)

Add the hostname to your Kubernetes master node

    sudo hostnamectl set-hostname K8s-Master

![](https://cdn-images-1.medium.com/max/3200/0*CMTpTH8i4U4gFlwv)

Add the hostname to your Kubernetes worker node

    sudo hostnamectl set-hostname K8s-Worker

![](https://cdn-images-1.medium.com/max/3200/0*v0klh_JPLYpieFNR)

Run the below commands on the both Master and worker Nodes.

    sudo su
    swapoff -a; sed -i '/swap/d' /etc/fstab
    cat <<EOF | sudo tee /etc/modules-load.d/k8s.conf
    overlay
    br_netfilter
    EOF
    sudo modprobe overlay
    sudo modprobe br_netfilter
    # sysctl params required by setup, params persist across reboots
    cat <<EOF | sudo tee /etc/sysctl.d/k8s.conf
    net.bridge.bridge-nf-call-iptables = 1
    net.bridge.bridge-nf-call-ip6tables = 1
    net.ipv4.ip_forward = 1
    EOF
    # Apply sysctl params without reboot
    sudo sysctl - system
    apt update
    sudo apt-get install -y apt-transport-https ca-certificates curl
    curl -fsSL https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo gpg - dearmor -o /etc/apt/keyrings/kubernetes-archive-keyring.gpg
    echo "deb [signed-by=/etc/apt/keyrings/kubernetes-archive-keyring.gpg] https://apt.kubernetes.io/ kubernetes-xenial main" | sudo tee /etc/apt/sources.list.d/kubernetes.list
    apt update
    apt-get install -y kubelet kubeadm kubectl kubernetes-cni
    apt install docker.io -y
    sudo mkdir /etc/containerd
    sudo sh -c "containerd config default > /etc/containerd/config.toml"
    sudo sed -i 's/ SystemdCgroup = false/ SystemdCgroup = true/' /etc/containerd/config.toml
    systemctl restart containerd.service
    systemctl restart kubelet.service
    systemctl enable kubelet.service

Now, run the following commands **Only on the Master Node,** and then you will get the command that is highlighted in the below snippet.

    kubeadm config images pull
    kubeadm init

![](https://cdn-images-1.medium.com/max/3200/0*lyuG8YKdfDBcF_Tq)

Exit from the root user and run the below commands

    mkdir -p $HOME/.kube
    sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
    sudo chown $(id -u):$(id -g) $HOME/.kube/config

![](https://cdn-images-1.medium.com/max/3200/0*4COYOQO-IF_YklM2)

**Run on the Worker Node**

Run the below command as a root user

    kubeadm join 172.31.59.154:6443 - token deq9nl.y34go2ziii0fu8c1 \
     - discovery-token-ca-cert-hash sha256:e93c56bd59b175b81845a671a82ffd1839e42272d922f9c43ca8d8f6d145ce02

![](https://cdn-images-1.medium.com/max/3200/0*ju3cFb9QwsHsmoE8)

Both nodes are not ready because the network plugin is not installed on the master node

![](https://cdn-images-1.medium.com/max/3200/0*_RQaZI1Me4uDTdCK)

**Only on the Master Node**

Run the below command to install the network plugin on the Master node

    kubectl apply -f https://raw.githubusercontent.com/projectcalico/calico/v3.25.0/manifests/calico.yaml

![](https://cdn-images-1.medium.com/max/3200/0*E0fWlC-ga_PDo6ba)

Both nodes are ready.

![](https://cdn-images-1.medium.com/max/3200/0*eZwFS_T4lGFvnfA5)

Install the following Kubernetes Plugins on your Jenkins

    Kubernetes
    Kubernetes Credentials
    Kubernetes Client API
    Kubernetes CLI
    Kubernetes Credential Provider

![](https://cdn-images-1.medium.com/max/3200/0*M6NvdF-7vQ3glbDm)

![](https://cdn-images-1.medium.com/max/3200/0*u3FP1rCrgL9YIbM2)

Now, we will set Kubernetes Monitoring for both Master and worker Nodes

Run the below command on both Kubernetes Nodes

    sudo useradd \
    --system \
    --no-create-home \
    --shell /bin/false prometheus

![](https://cdn-images-1.medium.com/max/3200/0*z3ThZHHLyOLVAx8x)

Download the node exporter package on both Kubernetes Nodes and Untar the node exporter package file and move the node_exporter directory to the /usr/local/bin directory

    wget https://github.com/prometheus/node_exporter/releases/download/v1.7.0/node_exporter-1.7.0.linux-amd64.tar.gz
    tar -xvf node_exporter-1.7.0.linux-amd64.tar.gz
    sudo mv node_exporter-1.7.0.linux-amd64/node_exporter /usr/local/bin/

![](https://cdn-images-1.medium.com/max/3200/0*Z-4kw3EoojQj42sX)

Create the systemd configuration file for node exporter.

Edit the file

    sudo vim /etc/systemd/system/node_exporter.service

Copy the below configurations and paste them into the /etc/systemd/system/node_exporter.service file.

    [Unit]
    Description=Node Exporter
    Wants=network-online.target
    After=network-online.target
    StartLimitIntervalSec=500
    StartLimitBurst=5
    [Service]
    User=node_exporter
    Group=node_exporter
    Type=simple
    Restart=on-failure
    RestartSec=5s
    ExecStart=/usr/local/bin/node_exporter \
     - collector.logind
    [Install]
    WantedBy=multi-user.target

![](https://cdn-images-1.medium.com/max/3200/0*O8Toy6Vxwqa00CKR)

Enable the node exporter systemd configuration file and start it.

    sudo systemctl enable node_exporter
    sudo systemctl enable node_exporter
    systemctl status node_exporter.service

![](https://cdn-images-1.medium.com/max/3200/0*aHgpiGpAfDhVamZT)

Now, we have to add a node exporter to our Prometheus target section. So, we will be able to monitor both Kubernetes Servers.

edit the file

    sudo vim /etc/prometheus/prometheus.yml

Add both job names(Master & Worker nodes) with their respective public.

![](https://cdn-images-1.medium.com/max/3200/0*2fModr9eBeB6wKRb)

After saving the file, validate the changes that you have made using promtool.

    promtool check config /etc/prometheus/prometheus.yml

If your changes have been validated then, push the changes to the Prometheus server.

    curl -X POST http://localhost:9090/-/reload

![](https://cdn-images-1.medium.com/max/3200/0*YWksH4zQpiKLX9nD)

As you know, Jenkins will deploy our application on the Kubernetes Cluster. To do that, Jenkins must have the access keys or something to connect with the master node.

To do that copy the content inside .kube/config on Kubernetes Master node.

    cat .kube/config

![](https://cdn-images-1.medium.com/max/3200/0*DNQLDllJuOCgwTMp)

Save the file with the .txt extension.

![](https://cdn-images-1.medium.com/max/3200/0*ULw7a3-EKh8a3R58)

Now, add the Secret file in Jenkins Credentials.

Click on **Add credentials.**

![](https://cdn-images-1.medium.com/max/3200/0*CGe2OmY53s_Ty0Xv)

Select the **Secret file **and provide the Secret file that you have saved earlier enter the ID k8s then click on **Create.**

![](https://cdn-images-1.medium.com/max/3200/0*FTpwoqhqBN73piM0)

Now, Add the deploy to the Kubernetes stage in your Jenkins pipeline.

    stage('Deploy to Kubernetes'){
                steps{
                    script{
                        dir('Kubernetes') {
                            withKubeConfig(caCertificate: '', clusterName: '', contextName: '', credentialsId: 'k8s', namespace: '', restrictKubeConfigAccess: false, serverUrl: '') {
                                    sh 'kubectl apply -f deployment.yml'
                                    sh 'kubectl apply -f service.yml'
                                    sh 'kubectl get svc'
                                    sh 'kubectl get all'
                            }   
                        }
                    }
                }
            }

![](https://cdn-images-1.medium.com/max/3200/0*tJG7sshf18OIjVgW)

Click on **Build Now**

You will see that our Application has been deployed successfully on Kubernetes.

![](https://cdn-images-1.medium.com/max/3200/0*RTqnlCOQR85hRnxX)

You can validate whether your pods are running or not from your Kubernetes master node.

![](https://cdn-images-1.medium.com/max/3200/0*p4S7U9pq3Tue9D1h)

Also, you can check the Console logs for the earlier results.

![](https://cdn-images-1.medium.com/max/3200/0*cFSvEyFx2ZbTXW72)

We got the email that our pipeline was successful.

![](https://cdn-images-1.medium.com/max/2000/0*ABm_d443ZVF7pvYs)

We get the trivyfs.txt file which contains the vulnerabilities.

![](https://cdn-images-1.medium.com/max/3200/0*Jon4Qzdt30mKzbJb)

Also, we got the vulnerabilities for our Docker Image.

![](https://cdn-images-1.medium.com/max/3200/0*hkWist9x-CqjMoBP)

Jenkins sent the console logs by email.

![](https://cdn-images-1.medium.com/max/3200/0*CmWg6jNMZdgwLpXl)

If you want to access your Netflix **Clone **Application**.**

Copy the Public IP of Worker Node and paste it on your favorite browser with port 32000 and see the magic.

![](https://cdn-images-1.medium.com/max/3200/0*N-bjUNKRLSU_rI9V)

Another Snippet of our Netflix **Clone **application.

![](https://cdn-images-1.medium.com/max/3200/0*HOidmEUFvu5xgLUN)

Go to the Grafana Dashboard and select Node Exporter.

You will see the real-time hardware specs of your Kubernetes master node.

![](https://cdn-images-1.medium.com/max/3200/0*R4TF7d6iKEqiBrTp)

You will see the real-time hardware specs of your Kubernetes worker node.

![](https://cdn-images-1.medium.com/max/3200/0*eosfKXo8tPUk67h9)


Stay up-to-date with **GitHub**: [GitHub Profile](https://github.com/Shivansh5)



