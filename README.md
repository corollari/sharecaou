# sharecaou
> ShareLaTeX for caoutchouc

This guides assumes usage of Ubuntu 18.04 LTS.

## Setup
### 1. Install Docker (see the [docker guide](https://docs.docker.com/install/linux/docker-ce/ubuntu/))
```bash
sudo apt-get update
sudo apt-get install \
    apt-transport-https \
    ca-certificates \
    curl \
    gnupg-agent \
    software-properties-common
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
sudo add-apt-repository \
   "deb [arch=amd64] https://download.docker.com/linux/ubuntu \
   $(lsb_release -cs) \
   stable"
sudo apt-get update
sudo apt-get install docker-ce docker-ce-cli containerd.io
# Also install docker-compose
sudo apt-get install docker-compose
```

### 2. Install sharelatex
```bash
docker pull sharelatex/sharelatex
wget https://raw.githubusercontent.com/overleaf/overleaf/master/docker-compose.yml
sudo docker-compose up
```

### 3. Update texlive
```bash
# Enter the container
docker ps # GET the docker id of the sharelatex container
docker exec -it $SHARELATEXID bash # Replace $SHARELATEXID with the id obtained in the last step

# Update texlive
wget http://mirror.ctan.org/systems/texlive/tlnet/update-tlmgr-latest.sh
bash update-tlmgr-latest.sh
tlmgr install scheme-full
```

### 4. Update node
```bash
curl -sL https://deb.nodesource.com/setup_12.x | sudo -E bash -
sudo apt-get install -y nodejs
```

### 5. Install caou
```bash
sudo npm install -g caou
```

### 6. Make sharelatex use caou
We assume that you are inside the container, run `docker exec -it $SHARELATEXID bash` if not
  1. Open `/var/www/sharelatex/clsi/` with an editor:
```bash
vi /var/www/sharelatex/clsi/
```
  2. Find the following line:
  
  3. Replace with this line:
  
  4. Open `/var/www/sharelatex/clsi/` with an editor:

### 7. Restart the container

### 8. Set up admin account
Open a web browser and visit $IP/launchpad

## Resources
- https://www.scaleway.com/en/docs/installing-sharelatex-ubuntu/
- https://github.com/overleaf/docker-image/issues/102
- https://docs.docker.com/install/linux/docker-ce/ubuntu/
- https://www.tug.org/texlive/tlmgr.html
