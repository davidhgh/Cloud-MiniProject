# Cloud-MiniProject

## Links to images
https://sitsingaporetechedu-my.sharepoint.com/:f:/g/personal/2000939_sit_singaporetech_edu_sg/EgwVZEEBOx5LkO14HlI1oZgBZQy9Xpc-h2wj7yeg3hOpfw?e=kG9XMJ

## Configuration for images
1. SSH login for both images
    - user: rpi
    - password: rpinas
2. main
    - Description: Main NAS storage device. Functions as a home server to run docker containers, e.g. Plex Media Server.
    - Hostname: main
    - Static IP: 192.168.1.100
3. backup
    - Description: Backup NAS storage device, mirrors files of shared drive on main with rsync as a cronjob
    - Hostname: backup
    - Static IP: 192.168.1.101

## Web GUIs and credentials
1. OpenMediaVault (OMV)
    - Description: NAS software
    - Accessed by: [IP Address of host]
    - Credentials
        * Username: admin
        * Password: openmediavault
2. Portainer
    - Description: Container management utility
    - Accessed by: [IP Address of host]:9000
    - Credentials
        * Username: admin
        * Password: raspberrypinas
3. Plex Media Server (Installed only main RPI)
    - Description: Netflix but streaming with your own content. Reads files from NAS mounted as docker volumes.
    - Accessed by: 192.168.1.100:32400/web
    - Credentials (Login by email, not Google/Facebook etc)
        - Email: webscraperoop@gmail.com
        - Password: mmE3F3mUwc88Bhg
4. LibreOffice (Installed only main RPI)
    - Description: Open Source MS Office
    - Accessed by: 192.168.1.100:3000

## Folder Structure of shared drive
* plex
    * config
    * movies
    * tv
    * music
    * cat-videos
* libreoffice
    * config
    * doc
    * ppt
    * excel

## Cronjob for rsync to mirror drives every 10 minutes (Can be adjusted)
*/10 * * * * rsync -zaP -e ssh --delete [REPLACE WITH ABSOLUTE PATH OF YOUR MAIN SHARED DRIVE]/plex rpi@192.168.1.101:[REPLACE WITH ABSOLUTE PATH OF YOUR NASBACKUP SHARED DRIVE] > rsync_logs.txt

## Steps to setup
1. Image 2 SD cards with the images using RPI Imager, 1 for main and 1 for backup
2. Power on both RPI and connect via ethernet
3. SSH into both RPI with credentials above.
4. Plug in external drive into both Raspberry Pi and mount
5. Open OMV web gui for both Raspberry Pi and login as admin
6. Setup external drives for both Raspberry Pi as shared drives:
    * Setup file system under "File Systems" in OMV. Click the circular plus button to create as ext4 file system.
    * Create share of the created file system under "Shared Folders". Click the circular plus button to create a shared folder with "Everyone: read/write" permissions. 
    * Add your shared folder as an SMB share under Services > SMB/CIFS and set public to "Guests only".
    * Map the created drive on windows, This PC > Map Network Drive. You may have to login with your SSH credentials.
7. Get absolute path of shared drive from Storage > Shared Folders on OMV, update paths in:
    * docker-compose.yaml file: To mount NAS directory so files can be displayed in Plex Media Server.
    * rsync command in crontab -e: Ensure files are mirrored properly based on a set schedule.
8. On both external drives, create directories for Plex Media Server following the folder structure above.
9. Run chmod -R 777 [Absolute path of shared drive]. This lets us modify subdirectories in our shared drive which is not possible with the existing permissions.
* Steps below for testing portainer, plex are not done yet!
10. Edit the paths in the cronjob command above with the absolute paths of the shared drives and paste it in the crontab and save it. (Can verify if created with sudo nano /var/spool/cron/crontabs/rpi)
11. Open Portainer on the browser for the main Raspberry Pi
12. Create a stack with the docker compose file with the updated absolute paths to create the volumes
13. A container for the plex media server should be created. Access it via 192.168.1.100:32400/web
14. You should see a login page. Login with the provided credentials
15. Upload some videos into the cat-videos folder and some music into the music folder on the mapped shared drive in your file explorer
16. oh god idk why some stuff isn't working now
