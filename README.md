A step-by-step guide to creating the Jenkins pipeline


Why Jenkins Pipeline?
Continuous Delivery (CD) is an essential part of DevOps Lifecycle.

It makes sure that the software/application developers are creating is always production-ready. For this to happen, whenever the code gets updated, it needs to be built, tested, and deployed continuously. This is where Jenkins Pipeline comes into the picture.

In DevOps, Continuous Integration and Continuous Delivery (CI/CD) is achieved through Jenkins Pipeline.  Using Jenkins Pipeline for CD helps to deliver the software with faster and frequent releases. This helps to incorporate the feedback in every next release.

What is Jenkins Pipeline?
Jenkins Pipeline is a combination of jobs to deliver software continuously using Jenkins.



I assume you know what Jenkins is. If not then check out this Udemy course to master Jenkins.

A Jenkins pipeline consists of several states or stages, and they get executed in a sequence one after the other. JenkinsFile is a simple text file that is used to create a pipeline as code in Jenkins. It contains code in Groovy Domain Specific Language (DSL), which is simple to write and human-readable.

Either you can run JenkinsFile separately, or you can run the pipeline code from Jenkins Web UI also. There are two ways you can create a pipeline using Jenkins.

Declarative – a new way of creating Jenkins Pipeline. Here you write groovy code containing “pipeline” blocks, which is checked into an SCM (Source Code Management)
Scripted – way of writing groovy code where the code is defined inside “node” blocks.
Before we get into the demo, if you have not installed Jenkins, please install it first. Make sure you have Jenkins up and running on your system.

Create a Jenkins Pipeline
This is how a Jenkins pipeline flow looks like, which consists of multiple stages between developers creating a software (coding) and software delivered on production.


