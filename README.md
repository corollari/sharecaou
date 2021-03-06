# sharecaou
> ShareLaTeX for caoutchouc

This guides assumes usage of Ubuntu 18.04 LTS.


## Quickstart
Once you have installed Docker and docker-compose (see the [instructions on the Build Section](https://github.com/corollari/sharecaou#1-install-docker-see-the-docker-guide)), run:
```
wget https://raw.githubusercontent.com/corollari/sharecaou/master/docker-compose.yml
docker-compose up
```

## Build
If you'd prefer to build it directly from the sharelatex container follow these instrutions:
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
sudo apt-get update
sudo apt-get install texlive-full
```

### 4. Install pandoc
```bash
wget https://github.com/jgm/pandoc/releases/download/2.2.1/pandoc-2.2.1-1-amd64.deb && sudo dpkg -i pandoc-2.2.1-1-amd64.deb
```

### 5. Install caou
```bash
curl -s https://api.github.com/repos/corollari/caoutchouc/releases/latest | grep "browser_download_url.*linux" | cut -d : -f 2,3 | tr -d \" | wget -qi -
chmod +x caou-linux
sudo mv caou-linux /usr/bin/caou
chmod +x caou-pandoc-filter-linux
sudo mv caou-pandoc-filter-linux /usr/bin/caou-pandoc-filter
```

### 6. Make sharelatex use caou
We assume that you are inside the container, run `docker exec -it $SHARELATEXID bash` if not
  1. Open `/var/www/sharelatex/clsi/app/coffee/LatexRunner.coffee` with an editor:
```bash
vi /var/www/sharelatex/clsi/app/coffee/LatexRunner.coffee
```
  2. Find the following line:
```
args = ["latexmk", "-cd", "-f", "-jobname=output", "-auxdir=$COMPILE_DIR", "-outdir=$COMPILE_DIR", "-synctex=1","-interaction=batchmode"]
```
  3. Replace with this line:
```
args = ["caou", "--tex", "latexmk", "-cd", "-f", "-jobname=output", "-auxdir=$COMPILE_DIR", "-outdir=$COMPILE_DIR", "-synctex=1","-interaction=batchmode"]
```
  4. Open `/var/www/sharelatex/clsi/app/js/LatexRunner.js` with an editor:
```bash
vi /var/www/sharelatex/clsi/app/js/LatexRunner.js
```
  5. Find the following line:
```
    _latexmkBaseCommand: ((Settings != null ? (_ref1 = Settings.clsi) != null ? _ref1.latexmkCommandPrefix : void 0 : void 0) || []).concat(["latexmk", "-cd", "-f", "-jobname=output", "-auxdir=$COMPILE_DIR", "-outdir=$COMPILE_DIR", "-synctex=1", "-interaction=batchmode"]),
```
  6. Replace with this line:
```
    _latexmkBaseCommand: ((Settings != null ? (_ref1 = Settings.clsi) != null ? _ref1.latexmkCommandPrefix : void 0 : void 0) || []).concat(["caou", "--tex", "latexmk", "-cd", "-f", "-jobname=output", "-auxdir=$COMPILE_DIR", "-outdir=$COMPILE_DIR", "-synctex=1", "-interaction=batchmode"]),
```

### 7. Commit docker image
```
docker commit $SHARELATEXID corollari/sharecaou
docker push corollari/sharecaou
```

### 8. Set up admin account
Open a web browser and visit `$IP/launchpad` to create the administrator's account.

## Resources
- https://www.scaleway.com/en/docs/installing-sharelatex-ubuntu/
- https://github.com/overleaf/docker-image/issues/102
- https://docs.docker.com/install/linux/docker-ce/ubuntu/
- https://www.tug.org/texlive/tlmgr.html
