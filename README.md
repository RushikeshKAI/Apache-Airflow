# Apache-Airflow
We will see required Installation and implementation of Apache-Airflow setup after that we will implement bunch of data pipelines called DAG to check how our tasks will run in mode of sequential, local, celery executor to achieve concurrent task executions in airflow with the help of scheduler.


Prerequisites:
    1. Python3.5+
    2. VS code (recommended) IDE	    remote-ssh plugin required
    3. VirtualBox(the latest version)   link: https://www.virtualbox.org/wiki/Linux_Downloads
    4. Virtual Machine(AirflowVM.ova)   link: https://marclamberti.wetransfer.com/downloads/b1057cf63e657b355cdf4eae70e65e6620210113125028/663de9
    5. Airflow Installation
	6. Additional packages it will be based on VM and python version. I am having 2.1.0 VM and 3.9 python you will get from requirement.txt
	7. Need to have knowledge of python

**Apache Airflow:**
    Apache Airflow is an open-source platform to programmatically author, schedule and monitor workflows. It is an best data orchestration platform to enables data engineers, data scientists, and data analysts to build, run, and observe pipelines-as-code..

Core Components:
    1. WebServer: Flask server with gunicorn serving the UI
    2. Scheduler: Daemon in change of scheduling workflows
    3. Metastore: Database where Metadata are stored
    4. Executor: class defining how your tasks should be executed
    5. Worker: Process/Sub process executing your task
	6. flower: You will see overview of tasks(data-pipelines) statuses in UI

DAG:
    In DAG there is no loop occurs and DAG in Airflow is nothing but data-pipelines. 
    Mentioned or given tasks will get executed by priority wise.

Operator:
    It is used to run the task

Types of Operators:
    1. Action Operators
        Execution function or commands.
        ex. Bash operator and python operator to run python functions.
    2. Transfer Operator
        Transferring data between source to destinations.
    3. Sensor Operator:
        Automatically handles conditions like if lines of code needs to be executed  upon some conditions 
        it will search for inputs based on provided parameters if there is present.

Task and Task Instance:
    It is in queue and ready to run(not yet started for execution).
    when the task is ready to run its task instance is created.

**Stepwise Procedure to download All required softwares:**

Download Virtual Box through below provided link:
	Download and install it by following instructions
	https://www.virtualbox.org/wiki/Linux_Downloads
	or
	Installing Virtual Box from Ubuntu Repositories
	$ sudo apt update
	$ sudo apt install virtualbox vitrualbox-ext-pack

Download Virtual Machine	https://marclamberti.wetransfer.com/downloads/b1057cf63e657b355cdf4eae70e65e6620210113125028/663de9

Update the packages list and install the latest version of VirtualBox:
	$ sudo apt update
	$ sudo apt install virtualbox=6.1.1


Installing Airflow and its dependencies:
If you don’t have an virtual environment create:
	$ python3 -m venv <new-env-name>
	$ source <new-env-name>/bin/activate
	$ pip install wheel
	$ pip install apache-airflow==2.1.0 –constraint https://raw.githubusercontent.com/apache/airflow/constraints-2.1.0/constraints-3.9.txt
	or above instead of constraint and link you can use requirement.txt if you have same versions of VM and python.
	$ airflow db init
	$ ls
	$ cd airflow/
	$ ls

Once you done with this procedure 
You need to open VS code in order to run and connect to remote-ssh
1. first go to the downloaded VM.odv then open and do import VM 
2. Open virtual box and you will see VM is added.
3. run VM by click pn right side green right button it will run.
4. Open VS code IDE and go to plugins and search 'remote ssh'
	1. Press F1 and run the Remote-SSH: Open SSH Host... command.
	2. Enter your user and host/IP in the following format in the input box that appears and press enter: airflow@localhost 8080
	3. If prompted, enter your password (but we suggest setting up key based authentication).
	4. After you are connected, use File > Open Folder to open a folder on the host.
	5. Press F1 and enter  >remote-ssh: connect to host.... You will be prompted for password, by entering right password you will connect to host

Installation of Required Airflow Plugins:

	$ pip install ‘apache-airflow-providers-sqlite’
	$ pip install ‘apache-airflow-provides-http’
	$ pip install ‘apache-airflow-providers-postgres’

	If your apache airflow version is below than 2.2.0 use following celery package version:
	$ pip install ‘apache-airflow-providers-celery==2.0.0’
	or if already installed do upgrade
	$ pip install –upgrade ‘apache-airflow-providers-celery==2.0.0’

If you need additional plugins check here with the below link:
	https://airflow.apache.org/docs/


**Common Commands**
Note: Please make sure for every terminal virtual environment is activated and below given serices has to be run on separate terminal.

Start webserver:
	$ airflow webserver
Start scheduler:
	$ airflow scheduler
Start Redis-Server:
	$ sudo systemctl restart redis.service
To check redis server running status:
	$ sudo systemctl status redis.service
Start Flower:
	$ airflow celery flower
It will run on 5555 port number

Start worker:
	$ airflow celery worker


Command Line Interface commands:
	$ airflow -h
	It will lists all possible command line interface commands.
	Again we can hit -h command for groups also
	$ airflow db -h
	
	Create user:
	$ airflow users create -u <username> -p <password> -f <first name> -l <last name> -e 	<email>

	To run shceduler:
	$ airflow scheduler


