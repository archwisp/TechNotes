docker run --name artifactory -d -p 8081:8081 docker.bintray.io/jfrog/artifactory-oss:latest

docker exec -t -i a81761d001e2 /bin/bash


docker ps -a

docker start a81761d001e2
