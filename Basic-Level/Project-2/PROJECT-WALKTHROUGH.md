Step-1 : Deploymwnt of LAMP workpress app on Localhost Server.
For this we are using subscription of Kodekloud practice lab. I will practice all devops here here only.

I am using my website blog post for such practical: https://projectwala.site/deploy-wordpress-mariadb-php-apache-web-server-on-rhel9-with-easy-steps 

So kindly follow this blog post to deployment of LAMP wordpress application on Local Server

Images of LAMP wordpress application deployment below:
---
> <img width="1365" height="549" alt="image" src="https://github.com/user-attachments/assets/d052dac1-62c0-4b96-acf7-74e2e0347002" />

> <img width="1365" height="693" alt="image" src="https://github.com/user-attachments/assets/a88040ed-b83a-4479-9690-b65ee391c96d" />

> <img width="1168" height="302" alt="image" src="https://github.com/user-attachments/assets/1de22d53-106c-4f24-a5cd-ab51522e6c54" />

> <img width="1081" height="453" alt="image" src="https://github.com/user-attachments/assets/f6389ac3-2a5b-4a42-8924-319944e63313" />

> <img width="955" height="104" alt="image" src="https://github.com/user-attachments/assets/7dc5aa44-9a01-43df-9f31-dbb6cad2a872" />

> <img width="822" height="180" alt="image" src="https://github.com/user-attachments/assets/e534080a-32bc-443b-bcc5-b05bda50f533" />

---

Step-2: LAMP wordpress application deployment on Docker Containers
For this i have used this blog post from same website :
https://projectwala.site/docker-containerization-mysql-wordpress-web-installation-on-cloud-aws

Step-3: Usinf docker-compose setup we will deploy the LAMP application on docker container that is running over ubuntu OS over AWS t2.medium instance.

> <img width="643" height="93" alt="image" src="https://github.com/user-attachments/assets/e1558857-96c6-4d24-a0ae-af31f5789238" />

```bash
# vim docker-compose.yml
version: "3.8"

services:
  mysql:
    image: mysql:5.7
    container_name: mysql
    restart: always
    environment:
      MYSQL_ROOT_PASSWORD: root
      MYSQL_DATABASE: wordpress
    volumes:
      - ./mysql-data:/var/lib/mysql
    networks:
      - wordpress-net

  wordpress:
    image: wordpress:latest
    container_name: wordpress
    restart: always
    depends_on:
      - mysql
    ports:
      - "8080:80"
    environment:
      WORDPRESS_DB_HOST: mysql
      WORDPRESS_DB_NAME: wordpress
      WORDPRESS_DB_USER: root
      WORDPRESS_DB_PASSWORD: root
    volumes:
      - ./wp-content:/var/www/html/wp-content
    networks:
      - wordpress-net

networks:
  wordpress-net:
    driver: bridge

```
```bash
# docker compose up -d
# docker image ls
# docker ps
# docker network ls
```
Note: 
 - Do not forgot to install docker-compose. Follow the official documentation to install docker and docker-compose.
 - Do not forgot to add 8080 port on your inbound security group.
 - If you want to change the theme - Go to the mount volume of docker volume wp-content that is present in docker location cd /var/lib/docker/volumes/<container-full-hash-id>/_data/wp-content/themes/
 - You can put your own wordpress theme here only

> <img width="1146" height="72" alt="image" src="https://github.com/user-attachments/assets/0aca16c7-bd52-4785-91cc-aac86635675a" />
> <img width="1161" height="87" alt="image" src="https://github.com/user-attachments/assets/f3a40e50-4bad-44f9-8a1e-d05c89fbaf5a" />
> <img width="1366" height="701" alt="image" src="https://github.com/user-attachments/assets/cb75ae4b-b2d1-45ef-ae28-7c08eb24452f" />
> <img width="1366" height="551" alt="image" src="https://github.com/user-attachments/assets/5beb0e96-336f-4e2d-a423-8b3640912df1" />
> <img width="1366" height="648" alt="image" src="https://github.com/user-attachments/assets/1c293583-8e98-4e82-ae81-342153b3fc14" />



Note:
 - In kubernetes deployment of LAMP application, i am sharing the details in dedicated path on this same GitHub Repository.
 Link: https://github.com/devrakaops/projectwala/tree/main/Basic-Level/Project-2/Kubernetes location
 - Why i am doing this becase of clear understanding and later GitOps integration. So click on the above link and learn how to deploy app on kubernetes and argocd.

 Thank you
