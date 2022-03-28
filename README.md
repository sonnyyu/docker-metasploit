# Build it:
```bash
git clone https://github.com/sonnyyu/docker-metasploit/
cd docker-metasploit
```
# Create a directory metasploit & postgres
```bash
mkdir $HOME/docker-metasploit/msf4
mkdir $HOME/docker-metasploit/msf4/database
```
# Create docker network 
```bash
docker network create --subnet=172.28.0.0/16 msf
```
# Create postgres
```bash
docker run --ip 172.28.0.2 --network msf --rm --name postgres \
-v "${HOME}/docker-metasploit/msf4/database:/var/lib/postgresql/data" -e POSTGRES_PASSWORD=postgres \
-e POSTGRES_USER=postgres -e POSTGRES_DB=msf -d postgres:11-alpine
```
# Setup metasploit
```bash
docker run --rm -it --network msf --name msf \
--ip 172.28.0.3 -e DATABASE_URL='postgres://postgres:postgres@172.28.0.2:5432/msf' \
-v "${HOME}/docker-metasploit/msf4:/home/msf/.msf4" -p 8443-8500:8443-8500 \
metasploitframework/metasploit-framework
```
# Save database setting
```bash
db_save
```
# Run Metasploit
```bash
docker run --rm -it -u 0 --network msf --name msf \
--ip 172.28.0.3 -v "${HOME}/docker-metasploit/msf4:/home/msf/.msf4" \
-p 8443-8500:8443-8500 metasploitframework/metasploit-framework
```
