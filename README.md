# jenkins-lab
1- install jenkins with docker image

  using docker run -d -p 8080:8080 -v my-vol:/var/jenkins_home jenkins/jenkins:lts
  
2- install role based authorization plugin

  navigate to manage jenkins -> plugins -> install role based autherization strategy
  
3- create new user

  navigate to manage jenkins -> users -> create new user
  
4- create read role and assign it to the new user

  navigate to manage and assign roles -> create a role named developers 
  at the bottom you will find assign roles add your new user 
  
5- create free style pipeline and link it to private git repo(inside it create directory and create file with "hello world")

   from dashboard click on new item and select freestyle 
   configure the git in scm add a credintial with you git token , bransh name and repo url
   check on delete before each build 
   write your build steps 
     mkdir folder
     touch helloworld 

1- create declarative in jenkins GUI pipeline for your own repo to do "ls"

  uploaded a declarative file on the private repo and used the pipeline from git option to allow it to find the declarative 
  file on the repo 
  
2- create scripted in jenkins GUI pipeline for your own repo to do "ls"

  uploaded a scripted file on the private repo and used the pipeline from git option to allow it to find the scripted 
  file on the repo 
