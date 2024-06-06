# TP1 : Premiers pas Docker

# I. Init



## 3. sudo c pa bo



🌞 **Ajouter votre utilisateur au groupe `docker`**

```bash
[clem@dockervm ~]$ sudo usermod -aG docker ${USER}
[sudo] password for clem:
[clem@dockervm ~]$ newgrp docker
[clem@dockervm ~]$ docker ps
CONTAINER ID   IMAGE     COMMAND   CREATED   STATUS    PORTS     NAMES
```
## 4. Un premier conteneur en vif

🌞 **Lancer un conteneur NGINX**


```bash
[clem@dockervm ~]$ docker run -d -p 9999:80 nginx
Unable to find image 'nginx:latest' locally
latest: Pulling from library/nginx
2f44b7a888fa: Pull complete
8b7dd3ed1dc3: Pull complete
35497dd96569: Pull complete
36664b6ce66b: Pull complete
2d455521f76c: Pull complete
dc9c4fdb83d6: Pull complete
8056d2bcf3b6: Pull complete
Digest: sha256:4c0fdaa8b6341bfdeca5f18f7837462c80cff90527ee35ef185571e1c327beac
Status: Downloaded newer image for nginx:latest
3ff6f2fc96960647503a2ddd0e3062e2142bab35804a23643ea44aef8d5c3560
```

🌞 **Visitons**

```bash
[clem@dockervm ~]$ docker ps
CONTAINER ID   IMAGE     COMMAND                  CREATED              STATUS              PORTS
           NAMES
3ff6f2fc9696   nginx     "/docker-entrypoint.…"   About a minute ago   Up About a minute   0.0.0.0:9999->80/tcp, :::9999->80/tcp   optimistic_jones
[clem@dockervm ~]$ docker logs optimistic_jones
/docker-entrypoint.sh: /docker-entrypoint.d/ is not empty, will attempt to perform configuration
[...]
2024/01/25 13:59:35 [notice] 1#1: start worker process 29
[clem@dockervm ~]$ docker inspect optimistic_jones
[
    {
        "Id": "3ff6f2fc96960647503a2ddd0e3062e2142bab35804a23643ea44aef8d5c3560",
        "Created": "2024-01-25T13:59:34.98529045Z",
        "Path": "/docker-entrypoint.sh",
        "Args": [
            "nginx",
            "-g",
            "daemon off;"
        ],
        "State": {
            "Status": "running",
[...]
                    "IPAddress": "172.17.0.2",
                    "IPPrefixLen": 16,
                    "IPv6Gateway": "",
                    "GlobalIPv6Address": "",
                    "GlobalIPv6PrefixLen": 0,
                    "DriverOpts": null,
                    "DNSNames": null
                }
            }
        }
    }
]
[clem@dockervm ~]$ sudo ss -lnpt
[sudo] password for clem:
State         Recv-Q        Send-Q               Local Address:Port                Peer Address:Port        Process
LISTEN        0             4096                       0.0.0.0:9999                     0.0.0.0:*            users:(("docker-proxy",pid=3783,fd=4))
LISTEN        0             128                        0.0.0.0:22                       0.0.0.0:*            users:(("sshd",pid=718,fd=3))
LISTEN        0             4096                          [::]:9999                        [::]:*            users:(("docker-proxy",pid=3789,fd=4))
LISTEN        0             128                           [::]:22                          [::]:*            users:(("sshd",pid=718,fd=4))
[clem@dockervm ~]$ sudo firewall-cmd --add-port=9999/tcp --permanent
[sudo] password for clem:
success
```


🌞 **On va ajouter un site Web au conteneur NGINX**


```bash
[clem@dockervm nginx]$ sudo cat index.html
<h1>MEOOOW</h1>
[clem@dockervm nginx]$ sudo cat site_nul.conf
server {
    listen        8080;

    location / {
        root /var/www/html;
    }
}
```