![1](https://user-images.githubusercontent.com/93249038/221332383-5dc82af0-81f5-485d-ab78-b9f32a9ed1cc.jpg)

Jenkins Pipeline
Let’s create a declarative pipeline.

![2](https://user-images.githubusercontent.com/93249038/221332414-25da25fc-c1e5-4262-a434-81e10d56757f.jpg)


On the Jenkins dashboard, click on New Item. Then enter an item name, for example, ‘First Pipeline’ and select the ‘Pipeline’ project. Then click on, OK.

New Item - Jenkins Pipeline
Click on the Pipeline tab as shown in the image below, and put your JenkinsFile code (Groovy Code) here.

![3](https://user-images.githubusercontent.com/93249038/221332436-7ff55614-22fa-40e9-89da-5739f378bac7.jpg)


JenkinsFile Code
pipeline {

         agent any
         
         stages {
         
                 stage('Build') {
                 
                 steps {
                 
                     echo 'Hi, GeekFlare. Starting to build the App.'
                     
                 }
                 
                 }
                 
                 stage('Test') {
                 
                 steps {
                 
                    input('Do you want to proceed?')
                    
                 }
                 
                 }
                 
                 stage('Deploy') {
                 
                 parallel { 
                 
                            stage('Deploy start ') {
                            
                           steps {
                           
                                echo "Start the deploy .."
                                
                           } 
                           
                           }
                           
                            stage('Deploying now') {
                            
                            agent {
                            
                                    docker {
                                    
                                            reuseNode true
                                            
                                            image ‘nginx’
                                            
                                           }
                                           
                                    }
                            
                              steps {
                              
                                echo "Docker Created"
                                
                              }
                              
                           }
                           
                           }
                           
                           }
                           
                 stage('Prod') {
                 
                     steps {
                     
                                echo "App is Prod Ready"
                                
                              }
                 
              }
              
}

}
Let me explain the above blocks.

1 The pipeline block consists of all the instructions to build, test, and deliver software. It is the key component of a Jenkins Pipeline.

2 An agent is assigned to execute the pipeline on a node and allocate a workspace for the pipeline.

3 A stage is a block that has steps to build, test, and deploy the application. Stages are used to visualize the Jenkins Pipeline processes.

4 A step is a single task to be performed, for example, create a directory, run a docker image, delete a file, etc.

5 The Groovy code above, I am using for the JenkinsFile. Any available agent is getting assigned to the pipeline. Then I am defining the Build stage and performing a simple echo step. Then I defined the Test stage where the step asks whether you want to proceed or not. After that, I have created a Deploy stage, which has two more stages in it running in parallel. Deploy start stage has a step with echo command, and Deploying now has a step that pulls a docker image of Nginx on the node. Finally, there is a Prod stage with a simple echo step.

The above-explained pipeline has stages that have simple steps for you to understand how it works. Once you learn how to create a Pipeline, you can add more complexity and create complex pipelines also.

Once you have the code in the Pipeline tab, click on Apply and Save. Finally, click on Build Now to start building the Jenkins Pipeline you just created.

Build Now
This is how the user interface will look like when the pipeline runs. If you have followed all the steps correctly, 
your build will be successful, and it shows a blue color for the build (#27). If there are mistakes in the build, it will give red color to the build (#27).

![6](https://user-images.githubusercontent.com/93249038/221332532-ab07acaf-aa29-422b-a7c8-6087ef52234e.jpg)


Jenkins Build 27
Now click on #27 build and then click on Console Output to check what exactly happened in the back-end when it ran.

Jenkins Build

This is how the output will look like ending with a SUCCESS message.

Success Console Output

Started by user Mithun

Running in Durability level: MAX_SURVIVABILITY

[Pipeline] Start of Pipeline

[Pipeline] node

Running on Jenkins in /var/lib/jenkins/workspace/First Pipeline

[Pipeline] {

[Pipeline] stage

[Pipeline] { (Build)

[Pipeline] echo

Hi, Mithun. Starting to build the App.

[Pipeline] }

[Pipeline] // stage

[Pipeline] stage

[Pipeline] { (Test)


[Pipeline] input

Do you want to proceed?

Proceed or Abort

Approved by geekflare

[Pipeline] }

[Pipeline] // stage

[Pipeline] stage

[Pipeline] { (Deploy)

[Pipeline] parallel

[Pipeline] { (Branch: Deploy start )

[Pipeline] { (Branch: Deploying now)

[Pipeline] stage

[Pipeline] { (Deploy start )

[Pipeline] stage

[Pipeline] { (Deploying now)

[Pipeline] getContext

[Pipeline] isUnix
[Pipeline] sh

[Pipeline] echo

Start the deploy ..

[Pipeline] }

+ docker inspect -f . nginx

[Pipeline] // stage

[Pipeline] }


Error: No such object: nginx

[Pipeline] isUnix

[Pipeline] sh

+ docker pull nginx

Using default tag: latest

latest: Pulling from library/nginx

8d691f585fa8: Pulling fs layer

5b07f4e08ad0: Pulling fs layer

abc291867bca: Pulling fs layer

abc291867bca: Verifying Checksum

abc291867bca: Download complete

5b07f4e08ad0: Verifying Checksum

5b07f4e08ad0: Download complete

8d691f585fa8: Verifying Checksum

8d691f585fa8: Download complete

8d691f585fa8: Pull complete

5b07f4e08ad0: Pull complete

abc291867bca: Pull complete

Digest: sha256:922c815aa4df050d4df476e92daed4231f466acc8ee90e0e774951b0fd7195a4

Status: Downloaded newer image for nginx:latest

[Pipeline] withDockerContainer

Jenkins does not seem to be running inside a container

$ docker run -t -d -u 125:131 -w "/var/lib/jenkins/workspace/First Pipeline" -v "/var/lib/jenkins/workspace/First Pipeline:/var/lib/jenkins/workspace/First Pipeline:rw,z" -v "/var/lib/jenkins/workspace/First Pipeline@tmp:/var/lib/jenkins/workspace/First Pipeline@tmp:rw,z" -e ******** -e ******** -e ******** -e ******** -e ******** -e ******** -e ******** -e ******** -e ******** -e ******** -e ******** -e ******** -e ******** -e ******** -e ******** -e ******** -e ******** -e ******** -e ******** -e ******** -e ******** -e ******** -e ******** nginx cat
$ docker top 3141793b98f315dc90a57d810068b8eedb6f62debafb9040de06635b558f2943 -eo pid,comm

[Pipeline] {

[Pipeline] echo

Docker Created

[Pipeline] }


$ docker stop --time=1 3141793b98f315dc90a57d810068b8eedb6f62debafb9040de06635b558f2943

$ docker rm -f 3141793b98f315dc90a57d810068b8eedb6f62debafb9040de06635b558f2943

[Pipeline] // withDockerContainer

[Pipeline] }

[Pipeline] // stage

[Pipeline] }

[Pipeline] // parallel

[Pipeline] }

[Pipeline] // stage

[Pipeline] stage

[Pipeline] { (Prod)

[Pipeline] echo

App is Prod Ready

[Pipeline] }

[Pipeline] // stage

[Pipeline] }

[Pipeline] // node

[Pipeline] End of Pipeline

Finished: SUCCESS


The above Jenkins Pipeline has created a docker image of Nginx. You can verify that by running the below command. You can see the Nginx docker image is also listed.

Mithun:~$ docker image ls

REPOSITORY TAG IMAGE ID CREATED SIZE

nginx latest 540a289bab6c 2 weeks ago 126MB

httpd latest 7d85cc3b2d80 2 months ago 154MB

redis latest f7302e4ab3a8 2 months ago 98.2MB

Mithun_mongodb latest 095d17727ca0 3 months ago 325MB

Conclusion

I hope this gives you an idea of Jenkins pipeline benefits and how you can create one. The above is a simple demonstration, and the best way to learn is by trying building complex pipelines.
