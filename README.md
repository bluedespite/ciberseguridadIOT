# Laboratorio IoT: Nmap + SSH de prueba (Ubuntu WSL + Podman)

Este laboratorio está pensado para una demostración docente, local y autorizada.
Crea dos contenedores en una red Podman `internal`: `scanner` (Nmap) y `target`
(servidor SSH con una cuenta ficticia). No publica puertos en Windows ni en el host.

La contraseña débil `class-demo-2026` y las listas pequeñas son SOLO para este laboratorio.
No reutilices estas credenciales ni ejecutes pruebas contra sistemas ajenos.

## 1. Requisitos en Ubuntu WSL

Instala Podman y un proveedor de Compose. En Ubuntu, según la versión disponible:

```bash
sudo apt update
sudo apt install -y podman podman-compose
```

Comprueba:

```bash
podman --version
podman-compose --version
```

Si tu instalación usa el subcomando integrado, también puedes probar `podman compose`.
Los comandos de abajo usan `podman-compose`.

## 2. Construir y levantar

Desde la carpeta que contiene `compose.yaml`:

```bash
podman-compose build
podman-compose up -d
podman-compose ps
```

## 3. Práctica A: descubrimiento de servicio

```bash
podman-compose run --rm scanner nmap --unpriviliged -sT -sV -p 22 target
```

Resultado esperado: puerto 22/tcp abierto y servicio OpenSSH identificado.

## 4. Práctica B: demostración acotada de autenticación SSH

La lista tiene solo tres contraseñas ficticias y el usuario es `iotlab`.
El script NSE `ssh-brute` probará únicamente esas entradas contra el contenedor `target`:

```bash
podman-compose run --rm scanner nmap --unpriviliged -sT -p 22 \
  --script ssh-brute \
  --script-args userdb=/lab/users.txt,passdb=/lab/passwords.txt \
  target
```

El resultado depende de la versión de Nmap/OpenSSH y de la configuración de autenticación.
No aumentes la lista ni cambies el objetivo a una IP externa.

## 5. Ver los registros del servidor

En otra terminal:

```bash
podman logs -f iot-lab-target
```

Vuelve a ejecutar la práctica B y observa los intentos de autenticación. Si la imagen/versión no emite todos los eventos por `podman logs`, entra al contenedor y revisa los registros disponibles:

```bash
podman exec -it iot-lab-target bash
```

## 6. Preguntas para el alumnado

- ¿Qué información revela el escaneo `-sV`?
- ¿Por qué una contraseña débil permite que un ataque de diccionario tenga éxito?
- ¿Qué cambia al deshabilitar la autenticación por contraseña y usar claves SSH?
- ¿Por qué no se debe exponer SSH a redes que no necesitan acceder al servicio?

## 7. Limpieza

```bash
podman-compose down
```

Para borrar también las imágenes creadas por este laboratorio:

```bash
podman rmi localhost/lab-iot-podman-wsl-scanner localhost/lab-iot-podman-wsl-target
```

Si los nombres exactos difieren, consulta `podman images` y elimina solo las imágenes de este laboratorio.

## Nota sobre WSL

La red `internal: true` limita el tráfico a la red del laboratorio y no se publica ningún puerto al host.
La construcción inicial necesita descargar imágenes y paquetes; una vez construido, los contenedores no necesitan acceso externo para esta práctica.
