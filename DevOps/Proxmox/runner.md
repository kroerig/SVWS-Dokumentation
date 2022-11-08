# Gitlab Runner

## Hardware

+ 2 cpu
+ 16 GB (=16384MB) Ram
+ 2 GB swap
+ 250 GB HD

## Installation 

Die Anleitung findet man in dem Gitlabserver unter: 

		apt get update
		apt update
		apt install -y net-tools dnsutils curl
		curl -L "https://packages.gitlab.com/install/repositories/runner/gitlab-runner/script.deb.sh" | bash
		apt update
		apt install -y gitlab-runner
		gitlab-runner status
   
Des Weiteren eine gute Dokumentation des Funktionsumfange und der Einstellungsmögliochkeiten unter: 

https://docs.gitlab.com/runner/configuration/advanced-configuration.html
   
## Registrierung

		gitlab-runner register --url https://git.svws-nrw.de/ --registration-token XYZ....
		
Der Token kann unter Gitlab im Adminbereich -> Runners unter "Register an instance runner" kopiert werden. Bei der installation kann man custom, docker, ... auswählen. 
Hier sollte docker genommen werden, wenn man dies benötigt. Die Tags können auch im Nachhinein in der Gitoberfläche gesetzt werden. 

## Nacharbeiten 

Hat man nun custom statt docker bei dem Installationsscript gewählt, dann helfen offensichtlich die folgenden Nacharbeiten für docker:

		apt install -y ca-certificates curl gnupg lsb-release
		mkdir -p /etc/apt/keyrings
		curl -fsSL https://download.docker.com/linux/debian/gpg | gpg --dearmor -o /etc/apt/keyrings/docker.gpg
		echo   "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/debian $(lsb_release -cs) stable" | tee /etc/apt/sources.list.d/docker.list > /dev/null
		apt update
		apt install -y docker-ce docker-ce-cli containerd.io docker-compose-plugin
		# docker run hello-world
		
Nachdem man die Docker Installation abgeschlossen hat, muss unter /etc/gitlab-runner/config-toml im unteren Teil der Datei die folgenden Einträge haben


```
...
  executor = "docker"
  [runners.custom_build_dir]
  [runners.cache]
    [runners.cache.s3]
    [runners.cache.gcs]
    [runners.cache.azure]
  [runners.docker]
    tls_verify = false
    image = "eclipse-temurin:17"
    privileged = false
    disable_entrypoint_overwrite = false
    com_kill_disable = false
    disable_cache = false
    volumes = ["/cache"]
    shm_size = 0
	
```