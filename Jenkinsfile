pipeline {
  agent any
  
  stages {
    stage("code") {
      steps {
        echo "cloning the code"
		git url:"https://github.com/Anshu100993/my-note-app.git", branch: "main" //to get code from the repo and branch
      }
    }
    stage("build") {
      steps {
        echo "building the image"
		sh "docker build -t my-note-app ." //shell command
		//one-time on the jenkins server by ssh execute follwoing cmd: "sudo usermod -aG docker jenkins" now reboot jenkins server "sudo reboot"
		//this is required to add jenkins as a user who have access to docker		
      }
    }
    stage("push to docker hub") {
      steps {
        echo "pushing the image to docker hub"
		// to store credetials in environment variable for jenkins, Add credentials in Jenkins with an ID - dockerhubid
		withCredentials([usernamePassword(credentialsId:"dockerhubid",passwordVariable:"dockerhubpass",usernameVariable:"dockerhubuser")]){
		sh "docker tag my-note-app ${env.dockerhubuser}/my-note-app:latest" //to rename the image with docker format <username / image>
		sh "docker login -u ${env.dockerhubuser} -p ${env.dockerhubpass}"
		sh "docker push ${env.dockerhubuser}/my-note-app:latest"
		}
      }
    }
    stage("deploy") {
      steps {
        echo "deploying the container"
		//In this case, 2nd deployment onwards it will start failing as port 8000 needs to be released first before deploying again
		//install on the jenkins server: sudo apt-get install docker-compose (it refers a YAML file in code which creates a service web with image ref. & ports in use)
		sh "docker-compose down && docker-compose up -d"
		// using this code below becomes redundant, now ports and image location is specified in YAML file
		// sh "docker run -d -p 8000:8000 trainwithshubham/my-note-app:latest" //here, d - detach mode p - publish
		// (port on the host machine, where the app is deployed:port on the container)
		// traffic sent to port 8000 on the host machine will be forwarded to port 8000 inside the container
      }
    }
  }
}
