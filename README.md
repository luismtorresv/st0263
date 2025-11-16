# ST0263 — 2025-2

## Instalación de Docker en Ubuntu:22.04

### Instalación

```shell
sudo apt update
sudo apt install docker.io -y
sudo apt install docker-compose -y
```

### Activación de servicios

```shell
sudo systemctl enable docker
sudo systemctl start docker
```

### Permisos

#### Para AWS

```shell
sudo usermod -a -G docker ubuntu
```

#### Para GCP

Cuando crea las VM, GCP le asigna el username que tiene usted en GCP.
Fíjese en este y cámbielo así:

```shell
sudo usermod -a -G docker ${USERNAME}
```
