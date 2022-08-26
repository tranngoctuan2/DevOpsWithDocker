# DevOpsWithDocker
- Exercise 1.1: 
cmd: 
```
docker container run -t -d hello-world
docker container run -t -d nginx
docker run -it -d centos
docker stop priceless_wescoff
docker ps -as
```
     
- Exersice 1.2:
cmd: 
```
docker system prune
docker ps -a
docker image ls
```

- Exercise 1.3:
cmd: 
```
docker run -it -d devopsdockeruh/simple-web-service:ubuntu
docker container ls
docker exec -it magical_raman bash
ls
tail -f ./text.log
```

- Exercise 1.4:
cmd: 
```
docker run -d --rm -it --name inputweb ubuntu sh -c 'echo "Input website:"; read website; echo "Searching..";sleep 1; curl http://$website;'
docker exec inputweb sh -c"apt-get upgrade&& apt-get -y install curl"
docker attach inputweb
helsinki.fi
```

- Exercise 1.5:
cmd: 
```
docker run -it -d --rm devopsdockeruh/simple-web-service:alpine
docker container ls
docker exec -it crazy_wing sh
ls
tail -f./text.log
```

- Exercise 1.6:
cmd: 
```
docker run -it devopsdockeruh/pull_exercise
```

Truy cập vào trang github docker-hy/docs-exercise
Nhập input "basics"

- Exercise 1.7:
Để dễ quản lý custom docker images, tạo folder MyDockerImage
cmd: 
```
mkdir MyDockerImage
cd MyDockerImage
touch Dockerfile
cat>Dockerfile
     FROM devopsdockeruh/simple-web-service:alpine
     CMD server
     ^C
docker build . -t web-server **cmt: image name web-server**
docker run web-server
```

- Exercise 1.8:
cmd: 
```
cat>Dockerfile
     FROM ubuntu
     RUN apt-get update && apt-get -y install curl
     CMD echo "Input website:";read website; 
     echo"Searching..";sleep 1;curl http://$website;
     ^C
 docker build . -t curler
 docker run -it curler
 ```
 
Nhập input: helsinki.fi

- Exercise 1.9:
cmd: 
```
touch /tmp/log.txt
docker run -v ${pwd}/tmp/log.txt:/usr/app/logs.txt devopsdockeruh/first_volume_exercise
docker run -it -d  devopsdockeruh/first_volume_exercise
docker container ls
docker exec -it compassionate_volhard sh
ls
tail -f ./logs.txt
```

- Exercise 1.10:
cmd: 
```
docker run -d -it -p 8080:8080 --name portmapping devopsdockeruh/simple-web-service sh -c "server"
```

Truy cập vào localhost:8080 để kiểm tra

- Exercise 1.11:
cmd: 
```
git clone https://github.com/tranngoctuan2/material-applications
cd material-applications
cd spring-example-project
touch Dockerfile
cat>Dockerfile
     FROM openjdk:8
     WORKDIR /usr/src
     COPY . .
     RUN ./mvnw package
     CMD ["java", "-jar", "./target/docker-example-1.1.3.jar"]
     EXPOSE 8080
     ^C	
     
docker build . -t spring-project
docker run -p 8080:8080 spring-project
```

Truy cập vào localhost:8080 để thử (Note: kiểm tra port đã có container nào hoạt động chưa, nếu có thì stop container đó lại)

- Exercise 1.12:
cmd: 
```
cd ..
cd  example-frontend
touch Dockerfile
cat>Dockerfile
     FROM ubuntu:latest
     WORKDIR /usr/src
     COPY . .
     RUN apt-get update && apt-get -y install curl
     RUN apt-get -y install nodejs && apt-get -y install npm
     RUN npm install && npm run build && npm install -g serve
     CMD ["npx","serve","-s","-l","5000","build"]
     EXPOSE 5000
     ^C
     
docker build . -t example-fe
docker run -p 5000:5000 example-fe
```

