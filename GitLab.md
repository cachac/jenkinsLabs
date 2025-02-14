# 05. Gitlab <!-- omit in toc -->
# 1. Pre-requisitos
- 8 GB
- 4 CPU
- 30 GB disco

> config: /etc/gitlab/gitlab.rb

# 2. Instalar con APT
- [Link](https://about.gitlab.com/install/#ubuntu)

```sh
sudo apt update && sudo apt upgrade -y
sudo apt-get install -y curl openssh-server ca-certificates tzdata perl
sudo apt-get install -y postfix

curl https://packages.gitlab.com/install/repositories/gitlab/gitlab-ce/script.deb.sh | sudo bash

# Cambiar la ip publica
sudo EXTERNAL_URL="https://<PUBLIC_IP>" apt-get install gitlab-ce
```
Ejemplo: sudo EXTERNAL_URL="https://34.75.156.69" apt-get install gitlab-ce

## 2.1. Password:
```sh
cat /etc/gitlab/initial_root_password
```

## 2.2. Opcional: Cambiar la contraseña
```sh
gitlab-rake "gitlab:password:reset[root]"
```

## 2.3. Comandos básicos de administración
```sh
sudo systemctl status gitlab-runsvdir
sudo gitlab-ctl status
sudo gitlab-ctl tail
sudo gitlab-ctl restart
```

# 3. Opcional: usando Docker Compose
## 3.1. Agregar las variables
```sh
sudo mkdir -p /srv/gitlab
￼
export GITLAB_HOME=/srv/gitlab
￼
echo "export GITLAB_HOME=/srv/gitlab" >> ~/.bashrc
```

## 3.2. Instalar Docker Compose
```sh
sudo apt-get update
sudo apt-get install docker-compose-plugin
```

## 3.3. Ejecutar
- docker-compose.yaml
```yaml
services:
  gitlab:
    image: gitlab/gitlab-ce:latest
    container_name: gitlab
    restart: always
    hostname: 'gitlab.example.com'
    environment:
      GITLAB_OMNIBUS_CONFIG: |
        external_url 'https://gitlab.example.com'
			GITLAB_ROOT_PASSWORD: 'MJqaiNBuSZPAC6YL'
			GITLAB_ROOT_EMAIL: 'root@local.com'
    ports:
      - '80:80'
      - '443:443'
      - '2200:22'
    volumes:
      - '$GITLAB_HOME/config:/etc/gitlab'
      - '$GITLAB_HOME/logs:/var/log/gitlab'
      - '$GITLAB_HOME/data:/var/opt/gitlab'
    shm_size: '256m'
```

```sh
docker compose up -d
docker compose ps
```

## 3.4. Opcional: resetear el password del usuario root
```sh
docker exec -it gitlab bash
gitlab-rake "gitlab:password:reset[root]"
```

# 4. Ingresar con el usuario root en <IP> puerto 80

# 5. Agregar las llaves SSH
## 5.1. Crear la llave en la VM
```sh
ssh-keygen -q -N "" -C "user@mail.com"

cat /home/storylabscr/.ssh/id_ed25519.pub
```
## 5.2. Agregarla a GitLab
- Avatar - edit profile
- SSH keys
- pegar la llave SSH
- Title: demo

# 6. Crear el Proyecto
- Create blank project
- Project name: public-api
- Project URL: root
- Visibility Level: Private
- Initialize repository with a README: false
- Create project

## 6.1. Subir el código
Desde la VM
```sh
mkdir public-api
cd public-api

git config --global user.name "Administrator"
git config --global user.email "user@example.com"

git init --initial-branch=main
```

## 6.2. Clonar y subir el código
```sh
git clone https://github.com/Storylabs-Learning/public-api-demo.git
mv public-api-demo/* .
rm -rf public-api-demo

git remote add origin git@<PUBLIC_IP>:root/public-api.
git add .
git commit -m "Initial commit"
git push --set-upstream origin main
```