$ airflow dags list
$ airflow tasks list <task-name>
$ airflow dags trigger -e 2022-04-21 <task-name>


**NOTE: If you made any changes in airflow config file, please do restart all services**

To Test airflow tasks:
	$ airflow tasks test <dag_id> <task_id> <scheduling-date YYYY-MM-DD>



**Please look into following issues, Particulary if you came across with kernel driver and celery package**
**Issues:** 
1. Virtual Box Error: Failed to open a session for the virtual machine
	Kernel driver not Installed (rc=-1908)
	If you specifically came across with this type of issue then this error is related kernel 	driver and system is activated with secure boot mode.
	You need to follow following steps to resolve this issue without disabling UEFI secure 	boot.
 	1. Create a personal public/private RSA key pair to sign the kernel modules. As 	recommended in the link below, I chose to store the key/pair in the /root/module-signing/ 	directory.
    	> sudo -i
    	> mkdir /root/module-signing
    	> cd /root/module-signing
    	> openssl req -new -x509 -newkey rsa:2048 -keyout MOK.priv -outform DER -out 	MOK.der - 	nodes -days 36500 -subj "/CN=YOUR_NAME/" chmod 600 MOK.priv

	2. Use mokutil, a tool to import or delete the machine owner keys (MOK), to import the 	public key, and then enroll it when the machine is rebooted. The password in this step is a t	emporary use password you'll only need to remember for a few minutes.

	> mokutil --import /root/module-signing/MOK.der
	> input password:
	> input password again:

	3. Reboot the machine. When the bootloader starts, you should see a screen asking you to 	press a button to enter the MOK manager EFI utility. Note that any external external 	keyboards 	won't work in this step. Select Enroll MOK in the first menu, then continue, 	and then select Yes to 	enroll the keys, and re-enter the password established in step 2. 	Then select OK to continue the 	system boot.

	4. Future kernel updates would require the updated kernels to be signed again, so it makes 	sense to put the signing commands in a script that can be run at a later date as necessary. A 	sample script /root/module-signing/sign-vbox-modules is given below.
	
	Create one file with following command:
		$ sudo cat > file_name
	Open and save below lines of code in file. 
	
	#!/bin/bash

	for modfile in $(dirname $(modinfo -n vboxdrv))/*.ko; do
  		echo "Signing $modfile"
  		/usr/src/linux-headers-$(uname -r)/scripts/sign-file sha256 \
                                /root/module-signing/MOK.priv \
                                /root/module-signing/MOK.der "$modfile"
	done

	
	5. Add execution permission, and run the script above as root from the /root/module-signing/ 	directory.

	> sudo -i
    	> cd /root/module-signing
    	> chmod 700 /root/module-signing/sign-vbox-modules
   	./sign-vbox-modules
	
	6. Load vboxdrv module and launch VirtualBox.
	
	> modprobe vboxdrv 

2. If you came across flower module not found error
Airflow version is below than 2.2.0 then doesn't support Celery 5 version yet.
We need to downgrade our celery version: just downgrade Celery provider:
Command:
$ pip install --upgrade apache-airflow-providers-celery==2.0.0
It will resolve the flower UI service issue and worker also.

**Installing Elasticsearch over VM to use as a Plugin**

In order to make the plugin section of the course really interesting, we are going to use a popular tool which is Elasticsearch!
Elasticsearch is search engine providing a way of searching in data at scale in a very efficient way. In a nutshell, you have a lot of logs to analyse, Elasticsearch does a very good job at analysing them.
As we want to interact with Elasticsearch from Airflow, we need to install it.

In the terminal, execute
curl -fsSL https://artifacts.elastic.co/GPG-KEY-elasticsearch | sudo apt-key add -


Then execute 
echo "deb https://artifacts.elastic.co/packages/7.x/apt stable main" | sudo tee -a /etc/apt/sources.list.d/elastic-7.x.list

Next 
sudo apt update && sudo apt install elasticsearch

Finally 
pip install elasticsearch==7.10.1

We still have to start and test it. Let's do it!Start Elasticsearch with
sudo systemctl start elasticsearch

This command can take a bit of time before getting it done.

Check if Elasticsearch works with 
curl -X GET 'http://localhost:9200'

To Check if we have some data inside the index connection
curl -X GET "http://localhost:9200/connections/_search" -H "Content-type: application/json" -d '{"query":{"match_all":{]}}'


**Using Apache-Airflow with Docker**

> mkdir airflow-local
> cd airflow-local/
> wget https://airflow.apache.org/docs/apache-airflow/2.1.0/docker-compose.yaml
> sudo apt install docker-compose

Install docker-compose stepswise:
$ sudo apt update
$ sudo apt upgrade
$ sudo apt install curl
$ sudo curl -L "https://github.com/docker/compose/releases/download/1.29.2/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
$ sudo chmod +x /usr/local/bin/docker-compose
$ sudo docker–compose –version
$ which docker-compose
you will get path 
$ sudo <path-from-above-command> up
or 
otherwise you can run 
$ docker-compose -f docker-compose.yaml up -d
to check docker conatiner status use below command
$ docker ps