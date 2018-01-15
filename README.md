# CSX_450_1_Project_1

## Data Science Architecture Blueprint
![AWS Diagram](https://imgur.com/a/LofzQ)

## TABLE of CONTENTS
* SSH Keys
* Amazon EC2
* Security Groups
* AWS Operating System
* Docker
* Jupyter

Before delving into the nitty gritty tasks involved in being a data scientist, this module will help users set up the correct infrastructure with the end goal of accessing Jupyter Data Science Notebook through Docker connected to AWS running on Ubuntu through an SSH on the user's personal platform.(Are there enough prepositions in that sentence?)

As Prof. Joshua Cook is an analogy afficionado, this readme will attempt to include an excessive amount of analogies. 

## KEY TERMS
- AWS: [Amazon Web Service](https://aws.amazon.com/)
- SSH: Secure SHell
- Docker: "Another layer of abstraction" [What is Docker?](https://www.docker.com/what-docker)
- Jupyter: Julia Python R [Jupyter Notebook](http://jupyter.org)

## Procedure

##### Configuring a Key Pair

Generate a SSH key pair on your local system, Terminal for MacOS users and [GitBASH](https://git-for-windows.github.io) for Windows.

<pre><code>ssh key-gen </code></pre>

This creates a key id-rsa and lock id-rsa.pub pair. The lock will be placed on the AWS server, which will then enable us to access. (This analogy already wrote itself, no credit)

##### Amazon EC2

After creating an account with Amazon Web Services, we will select *EC2* Elastic Cloud Computing to have Amazon reserve a little chunk of computer on their server farm. 
On the left hand side-bar, we will import our key pair we created above. 

Next, we'll be creating our security groups and adding Inbound rules. Create a clever name, or at clear and moderately descriptive. Like "ucla_data_sci". 

| Type       | Protocol   |  Port Range |  Source      | Description |
| --- | --- | --- | --- | --- |
| HTTP       | TCP        |  80         |   Anywhere   |  (HTTP)     |
| Custom TCP | TCP        |  8888       |   Anywhere   |   (Jupyter) |
| SSH        | TCP        |  22         |   Anywhere   |      (SSH)  |                                
| Custom TCP | TCP        |  2376       |   Anywhere   |    (Docker) |
| Custom TCP | TCP        |  27016      |   Anywhere   |     (Mongo) |   



##### Creating an Instance

1. Choose an AMI [Amazon Machine Image](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/AMIs.html)
   We will be selecting Ubuntu ["I am what I am because of who we all are"](https://www.ubuntu.com/about/about-ubuntu)
2. RAM t2.micro (1 GiB)
>> For the first 12 months following your AWS sign-up date, you get up to 750 hours of micro instances each month. When your free usage tier expires or if your usage exceeds the free tier restrictions, you pay standard, pay-as-you-go service rates.
3. CPU 1 Instance (default setting)
4. HardDrive 30 GiB.
5. Tags (default setting)
6. Configure Security Group. Select the previously create security group

Launch your instance with an existing key pair, name your instance, and then we're ready to go!

View the instance you just created and locate the _public_ IP address. 

##### Connect terminal to IP

In your Terminal (or GitBash), run:

<pre><code> ssh ubuntu@your_IP_Address </code></pre>
Even though we used the public address, the terminal will display the _private_ address. 

##### Download Docker

Using the command `curl` we will download the Docker from a URL. Here we are downloading using `curl` applying 3 security keys using the script from the website, and piping it directly into the shell. 
<pre><code> curl -sSL https://get.docker.com | sh </code></pre>

We modify the user by adding ubuntu to use docker.
<pre><code> sudo usermod -aG docker ubuntu </code></pre>

This downloads the docker image, defined by the Jupyter team
<pre><code>docker -v
docker pull jupyter/datascience-notebook </code></pre>

This calls docker to run on the ubuntu server through port 8888.
<pre><code> docker run -v /home/ubuntu:/home/jovyan -p 8888:8888 -d jupyter/datascience-notebook</code></pre>
It will return a container ID. Another way to check find the container ID is by running `docker ps -a` which lists the beginning sequence of the Container. The first four characters is enough for identification, the container_id.

Next, docker is called to execute the jupyter image located at the containter_id. 
<pre><code> docker exec container_id jupyter notebook list </code></pre>

A url address with http://localhost:8888/?token= ... will generate. 
Open a browser window and relace the localhost with the public IP address from the created AWS serve, along with the token provided in the url. If all goes well, you should now have Jupyter up and running on your browser. Go forth data scientist! 