- Exercise 1.13:
cmd: 
```
cd ..
cd example-backend
touch Dockerfile
cat>Dockerfile
     FROM golang:1.16
     WORKDIR /usr/src/
     COPY . .
     EXPOSE 8080
     ENV REQUEST_ORIGIN=http://localhost:5000	
     RUN go build
     RUN go test
     CMD ./server
     ^C
     
docker build . -t example-be	
docker run -p 8080:8080 example-be
```
- Exercise 2.1:
Tạo 1 file docker-compose.yml
cmd:
```
cat > docker-compose.yml
	version: "3.9"
	services:
	 logger:
	  image: devopsdockeruh/simple-web-service
	  build: .
	  volumes: 
		 - ./log:/usr/src/app/text.log
	  container_name: logger
	  ^C
docker-compose up
```
- Exercise 2.2:
cmd:
```
cat > docker-compose.yml
	version: "3.9"
	services:
	 web:
	  image: devopsdockeruh/simple-web-service
	  build: .
	  ports: 
	       - 8080:8080
	  command: server
	  container_name: web
	  ^C
docker-compose up
```
- Exercise 2.3:
cmd:
```
cat > docker-compose.yml
	version: "3.9"
	services:
	 example-backend:
	  container_name: containbe
	  image: example-be
	  build: .
	  ports: 
	   - "8080:8080"
	 example-frontend:
	  container_name: containfe
	  image: example-fe
	  build: .
	  ports: 
	   - "5000:5000"
	  ^C
docker-compose up
```
- Exercise 2.4:
cmd:
```
cat > docker-compose.yml
	version: "3.9"
	services:
	 redis:
	  image: redis
	  restart: unless-stopped
	 example-backend:
	  container_name: containbe
	  image: example-be
	  environment:
	   - REDIS_HOST=redis
	  depends_on:
	   - redis
	  ports:
	   - "8080:8080"
	 example-frontend:
	  container_name: containfe
	  image: example-fe
	  depends_on:
	   - example-backend
	  ports: 
	   - "5000:5000"
	  ^C
docker run -d redis
docker-compose up
```
truy cập vào localhost:5000 click vào button tại 2.4
- Exercise 2.5:
cmd:
```
docker-compose up -d --scale compute=3
```
- Exercise 2.6:	
cmd:
```
docker run -d postgres
cat > docker-compose.yml
	version: "3.9"
	services:
	 redis:
	  image: redis
	  restart: unless-stopped
	 example-backend:
	  container_name: containbe
	  image: example-be
	  environment:
	   - REDIS_HOST=redis
	   - POSTGRES_HOST=postgres
	   - POSTGRES_USER=postgres
	   - POSTGRES_PASSWORD=postgres
	   - POSTGRES_DATABASE=postgres
	  depends_on:
	   - redis
	   - postgres
	  ports:
	   - "8080:8080"
	 example-frontend:
	  container_name: containfe
	  image: example-fe
	  depends_on:
	   - example-backend
	  ports: 
	   - "5000:5000"
	 postgres:
	  image: postgres
	  environment:
	   - POSTGRES_HOST=postgres
	   - POSTGRES_USER=postgres
	   - POSTGRES_PASSWORD=postgres
	   - POSTGRES_DATABASE=postgres
	   ^C
docker-compose up
```
- Exercise 2.8:	
cmd:
```
cat> nginx.conf
	events { worker_connections 1024; }

	http {
	  server {
	    listen 80;

	    location / {
	      proxy_pass http://frontend:5000/;
	    }

	    location /api/ {
	      proxy_pass http://backend:8080/;
	    }
	  }
	}
	^C
cat>docker-compose.yml
	version: "3.9"
	services:
	 redis:
	  image: redis
	  restart: unless-stopped
	 backend:
	  container_name: backend
	  image: example-be
	  environment:
	   - REDIS_HOST=redis
	   - POSTGRES_HOST=postgres
	   - POSTGRES_USER=postgres
	   - POSTGRES_PASSWORD=postgres
	   - POSTGRES_DATABASE=postgres
	   - REQUEST_ORIGIN=http://localhost:80
	  depends_on:
	   - redis
	   - postgres
	  ports:
	   - "8080:8080"
	 frontend:
	  container_name: frontend
	  image: example-fe
	  depends_on:
	   - backend
	  environment:
	   - REACT_APP_BACKEND_URL=http://localhost:80
	  ports: 
	   - "5000:5000"
	 postgres:
	  image: postgres
	  environment:
	   - POSTGRES_HOST=postgres
	   - POSTGRES_USER=postgres
	   - POSTGRES_PASSWORD=postgres
	   - POSTGRES_DATABASE=postgres 
	 nginx:
	  image: nginx
	  ports:
	   - "8081:80"
	  volumes:
	   - ./nginx.config:/etc/nginx/nginx.conf
	 ^C
docker compose up
```
Sau đó truy cập localhost:8081 để test

 