- lancez le conteneur avec la commande en dessous, notez que :
  - on partage désormais le port 8080 du conteneur (puisqu'on l'indique dans la conf qu'il doit écouter sur le port 8080)
  - on précise les chemins des fichiers en entier
  - note la syntaxe du `-v` : à gauche le fichier à partager depuis ta machine, à droite l'endroit où le déposer dans le conteneur, séparés par le caractère `:`
  - c'est long putain comme commande

```bash
docker run -d -p 9999:8080 -v /home/<USER>/nginx/index.html:/var/www/html/index.html -v /home/<USER>/nginx/site_nul.conf:/etc/nginx/conf.d/site_nul.conf nginx
```

🌞 **Visitons**

```bash
[clem@dockervm nginx]$ docker ps
CONTAINER ID   IMAGE     COMMAND                  CREATED          STATUS          PORTS                                   NAMES
3ff6f2fc9696   nginx     "/docker-entrypoint.…"   58 minutes ago   Up 58 minutes   0.0.0.0:9999->80/tcp, :::9999->80/tcp   optimistic_jones
[clem@dockervm nginx]$ curl http://10.1.1.9:9999
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
<style>
html { color-scheme: light dark; }
body { width: 35em; margin: 0 auto;
font-family: Tahoma, Verdana, Arial, sans-serif; }
</style>
</head>
<body>
<h1>Welcome to nginx!</h1>
<p>If you see this page, the nginx web server is successfully installed and
working. Further configuration is required.</p>

<p>For online documentation and support please refer to
<a href="http://nginx.org/">nginx.org</a>.<br/>
Commercial support is available at
<a href="http://nginx.com/">nginx.com</a>.</p>

<p><em>Thank you for using nginx.</em></p>
</body>
</html>
```


## 5. Un deuxième conteneur en vif



🌞 **Lance un conteneur Python, avec un shell**


```bash
[clem@dockervm nginx]$ docker run -it python bash
Unable to find image 'python:latest' locally
latest: Pulling from library/python
1b13d4e1a46e: Pull complete
1c74526957fc: Pull complete
30d855997954: Pull complete
ad5739181616: Pull complete
75e2b45cbee5: Pull complete
fa6efb383f81: Pull complete
0c6213e9c29b: Pull complete
1b1e18053fdc: Pull complete
Digest: sha256:a09f71f4af992ddf9a620330fed343c850c371251be45c3f9bb46ebeca49c9c6
Status: Downloaded newer image for python:latest
root@79b4ab1061f4:/#
```


🌞 **Installe des libs Python**

```bash
root@82ffa4cc5985:/# python
Python 3.12.1 (main, Jan 17 2024, 06:18:08) [GCC 12.2.0] on linux
Type "help", "copyright", "credits" or "license" for more information.
>>> import aiohttp
>>>
```

# II. Images

## 1. Images publiques

🌞 **Récupérez des images**

```bash
[clem@dockervm nginx]$ docker pull python:3.11
3.11: Pulling from library/python
1b13d4e1a46e: Already exists
1c74526957fc: Already exists
30d855997954: Already exists
ad5739181616: Already exists
75e2b45cbee5: Already exists
ee0d5ad99e90: Pull complete
a6382b418b16: Pull complete
31c0f7a3374e: Pull complete
Digest: sha256:a2f76e7c73c241d15e867987f143dfbdcf280fb229ae1ecde98850504fd3b234
Status: Downloaded newer image for python:3.11
docker.io/library/python:3.11
[clem@dockervm nginx]$ docker pull mysql:5.7
5.7: Pulling from library/mysql
20e4dcae4c69: Pull complete
1c56c3d4ce74: Pull complete
e9f03a1c24ce: Pull complete
68c3898c2015: Pull complete
6b95a940e7b6: Pull complete
90986bb8de6e: Pull complete
ae71319cb779: Pull complete
ffc89e9dfd88: Pull complete
43d05e938198: Pull complete
064b2d298fba: Pull complete
df9a4d85569b: Pull complete
Digest: sha256:4bc6bc963e6d8443453676cae56536f4b8156d78bae03c0145cbe47c2aad73bb
Status: Downloaded newer image for mysql:5.7
docker.io/library/mysql:5.7
[clem@dockervm nginx]$ docker pull wordpress
Using default tag: latest
latest: Pulling from library/wordpress
2f44b7a888fa: Already exists
3a95dcec6035: Pull complete
e22afa33f327: Pull complete
1aa61ea11ee8: Pull complete
ea67ae2bde33: Pull complete
ff5356c6468f: Pull complete
5e91cb51e886: Pull complete
935e88b21d5f: Pull complete
8202e883ada1: Pull complete
be40d3507d33: Pull complete
5fac4c1a6ab6: Pull complete
03fcb105f703: Pull complete
87c6b0d7fa4d: Pull complete
7b595acbbcdc: Pull complete
9097ed91daa0: Pull complete
4796aa9f1a89: Pull complete
8edd6604ce7d: Pull complete
9b37f9713fe2: Pull complete
581954acac8f: Pull complete
6073502d7819: Pull complete
879585ec90c2: Pull complete
Digest: sha256:fb401b91fa9dedc1b04095e55debd6a593e77a68e346e761010f39ccbb0247e7
Status: Downloaded newer image for wordpress:latest
docker.io/library/wordpress:latest
[clem@dockervm nginx]$ docker pull linuxserver/wikijs
Using default tag: latest
latest: Pulling from linuxserver/wikijs
dd1be521b473: Pull complete
c36572129e15: Pull complete
d5671ec7fd78: Pull complete
ee400e5ea442: Pull complete
3bc6c9425316: Pull complete
64f5d515c28d: Pull complete
3110705d5d35: Pull complete
Digest: sha256:676ebb477738d03d6d349a69e23189b48b7388bde4e29b7338df8b9b84997d4e
Status: Downloaded newer image for linuxserver/wikijs:latest
docker.io/linuxserver/wikijs:latest
[clem@dockervm nginx]$ docker ps -a
CONTAINER ID   IMAGE         COMMAND                  CREATED        STATUS                    PORTS                                   NAMES
79b4ab1061f4   python        "bash"                   22 hours ago   Exited (0) 22 hours ago                                           ecstatic_payne
63799201a7b6   nginx         "/docker-entrypoint.…"   22 hours ago   Created                                                           naughty_kowalevski
3ff6f2fc9696   nginx         "/docker-entrypoint.…"   23 hours ago   Up 23 hours               0.0.0.0:9999->80/tcp, :::9999->80/tcp   optimistic_jones
50a403fc4ee4   hello-world   "/hello"                 23 hours ago   Exited (0) 23 hours ago                                           nice_euler
```

🌞 **Lancez un conteneur à partir de l'image Python**


```bash
[clem@dockervm nginx]$ python
Python 3.9.18 (main, Sep  7 2023, 00:00:00)
[GCC 11.4.1 20230605 (Red Hat 11.4.1-2)] on linux
Type "help", "copyright", "credits" or "license" for more information.
>>>
```

## 2. Construire une image


🌞 **Ecrire un Dockerfile pour une image qui héberge une application Python**

- l'image doit contenir
  - une base debian (un `FROM`)
  - l'installation de Python (un `RUN` qui lance un `apt install`)
    - il faudra forcément `apt update` avant
    - en effet, le conteneur a été allégé au point d'enlever la liste locale des paquets dispos
    - donc nécessaire d'update avant de install quoique ce soit
  - l'installation de la librairie Python `emoji` (un `RUN` qui lance un `pip install`)
  - ajout de l'application (un `COPY`)
  - le lancement de l'application (un `ENTRYPOINT`)
- le code de l'application :

```python
import emoji

print(emoji.emojize("Cet exemple d'application est vraiment naze :thumbs_down:"))
```

- pour faire ça, créez un dossier `python_app_build`
  - pas n'importe où, c'est ton Dockerfile, ton caca, c'est dans ton homedir donc `/home/<USER>/python_app_build`
  - dedans, tu mets le code dans un fichier `app.py`
  - tu mets aussi `le Dockerfile` dedans

> *J'y tiens beaucoup à ça, comprenez que Docker c'est un truc que le user gère. Sauf si vous êtes un admin qui vous en servez pour faire des trucs d'admins, ça reste dans votre `/home`. Les dévs quand vous bosserez avec Windows, vous allez pas stocker vos machins dans `C:/Windows/System32/` si ? Mais plutôt dans `C:/Users/<TON_USER>/TonCaca/` non ? Alors pareil sous Linux please.*

🌞 **Build l'image**

- déplace-toi dans ton répertoire de build `cd python_app_build`
- `docker build . -t python_app:version_de_ouf`
  - le `.` indique le chemin vers le répertoire de build (`.` c'est le dossier actuel)
  - `-t python_app:version_de_ouf` permet de préciser un nom d'image (ou *tag*)
- une fois le build terminé, constater que l'image est dispo avec une commande `docker`

🌞 **Lancer l'image**

- lance l'image avec `docker run` :

```bash
docker run python_app:version_de_ouf
```

# III. Docker compose

[Document dédié à l'utilisation de `docker-compose`.](./compose.md)

