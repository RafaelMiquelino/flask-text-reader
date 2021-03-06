# flask-text-reader
Make text files like logs available for simple remote access via web browser.
## Instructions
1) Change the line `FILE_PATH=your_file_path_here` in the `.env` file including the path to the file you want to read (the file path is without quotes).
2) Run `pip install -r requirements.txt`
3) Run `bash run_gunicorn` or `bash run_flask_server` for debugging.
4) Your file content will be available on `http://your_ip:8050`.
## Instructions to install as a service
1) Create a unit file ending in .service within the /etc/systemd/system directory to begin:
`sudo vim /etc/systemd/system/flask-text-reader.service`
2) Fill the file with the data describing the service:
```
[Unit]
Description=Gunicorn instance to serve flask-text-reader
After=network.target

[Service]
User=user_name
WorkingDirectory=/home/user_name/flask-text-reader
Environment="PATH=/home/user_name/flask-text-reader/venv/bin"
EnvironmentFile=/home/user_name/flask-text-reader/.env
ExecStart=/home/user_name/flask-text-reader/venv/bin/gunicorn --bind 0.0.0.0:8050 app:app

[Install]
WantedBy=multi-user.target
```
3) To ensure proper opeaeration with multiple users, make sure the folder and its contents belongs to a common group of users:  
`sudo chgrp -R root flask-text-reader/`  
## Instructions to load the service using Docker
### Install Docker
1. First, in order to ensure the downloads are valid, add the GPG key for the official Docker repository to your system:  
`curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -`
2. Add the Docker repository to APT sources:  
`sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"`
3. Next, update the package database with the Docker packages from the newly added repo:  
`sudo apt-get update`
4. Make sure you are about to install from the Docker repo instead of the default Ubuntu 16.04 repo:  
`apt-cache policy docker-ce`  
 You should see output similar to the follow:  
```
docker-ce:
    Candidate: 18.06.1~ce~3-0~ubuntu
    Version table:
        18.06.1~ce~3-0~ubuntu 500
            500 https://download.docker.com/linux/ubuntu xenial/stable amd64 Packages
```
5. Finally, install Docker:  
`sudo apt-get install -y docker-ce`
6. Docker should now be installed, the daemon started, and the process enabled to start on boot. Check that it's running:  
`sudo systemctl status docker`
7. If you want to avoid typing sudo whenever you run the docker command, add your username to the docker group:  
`sudo usermod -aG docker ${USER}`
8. To apply the new group membership, log out of the server and back in.
9. Afterwards, you can confirm that your user is now added to the docker group by typing:  
`id -nG`  

- For more info, visit: https://www.digitalocean.com/community/tutorials/how-to-install-and-use-docker-on-ubuntu-16-04
### Install Docker Compose
1. We'll check the current release and if necessary, update it in the command below:  
`ssudo curl -L "https://github.com/docker/compose/releases/download/1.24.1/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose`
2. Next we'll set the permissions:  
`sudo chmod +x /usr/local/bin/docker-compose`
3. Then we'll verify that the installation was successful by checking the version:  
`docker-compose -v`

- For more info, visit: https://www.digitalocean.com/community/tutorials/how-to-install-docker-compose-on-ubuntu-16-04
### Execute all the services
`docker-compose up -d` or `docker-compose up -d --build --force-recreate` to force recreation of image and container  
Check if everything is working via the commands:  
`docker-compose ps` or `docker ps`  
Check the logs of each container via the command:  
`docker-compose logs container_name`

**Tested on Python3**
