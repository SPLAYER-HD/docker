# docker

# commands
sudo docker volume ls | grep -v "my_volume" | awk 'NR>1 {print $2}'
sudo docker volume rm $(sudo docker volume ls | grep -v "my_volume" | awk 'NR>1 {print $2}')

sudo docker ps -a | grep -v "my_container" | awk 'NR>1 {print $1}'
sudo docker rm $(sudo docker ps -a | grep -v "my_container" | awk 'NR>1 {print $1}')