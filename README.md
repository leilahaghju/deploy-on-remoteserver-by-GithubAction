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
         to login to remote server by key not password you can do these things:( on remote server)
               1) first create a user named "deploy" in focker group
                  sudo useradd --create-home --user-group --shell /bin/bash --groups docker deploy
                  sudo usermod --lock deploy
               2) on the server, change to deploy user and create ssh key(keep in mind key should not have passphrase)
                  sudo -i -u deploy
                  ssh-keygen -t ed25519 -f ~/.ssh/id_ed25519 -C "deploy@server"
                  cat .ssh/id_ed25519.pub > .ssh/authorized_keys
               3) both the public key and private key needed to be added to github repository:
                  for public key : In your repo, go to the ‘Settings’ tab, and find the ‘Deploy keys’ tab on the left. Click “Add deploy key” and enter the content of                      .ssh/id_ed25519.pub into the box. It should begin with “ssh-ed25519”.
                  for private key: we save it as "SSH_KEY" secrets that explained above.
                  
                  
          GITHUB ACTIONS WORKFLOWS:
          Next, the workflow file is created in the github repository. The filepath must be .github/workflows/deploy.yml
          our scripts is a bash script that run our needed commands:
                 #!/bin/bash
                 docker login ; docker pull "OUR_IMAGE_NAME" ; docker commit foxiverse-web-app "OUR_IMAGE_NAME":$(date +'%Y-%m-%d_%H-%M') ;docker rm -f foxiverse-web-                   app; docker run -d -it  --restart=always --network=mytestnet --name foxiverse-web-app "OUR_IMAGE_NAME"
                
                as you can see: at first we login to docker hub registery and to do that we use "docker-credential-pass" to save our ligin username and password on 
                the server ans ofcourse as encrypted . when we want to simply login to docker we use this command: docker login -u "username" -p "password" and by                     doing that a base64 encode text of username:passwprd will be saved by docker in a file in this path: cat /root/.docker/config.json and anyone can                       decode it. to not do this risky work , we have to use "docker-credential-pass", an official Docker Credential Helper which Provides a helper to use                     "pass" as credentials store.
                so to do this:
                   1) in a directory that you choose:  wget https://github.com/docker/docker-credential-helpers/releases/download/v0.6.3/docker-credential-pass-v0.6.3-amd64.tar.gz 
                   2) tar xzf docker-credential-pass-v0.6.3-amd64.tar.gz ; cp docker-credential-pass /usr/bin/ ; chmod +x /usr/bin/docker-credential-pass
                   3)check that docker-credential-pass work. To do this, run command docker-credential-pass. You should see: "Usage: docker-credential-pass                                  <store|get|erase|list|version>"
                   4) apt-get install pass,gpg
                   5) gpg --generate-key
                        *Enter your name, mail, etc. You will get gpg-id like "5BB54DF1XXXXXXXXF87XXXXXXXXXXXXXX945A". Copy it to clipboard
                   6) pass init 
                         *paste from clipboard
                   7) pass insert docker-credential-helpers/docker-pass-initialized-check
                         *in this step you can set your login password
                   8) pass show docker-credential-helpers/docker-pass-initialized-check
                         *you can check if your password the one you enter or not
                   9) docker-credential-pass list
                         *You should see {} or another data. You shouldn`t see error like "pass store is uninitialized"
                   10) nano ~/.docker/config.json
                          *Set in root node the next line "credsStore": "pass" save ctrl+o ( if it's not already there)
                   11) then do docker login and etc.
                   
                   

AND THAT'S HOW IT WORKS FINE:
                        
