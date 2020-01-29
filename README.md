# docker-script-watcher-files
Monitora mudanças nos arquivo do projeto e criar o container automaticamente 

#!/bin/bash

#instale o  inotify-tools para esse script funcionar

imagename=test/haraka
paramrun=" -p 25:25" 
dir="./config ./helpers ./plugins ./"

inotifywait -m  $dir -e create -e moved_to -e close_write |
    while read path action file; do
        idImage=$(docker ps -q --filter ancestor=${imagename})

        if [ ! -z "$idImage" ]
        then
            docker rm -f $idImage
        fi
        
        docker build -t $imagename .
        docker run  -i  $imagename  &
    done
