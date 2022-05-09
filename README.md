# set-up-docker-environment-in-ubuntu

You can install Docker Engine in different ways, depending on your needs:

Most users set up Docker’s repositories and install from them, for ease of installation and upgrade tasks. This is the recommended approach.

Some users download the DEB package and install it manually and manage upgrades completely manually. This is useful in situations such as installing Docker on air-gapped systems with no access to the internet.

In testing and development environments, some users choose to use automated convenience scripts to install Docker.

# Update
~~~
sudo apt-get update
~~~

# Add CA, Curl, GNUPG & LSB
~~~
sudo apt-get install \
    ca-certificates \
    curl \
    gnupg \
    lsb-release
~~~

# Add Docker’s official GPG key
~~~
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg
~~~

# set up the stable repository
~~~
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu \
  $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
~~~

# Update ubunbu & Install Docker Engine
~~~
sudo apt-get update
sudo apt-get install docker-ce docker-ce-cli containerd.io docker-compose-plugin
~~~

# Now check Docker Version
~~~
docker version
~~~

# Install Docker Compose Version 2
~~~
sudo curl -L https://github.com/docker/compose/releases/download/2.2.3/docker-compose-`uname -s`-`uname -m` -o /usr/local/bin/docker-compose
~~~

# Next set the permissions:
~~~
sudo chmod +x /usr/local/bin/docker-compose
~~~

# Now check Docker Compose Version
~~~
docker compose version
~~~

# Install Lazydocker
Get the latest version tag of Lazydocker release from GitHub. Assign version tag to variable:

~~~
LAZYDOCKER_VERSION=$(curl -s "https://api.github.com/repos/jesseduffield/lazydocker/releases/latest" | grep -Po '"tag_name": "v\K[0-9.]+')
~~~

Download archive from releases page of the Lazydocker repository:

~~~
curl -Lo lazydocker.tar.gz "https://github.com/jesseduffield/lazydocker/releases/latest/download/lazydocker_${LAZYDOCKER_VERSION}_Linux_x86_64.tar.gz"
~~~

Unzip archive:
~~~
mkdir lazydocker-temp
tar xf lazydocker.tar.gz -C lazydocker-temp
~~~
Move binary file to /usr/local/bin directory:
~~~
sudo mv lazydocker-temp/lazydocker /usr/local/bin
~~~
Now lazydocker can be used as a system-wide command for all users.

We can check Lazydocker version:
~~~
lazydocker --version
~~~
Archive and temporary directory is no longer necessary, remove them:
~~~
rm -rf lazydocker.tar.gz
rm -rf lazydocker-temp
~~~
Testing Lazydocker
Run hello-world image inside of a container:
~~~
sudo docker run hello-world
~~~
Start Lazydocker:
~~~
sudo lazydocker
~~~

# Uninstall Lazydocker
If you decided to completely remove Lazydocker, delete the binary file:
~~~
sudo rm -rf /usr/local/bin/lazydocker
~~~
You can also remove Lazydocker config directory:
~~~
rm -rf ~/.config/lazydocker
~~~

# Install buildkit on docker on ubuntu¶
Follow the official installation instructions from https://docs.docker.com/compose/install/ to install docker compose on your linux machine.

~~~
git clone https://github.com/michaelmcandrew/civicrm-buildkit-docker.git
cd civicrm-buildkit-docker
sudo docker compose up -d
~~~
Now you are ready to go.

To create a new site with buildkit run the following command:

~~~
sudo docker compose exec -u buildkit civicrm civibuild create dmaster --url http://localhost:8080
~~~
Alternative you can login into the conatiner and run the commands from there:

~~~
sudo docker compose exec -u buildkit civicrm bash
~~~

# Create a Daemon file
~~~
sudo touch /etc/docker/daemon.json
~~~
Give Permission
~~~
sudo chmod -R 777 /etc/docker/daemon.json
~~~
Go to /etc/docker/ folder & open daemon.json file, Copy the following code, paste in daemon.json file & save it:
~~~
{ "features": { "buildkit": true } }
~~~

# Give Permission to docker
~~~
sudo usermod -aG docker $USER
~~~
 
Now You Have to 'Restart' Your PC

# For run docker compose & lazydocker
~~~
docker compose up --build -d && lazydocker  
~~~

# For run a specific container docker compose & lazydocker
~~~
docker compose build <container name> && docker compose up -d && lazydocker  
~~~

# Stop Docker compose:
~~~
docker compose down
~~~

# stop and remove all the unused container
~~~
docker system prune
~~~