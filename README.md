# Homelab

There are many tutorials on how to run Docker containers on a Raspberry Pi, but few (to none) that walk you through the
entire process _including_ using GitHub Actions to deploy the containers.

This project aims to be that resource.

## Hardware Used

* Development Machine
    - Macbook Pro
        - Chip: Apple M4 Pro
      - 24GB RAM
      

* Home Lab Server
    - Rasberry Pi 5
        - 8GB RAM
        - 128GB micro SD card
        - Connected monitor & keyboard/mouse

### Other Hardware Needed

* Appropriate power source for RPi (watch for low voltage warning in the UI)
* Micro SD adapter (for creating RPi image)
* Connected keyboard and mouse 
    I wanted to control the RPi with just SSH or VNC, but found I needed to have some peripherals for the initial
    post-setup
    

    - 📺 Display: I chose to use the mini-HDMI
    - ⌨️ Connect a keyboard/mouse
        Obviously, USB wired is the easiest way here, but I ventured further afield and got a [mini-keyboard](https://www.amazon.com/dp/B0BML42L6X). To connect, I simply inserted the Bluetooth receiver to the USB and turned on my keyboard. From here, I proceeded to the Post-boot updates…

## Raspberry Pi Setup

### 🪄 Create RPi Image 🥧 

* Install the imagine software - https://www.raspberrypi.com/software/
    - Connect your RPi storage (i.e. MicroSD card)to your development computer
    - Options
        - Device: Raspberry Pi 5
        - Operating System: Raspberry Pi OS (64-bit)
        - Storage: make sure to select the correct device!
    - General

        ⚠️ MAKE SURE TO COLLECT ALL THESE VALUES FOR LATER STEPS ⚠️

        - Hostname - set to your preferred local domain name (format: `<DOMAIN>.local` )
        - Authentication - set your admin credentials
        - WiFi - connection settings for your local wifi
        - Locale - where you want the RPi to think you are on the planet

----

### 🥾 Boot the Pi 🥧 

* Install the Micro SD card and power it up

> 👮 I needed additional setup to SSH into the RPi, thus, peripherals…

* 📺 Connect a monitor - I chose to use the mini-HDMI
* ⌨️ Connect peripherals
* Collect IP address of RPi if not statically assigned on your router. In my case, `192.168.170.211`

----------

### Post-Boot for the Raspberry Pi

This step was performed with my mini-keyboard/mouse attached via pre-paired Bluetooth.

  > 💡 Always keep the software running on your Raspberry Pi updated to the latest version. This keeps your device secure from [vulnerabilities](https://cve.mitre.org/index.html) and ensures that you get the latest bug fixes. [Raspberry Pi: Updating Software](https://www.raspberrypi.com/documentation/computers/os.html#update-software)

  **TL; DR:** If you don't want to read the linked docs, here is a summary of steps.

1. Update installed packages

  

```zsh
  $ sudo apt update 
  ```

2. Update ALL packages to the latest version

  

```zsh
  $ sudo apt full-upgrade
  ```

1. [RECOMMENDED] Update the firmware

  

```zsh
  $ sudo rpi-update
  ```

1. Reboot to apply changes

  

```zsh
  $ sudo reboot
  ```

**👀 Ensure settings**

  > 💡 Some advanced configuration is available in the `raspi-config` CLI, but not the Raspberry Pi Configuration GUI. [Raspberry Pi: Getting Started](https://www.raspberrypi.com/documentation/computers/configuration.html#getting-started)

  + Connect WiFi (if not connected) - again, collect your given IP address
  + Enable SSH
  + Enable VNC
    → Navigate to the start menu > Preferences > Raspberry Pi Configuration. Then select ‘Interfaces’, and click ‘Enable’ next to VNC. Then click ‘OK’. 

**🔧 Make any additional settings changes you prefer**

**🔐 Enable SSH**

1. On your development machine, update your hosts (e.g. `/etc/hosts`) file to include an FQDN for the IP that the RPi
   connected on. I added a record based on the IP assigned my RPi and the `HOSTNAME` assigned to the Raspberry Pi.

    ```text
    # RasberryPi5
    192.168.170.211 <HOSTNAME>
    ```
2. Transfer SSH Keys to RPi

      > 🙌 CREDIT: [Ionut Banu](https://ionutbanu.medium.com/?source=post_page---byline--9822b20037a0---------------------------------------) @ Medium - [article](https://ionutbanu.medium.com/setting-up-key-pair-ssh-on-raspberry-pi-9822b20037a0)
  

    - Generate private/public key pair on the client by running the following command providing the file path where the
      key pair to be stored. I recommend using a name that will identify that this is for connecting to your Rasperry
      Pi, for example, `~/.ssh/rpi_rsa`.

    ```zsh
    $ ssh-keygen -t rsa
    Generating public/private rsa key pair.
    Enter file in which to save the key (~/.ssh/id_rsa): ~/.ssh/rpi_rsa
    ```
    
      > ℹ️ It is recommended to provide a passphrase when asked, but you will be asked for it each time you ssh into
      > RPi.
      

    - At this point you should have two files at the location you provided, in my case the files are `rpi_rsa` and
      `rpi_rsa.pub` (or whatever filename you chose). The private key is kept secret and should never be shared. The
      public key can be freely distributed.
      

    - Copy the generated public key to the RPi where `<RPi_USER>` is the username you created when setting up the RPi
      image and and `<RPi_IP>` is the IP address of the RPi or the `<HOSTNAME>` you associated it with in the `hosts`
      file.

      ```zsh
      $ ssh-copy-id -i ~/.ssh/rpi_rsa.pub <RPi_USER>@<RPi_IP>
      ```
    
    - Use the terminal to connect to the server using the same substitutions as the last step.
        
        ```zsh
        $ ssh <RPi_USER>@<RPi_IP>
        ```
        
    - ℹ️ Troubleshooting: SSH Connection
        
        - I needed to update my `~/.ssh/known_hosts` file to show 
        
        ```text
        <HOSTNAME> ssh-ed25519
        ```

