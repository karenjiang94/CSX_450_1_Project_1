# CSX_450_1_Project_1

## Data Science Architecture Blueprint
![AWS Diagram](https://i.imgur.com/54aL7vq.png)

## TABLE of CONTENTS
* SSH Keys
* Amazon EC2
* Security Groups
* AWS Operating System
* Docker
* Jupyter

Before delving into the nitty gritty tasks involved in being a data scientist, this module will help users set up the correct infrastructure with the end goal of accessing Jupyter Data Science Notebook through Docker connected to AWS running on Ubuntu through an SSH on the user's personal platform. (Are there enough prepositions in that sentence?)


## KEY TERMS
- AWS: [Amazon Web Service](https://aws.amazon.com/)
- SSH: Secure SHell
- Docker: "Another layer of abstraction" [What is Docker?](https://www.docker.com/what-docker)
- Jupyter: Julia Python R [Jupyter Notebook](http://jupyter.org)

## Procedure

##### Configuring a Key Pair

Generate a SSH key pair on your local system, Terminal for MacOS users and [GitBASH](https://git-for-windows.github.io) for Windows.
This creates a key id-rsa and lock id-rsa.pub pair. The lock will be placed on the AWS server, which will then enable us to access and operate off of Ubuntu from Amazon.
<pre><code>ssh key-gen </code></pre>

To access the key, we will `cat` the "lock" that will be placed on the Amazon server.
<pre><code>cat ~/.ssh/id_rsa.pub </code></pre> 

>ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQDqux91lz/ju4G8B4VhMesVq6bNpDPic0nAPjmRk+TjLkIPjNRXClqeB+NSU8a3zPXec0yACwj3qyOl526UDR0oC4xJruRWT4Y4YS6SdDqHqZeEQ1mvzDYZ9zZW9GvfCrMXKlBI9rH6ZEqmPj54GoylGuhBOEm63lMbx0126n2fuMpqZlCUK+4SLbGTKXIGPHyX9OvZRQ+qRI4XJweLl/7qN3F6ZE1uFsWkoUtj1EU+Fw+sJW3dcyDMCaV1ufnpyEnBZAhMLGG3c2hMr60MPzlpGYW/szLX/r0A2G1ZF9P0PgEyMeVZevQTntUR+ozYsAj9byt9avK+jCBW2us/EtRb karenjiang94@Karens-MacBook-Pro-7.local

After creating an account with Amazon Web Services, we will select *EC2* Elastic Cloud Computing to have Amazon reserve a little chunk of computer on their server farm. 
On the left hand side-bar, we will import our key pair we created above, and paste the content into *Public key contents*. 

##### Security Groups
Next, we'll be creating our security groups and adding Inbound rules. Create a clever name, or at clear and moderately descriptive. Like "ucla_data_sci". 

| Type       | Protocol   |  Port Range |  Source      | Description |
| --- | --- | --- | --- | --- |
| HTTP       | TCP        |  80         |   Anywhere   |  (HTTP)     |
| Custom TCP | TCP        |  8888       |   Anywhere   |   (Jupyter) |
| SSH        | TCP        |  22         |   Anywhere   |      (SSH)  |                                
| Custom TCP | TCP        |  2376       |   Anywhere   |    (Docker) |
| Custom TCP | TCP        |  27016      |   Anywhere   |     (Mongo) |   



##### Creating an Instance

1. __*Choose AMI:*__ [Amazon Machine Image](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/AMIs.html)
   We will be selecting Ubuntu Server 16.04. ["I am what I am because of who we all are"](https://www.ubuntu.com/about/about-ubuntu)
2. __*Choose Instance Type:*__ t2.micro (1 GiB)
>> For the first 12 months following your AWS sign-up date, you get up to 750 hours of micro instances each month. When your free usage tier expires or if your usage exceeds the free tier restrictions, you pay standard, pay-as-you-go service rates.
3. __*Configure Instance:*__ Number of instances: **1** (Everything else remains in default setting)
4. __*Add Storage:*__ Size (GiB) **30** (Everything else remains in default settings)
>> Free tier eligible customers can get up to 30 GB of EBS General Purpose (SSD) or Magnetic storage. Learn more about free usage tier eligibility and usage restrictions.
5. __*Add Tags:*__ (default setting)
6. __*Configure Security Group:*__ Select an existing security group. The option to select the security group we just created, named "ucla_data_sci" should appear. This is the one we pick.
7. __*Review:*__ You can view the Security Groups, Instance Details, Storage, Tags, to ensure the confirm the previous selections. Otherwise, looking good, good looking.

Finally, launch your instance with an existing key pair, acknowledge and accept, and then we're ready to go!

View the instance you just created, give it a cool name, and locate the _public_ IP address. 

##### Connect terminal to IP

In your Terminal (or GitBash), run:

<pre><code> ssh ubuntu@your_IP_Address </code></pre>
Even though we used the public address, the terminal will display the _private_ address. 

##### Download Docker

Using the command `curl` we will download the Docker from a URL. Here we are downloading using `curl` applying 3 security keys using the script from the website, and piping it directly into the shell. 
<pre><code> curl -sSL https://get.docker.com | sh </code></pre>

Log out with the command `logout`, which will terminate the connection to the AWS server. 
Log back in by calling the same function above `ssh ubuntu@your_IP_Address`.

We modify the user by adding ubuntu to use docker.
<pre><code> sudo usermod -aG docker ubuntu </code></pre>

Check the docker version with `docker -v`
>Docker version 18.01.0-ce, build 03596f5

This downloads the docker image, defined by the Jupyter team
docker pull jupyter/datascience-notebook </code></pre>

This calls docker to run on the ubuntu server through port 8888.
<pre><code> docker run -v /home/ubuntu:/home/jovyan -p 8888:8888 -d jupyter/datascience-notebook</code></pre>
It will return a container ID. Another way to check find the container ID is by running `docker ps -a` which lists the beginning sequence of the Container. The first four characters is enough for identification, the container_id.

Next, docker is called to execute the jupyter image located at the containter_id. 
<pre><code> docker exec container_id jupyter notebook list </code></pre>

A url address with http://localhost:8888/?token= ... will generate. 
Open a browser window and relace the localhost with the public IP address from the created AWS serve, along with the token provided in the url. If all goes well, you should now have Jupyter up and running on your browser. Go forth data scientist! 


## Budget
While AWS offers 750 hours per month for the first 12 months if using t2.micro, it would behoove us to create a budget detailing the costs for running a Jupyter Notebook Server for 3 months. Since the Jupyter Data Science Notebook itself is relatively lightweight, the type of computing the user is hoping to do as well as the quantity of data to be processed will inform which type of EC2 instance other than t2.micro would be chosen.
Our current selection looks at the most cost efficient EC2 instance for Compute Optimized, Memory Optimized, and Storage Optimized.

| Instance Type | Name | vCPU | Memory (GiB) | On-Demand ($/hour) | (On-Demand ($/3mos) | Spot Instances- Linux/UNIX ($/hour) | Spot Instances- Linux/UNIX ($/3mos) |
| --- | --- | --- | --- | --- | --- | --- | --- |
| General Purpose | t2.micro | 1 | 1 | .0116 | 25.40 | .0033 | 7.23 |
| Compute Optimized | c4.large | 2 | 3.75 | .10 | 219.00 | .03 | 62.85 |
