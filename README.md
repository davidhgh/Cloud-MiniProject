# Cloud-MiniProject

## Links to images
https://sitsingaporetechedu-my.sharepoint.com/:f:/g/personal/2000939_sit_singaporetech_edu_sg/EgwVZEEBOx5LkO14HlI1oZgBZQy9Xpc-h2wj7yeg3hOpfw?e=kG9XMJ

## Configuration for images
- SSH login for both images
user: rpi
password: rpinas
- main
hostname: david-rpi
Static IP: 192.168.1.100
- nasbackup
hostname: ken-rpi
Static IP: 192.168.1.101

## Web GUIs
1. OpenMediaVault (OMV)
Accessed by: [IP Address of host]
Credentials
    - Username: admin
    - Password: openmediavault
2. Portainer
Accessed by: [IP Address of host]:9000
Credentials
    - Username: admin
    - Password: raspberrypinas
3. Plex Media Server (Installed only main RPI)
Accessed by: 192.168.1.100:32400/web
Credentials (Login by email, not Google/Facebook etc)
    - Email: webscraperoop@gmail.com
    - Password: mmE3F3mUwc88Bhg

## File Structure of shared drive
plex > config, movies, tv, music, cat-videos

## Cronjob for rsync to mirror drives every 10 minutes (Can be adjusted)
*/10 * * * * rsync -zaP -e ssh [REPLACE WITH ABSOLUTE PATH OF YOUR MAIN SHARED DRIVE]/plex rpi@192.168.1.101:[REPLACE WITH ABSOLUTE PATH OF YOUR NASBACKUP SHARED DRIVE] > rsync_logs.txt

## Steps to setup
1. Image 2 SD cards with the images using Raspberry Pi Imager, 1 for main and 1 for nasbackup
2. Power on both Raspberry Pi and connect via ethernet
3. SSH into main Raspberry Pi with hostname or IP and run sudo docker ps. Verify that portainer and plex containers are running
4. SSH into nasbackup with hostname or IP
5. Plug in external drive into both Raspberry Pi and mount
6. Open OMV web gui for both Raspberry Pi and login as admin
<<BELOW STEPS ARE ALL SUMMARIZED>>
7. Setup external drives for both Raspberry Pi as shared drives (Setup file system, create share, enable SMB/CIFS, map drives on windows, create plex directories on external drive refer to docker compose file)
8. Get absolute path of shared drive from Storage, update paths in:
* docker-compose.yaml file
* rsync command in crontab -e
This ensures the plex media server volumes are mounted correctly and files in the NAS can be displayed on the plex media server's web gui
9. On the main RPI, run crontab -e. If there is an existing cronjob, remove it.
10. Edit the paths in the cronjob command above with the absolute paths of the shared drives and paste it in the crontab and save it. (Can verify if created with sudo nano /var/spool/cron/crontabs/rpi)
11. Open Portainer on the browser for the main Raspberry Pi
12. Create a stack with the docker compose file with the updated absolute paths to create the volumes
13. A container for the plex media server should be created. Access it via 192.168.1.100:32400/web
14. You should see a login page. Login with the provided credentials
15. Upload some videos into the cat-videos folder and some music into the music folder on the mapped shared drive in your file explorer
16. oh god idk why some stuff isn't working now