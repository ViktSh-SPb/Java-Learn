sudo docker ps --format "table {{.Names}}\t{{.Status}}\t{{.Ports}}"
sudo docker ps -a --format "table {{.Names}}\t{{.Status}}\t{{.Ports}}"