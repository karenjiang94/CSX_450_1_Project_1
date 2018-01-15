# CSX_450_1_Project_1

## Data Science Architecture Blueprint

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

##### Configuring a Key Pair

Generate a SSH key pair on your local system, Terminal for MacOS users and [GitBASH](https://git-for-windows.github.io) for Windows.

<pre><code>ssh key-gen </code></pre>

This creates a key id-rsa and lock id-rsa.pub pair. The lock will be placed on the AWS server, which will then enable us to access. (This analogy already wrote itself, no credit)

##### Amazon EC2

After creating an account with Amazon Web Services, we will select *EC2* Elastic Cloud Computing to have Amazon reserve a little chunk of computer on their server farm. 
On the left hand side-bar, we will import our key pair we created above. 

Next, we'll be creating our security groups and adding Inbound rules. 

| Type       | Protocol   |  Port Range |  Source      | Description |
| --- | --- | --- | --- | --- |
| HTTP       | TCP        |  80         |   Anywhere   |  (HTTP)     |
| Custom TCP | TCP        |  8888       |   Anywhere   |   (Jupyter) |
| SSH        | TCP        |  22         |   Anywhere   |      (SSH)  |                                
| Custom TCP | TCP        |  2376       |   Anywhere   |    (Docker) |
| Custom TCP | TCP        |  27016      |   Anywhere   |     (Mongo) |   

