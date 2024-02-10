# Deploying an Amazon clone app through Terraform and Jenkins for CI/CD.

<h3>Project Introduction:</h3>
In this session, our objective is to utilize Terraform as an infrastructure building (IaC) tool to deploy the Amazon app on AWS EC2. Subsequently, we will automate the entire procedure through Jenkins, enabling continuous integration and continuous deployment (CI/CD). Finally, we will establish Prometheus and Grafana for monitoring purposes.

<h3>Project Overview:</h3>

In this project, we will cover the following key aspects:

1. `IAM User Setup:` Create an IAM user on AWS with the necessary permissions to facilitate deployment and management activities.

2. `Infrastructure as Code (IaC):` Use Terraform and AWS CLI to set up the Jenkins server (EC2 instance) on AWS.
3. `Jenkins Server Configuration:` Install and configure essential tools on the Jenkins server, including Jenkins itself, Docker, Sonarqube, Terraform,  and AWS CLI.
4. `Sonarqube Integration:` Integrate Sonarqube for code quality analysis in the DevSecOps pipeline.
5. `Jenkins Pipelines:` Create Jenkins pipelines.
6. `Monitoring Setup:` Implement monitoring with Prometheus and Grafana.

<h3>Prerequisites:</h3>

  1. An AWS account with the necessary permissions to create resources.
  2. Terraform and AWS CLI installed on your local machine.
  3. Basic familiarity with Docker, Jenkins, and DevOps principles.


<h3>Step 1: Create an IAM user and generate the AWS Access key</h3> 

We need to create a new IAM User on AWS and give it to the AdministratorAccess for testing purposes (not recommended for your Organization's Projects)

Go to the AWS IAM Service 

![alt text](https://github.com/macielo-bumalay/DevOps-Project-2/blob/main/img/a1.png?raw=true) 

Click on `Users`

![alt text](https://github.com/macielo-bumalay/DevOps-Project-2/blob/main/img/a2.png?raw=true) 

Create a name to your `user` and tick on provide user access to management console then click on I want an `IAM user option` and provide a `password` then click on `Next`

![alt text](https://github.com/macielo-bumalay/DevOps-Project-2/blob/main/img/a3.png?raw=true) 

Select the Attach policies directly option and search for Administrator then select it. Click on >>>`Next`

![alt text](https://github.com/macielo-bumalay/DevOps-Project-2/blob/main/img/a4.png?raw=true) 

Click on `Create user`

![alt text](https://github.com/macielo-bumalay/DevOps-Project-2/blob/main/img/a5.png?raw=true) 

Now, Select your created user then click on `Security credentials` and generate access key by clicking on `Create access key`.

![alt text](https://github.com/macielo-bumalay/DevOps-Project-2/blob/main/img/a6.png?raw=true) 

Select the `Command Line Interface (CLI)` then select the checkmark for the confirmation and click on >>>`Next`

![alt text](https://github.com/macielo-bumalay/DevOps-Project-2/blob/main/img/a7.png?raw=true) 

Here, you will see that you got the credentials and also you can download the CSV file for the future.

![alt text](https://github.com/macielo-bumalay/DevOps-Project-2/blob/main/img/a8.png?raw=true) 


<h3>Step 2: Install and extract DevOps  tools that we were going to use in this Project <i>AWS CLI, Terraform, Prometheus, Node Exporter, and Grafana </i>  </h3> 

Terraform Installation Script

    wget -O- https://apt.releases.hashicorp.com/gpg | sudo gpg - dearmor -o /usr/share/keyrings/hashicorp-archive-keyring.gpg
    echo "deb [signed-by=/usr/share/keyrings/hashicorp-archive-keyring.gpg] https://apt.releases.hashicorp.com $(lsb_release -cs) main" | sudo tee /etc/apt/sources.list.d/hashicorp.list
    sudo apt update
    sudo apt install terraform -y

AWSCLI Installation Script

    curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
    sudo apt install unzip -y
    unzip awscliv2.zip
    sudo ./aws/install

Prometheus Installation Script

      sudo useradd — system — no-create-home — shell /bin/false prometheus
      wget https://github.com/prometheus/prometheus/releases/download/v2.47.1/prometheus-2.47.1.linux-amd64.tar.gz
    
      tar -xvf prometheus-2.47.1.linux-amd64.tar.gz
      cd prometheus-2.47.1.linux-amd64/
      sudo mkdir -p /data /etc/prometheus
      sudo mv prometheus promtool /usr/local/bin/
      sudo mv consoles/ console_libraries/ /etc/prometheus/
      sudo mv prometheus.yml /etc/prometheus/prometheus.yml

      useradd prometheus
      sudo chown -R prometheus:prometheus /etc/prometheus/ /data/


Node Exporter Installation Script

    sudo useradd — system — no-create-home — shell /bin/false node_exporter
    wget https://github.com/prometheus/node_exporter/releases/download/v1.6.1/node_exporter-1.6.1.linux-amd64.tar.gz

    tar -xvf node_exporter-1.6.1.linux-amd64.tar.gz
    sudo mv node_exporter-1.6.1.linux-amd64/node_exporter /usr/local/bin/
    rm -rf node_exporter-1.6.1.linux-amd64


Grafana Installation Script

    sudo apt-get install -y apt-transport-https software-properties-common

    wget -q -O —  https://packages.grafana.com/gpg.key | sudo apt-key add -
    echo “deb https://packages.grafana.com/oss/deb stable main” | sudo tee — a /etc/apt/sources.list.d/grafana.list

    sudo apt-get update
    sudo apt-get -y install grafana
      
<h3>Step 3: Setup Sonarqube and Jenkins Server</h3> 

<h4>Sonarqube</h4> Enter instance IP address and 9000 as port, by default the username and password is `admin`

![alt text](https://github.com/macielo-bumalay/DevOps-Project-2/blob/main/img/a9.png?raw=true) 

Now update the password

![alt text](https://github.com/macielo-bumalay/DevOps-Project-2/blob/main/img/a10.png?raw=true) 

You see below is the homepage of Sonarqube

![alt text](https://github.com/macielo-bumalay/DevOps-Project-2/blob/main/img/a11.png?raw=true) 

<h4>Now Let's move on to Jenkins</h4>

<h4>Jenkins</h4> Enter instance IP address and 8080 as port

To access it we need to provide first the Admin Password, in your terminal enter this `cat /var/lib/jenkins/secrets/initialAdminPassword`

![alt text](https://github.com/macielo-bumalay/DevOps-Project-2/blob/main/img/a12.png?raw=true) 

Choose `Install suggested plugins`

![alt text](https://github.com/macielo-bumalay/DevOps-Project-2/blob/main/img/a14.png?raw=true) 

Wait until completed

![alt text](https://github.com/macielo-bumalay/DevOps-Project-2/blob/main/img/a15.png?raw=true) 

Create an Admin user Account

![alt text](https://github.com/macielo-bumalay/DevOps-Project-2/blob/main/img/a16.png?raw=true) 

Your now at the homepage of Jenkins, Click on Manage Jenkins

![alt text](https://github.com/macielo-bumalay/DevOps-Project-2/blob/main/img/a17.png?raw=true) 

<h5>Next thing to do is Install Plugins<h5>

1. Jdk
2. Owasp
3. Sonar
4. NodeJs
5. Docker
6. Prometheus metrics

![alt text](https://github.com/macielo-bumalay/DevOps-Project-2/blob/main/img/a18.png?raw=true) 








