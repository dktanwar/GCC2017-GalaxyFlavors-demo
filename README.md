# GCC2017-GalaxyFlavors-demo
Galaxy2017 Hackathon Galaxy Flavors demo in Ubuntu 16.04 in Docker

Step by step done:
Platform for Galaxy flavors hackathon is Ubuntu 16.04 VM running inside a Windows 7 Pro host machine.

Step 1: Install docker... from Docker Site (https://docs.docker.com/engine/installation/linux/ubuntu/#install-using-the-repository), follow instructions to the letter 

Step 2: Specific to my VM, move the docker default container install directory to the mounted drive with more space...
default: /var/lib/docker
to... : /mnt/12g_sata/docker
from: https://forums.docker.com/t/how-do-i-change-the-docker-image-installation-directory/1169

$ sudo tar -zcC /var/lib docker > /mnt/12g_sata/docker/var_lib_docker-backup-$(date +%s).tar.gz
$ mv /var/lib/docker /mnt/12g_sata/
$ ln -s /mnt/12g_sata/docker /var/lib/docker

Step 3: pull in the galaxy-stable docker image-installation-directory

$ sudo docker search galaxy (lists the specific galaxy container to pull)
$ sudo docker pull bgruening/galaxy-stable

Then run docker container (as daemon) ...

$ sudo docker run -d -p 8080:80 -p 8021:21 -p 8022:22 bgruening/galaxy-stable

From... https://github.com/bgruening/docker-galaxy-stable/blob/master/README.md
TO view the running Galaxy container in your host machine, point web browser to localhost:8080
Success = see the running Galaxy in localhost :8080

Step 4: add a tool in this running Galaxy using toolshed:
Log-in as Galaxy admin of this container, info from here ...https://hub.docker.com/r/bgruening/galaxy-stable/~/dockerfile/
 Galaxy Admin User 'admin@galaxy.org' with password 'admin'
 
Add a tool from toolshed (admin ->Tools and Toolshed -> search toolshed, then select a tool to install, follow instruction screen)
Installed oghma (genome prediction) tool from Nicolas Beaume (IRRI), pulled from tooklshed and installed into a separate section (Oghma), successful.

After tool is successfully installed, the main exercise begins!!!

Creating a new Galaxy flavor and pushing to Dockerhub

Supplemental step (extra): interacting with the running galaxy docker

$ sudo docker ps (gives running container ID to be used in next command)
$ sudo docker exec -it <galaxy-con-ID> bash (gives interactive bash shell in the container)

Step 1: Edit galaxy.ini in /etc/galaxy to activate webhooks (uncomment it, append /demo...)
webhooks_dir = config/plugins/webhooks

Step 2: verify if <activate = true> in searchover.yaml ...
$ cd /galaxy-central/config/plugins/webhooks/demo/search/config/
$ less searchover.yaml 
   look for tag activate = true

Step 3: if all good, restart galaxy from command line

$ supervisorctl restart galaxy:

A new tool icon (tool-list) in now visible on the pane. 

Step 4. Click on tool-list tool to see the YAML file of the newly added tool (oghma). Then saved the contents of the YAML to an external YAML file.

Step 6. Created a new Dockefile based on galaxy-stable and yaml file and pushed these 2 files into this Github account (https://github.com/maumauleon/GCC2017-GalaxyFlavors-demo, along with this README file.

Step 7. From my Dockerhub account, created this new Docker container repository, then linked this to the correspondin Github repository.
Then did manual trigger from Dockerhub repo to update the contents from linked Github repository.

Step 8. Pulled this new Docker container into the same VM, as new image, and started the container using the same run commnands parameter of galaxy-stable container. 

The container ran successfully but an issue is that the tools imported from the toolshed is not inside the tool section, it's installed outside.

