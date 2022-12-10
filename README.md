# Deploy-On-A-RemoteServer-By-GithubActions

The "deploy.yml" file must be located in ".github/workflows" of your repository and you can give it nay name that you want.
By using this script, you can build your image, push it into DockerHub registry and then deploy it on your remote server with a script.
It works on "dev" branch and by every push, the action will run.

The important things to know is that:

secrets: I define some secrets in Github itself which define critical informations like usernames, passwords that shoud not be lefted unencrypted.
         To define secrets you can go to the "Settings" part of your repository,then "Secrets" and then "Actions". by clicking "New repository secret" green button,
            you can create required ones. 
         DOCKERHUB_USERNAME and DOCKERHUB_PASSWORD can be defined as organization secrets so can be accessed by all your repositories.
         SSH_HOST , SSH_USER, SSH_KEY will be needed to login to remote server. first one can be "hostname" or "ip" , second one is "username" to login to server ,
         and third one is the "private key" of the remote server.
         
         HOW TO GENERATE PUBLIC AND PRIVATE KEY TO lOGIN TO REMOTE SERVER:
