## How to Setup FastDL in Garry's Mod




## Introduction

This document should give you a general concept on how `FastDL` works and any bugs you may encounter alongside other child 'content-pushers'. Just to save time for everyone digging to the ends of the internet tubes everything you need will be neatly organized here.

I've decided to write a general guide on how to setup `FastDL` for Garry's Mod since not many resources give any guidelines or information on how it is setup or run. Due to other servers within the Oceanic region of the world dabbling in smoke and daggers in regards to information for it. From what I have seen it is more often than not 'flexing' on other smaller communities or individuals to 'trample' other servers in certain categories of the server ranking list.

A lot of documentation for it is usually shrouded by WorkshopDL hence why it may be hard to find documentation on this.


## FastDL vs. WorkshopDL / ServerDL

### General concept ahead this is purely me regurgitating information. Some of this may be incorrect but putting experience to the text.


#### ServerDL

- ServerDL is enabled by default with this method of pushing content to the clients it DIRECTLY downloads content from the server within garrysmod if you hadn't already setup any external way to push content to the client. By default, the download limit is capped in Garrysmod at 20 kilobits per second. (20kbps)

#### WorkshopDL

- WorkshopDL is typically combined with a workshop collection then accommodated with the command line parameter +host_workshop_collection xxxxxx. This is using Valve's own CDN to push content to your clients via the workshop if they hadn't already subscribed to your content pack or your individual addons.

- WorkshopDL is configured by setting a file up located somewhere in autorun - typically in (lua/autorun/server), you would gather all of your workshop addon ID's and use the function `resource.AddWorkshop("xxxxxxx")`https://wiki.garrysmod.com/page/resource/AddWorkshop

- Example of this would be

```
-- File: garrysmod/lua/autorun/server/

if (SERVER) then
  resource.AddWorkshop('xxxx')
  resource.AddWorkshop('xxxx')
  resource.AddWorkshop('xxxx')
end
```

- Unfortunately, I haven't tested the exact speed of WorkshopDL but I definitely know that it's much slower than FastDL. I suspect it's under the same threshold as ServerDL(20kbps). 



#### FastDL

- FastDL utilizes the HTTP protocol to push content to the clients externally using your own service(web host), hence by using your own service there are no restrictions placed upon the client's transfer(download) rates instead of the 20kbps and if there are any restrictions it would be on your service - an average user would NOT exceed this restriction.

- FastDL can also support compressed files in the format bz2(Bzip2). Garry's mod just also happens to be able to open, read and decompile bzip2 files, BZ2(bzip2) files are compressed using either 7zip or another program - Eitherway bz2 utilizes the Burrows-Wheeler algorithm which significantly reduces file sizes; for comparison, A map file (32 MegaBytes) has to be downloaded upon joining, simply put increasing loading times due to the download of the map. We take the same map file but compress it using bzip2, it almost halves the size of the map (16 MegaBytes) effectively 'halving' loading times and quota you'll use pushing content.

- Knowing this information about compression, quota usage, and other parent 'content-pushers' we can take that information and also advise ourselves on the possibilities that you could run into.

  - Bandwidth ( quota ) costs may incur if you have a very popular server hence serving content to hundreds of users ( typical web server hosts allow around 500gb/1tb of quota before you start paying per GB of usage )
  
  - Stealing FastDL Content - it's something that you WILL need to counter otherwise other people can use your fastdl server at **YOUR** expense, if you have access to your webserver's SSH terminal you can check your webserver package's logs(nginx, apache, Lighttpd, etc), You'll see the referring header or user-agent I've forgotten which is inside the log file, the format should go something along these lines `[HH:MM:SS:] some.other.ip.address, Valve/Steam HTTP Client 1.0 (4000), hl2://your.server.ip.address` you can deny anyone with a mismatching referer/user-agent header which effectively kills almost everyone attempting to use your fastdl server.
  
 

- FastDL is rather tricky in terms of how it works, to cut a long story short. FastDL doesn't work like how Garry's mod parses addons
e.g 
```
-- Typical Garry's mod Addon Structure
addons/

  -> addon_example1/
    -> lua/
    -> materials/
    
  -> addon_example2/
    -> lua/
    -> materials/
    
```

As instead FastDL parses things in the main root directories that need to be downloaded.

```
-- FastDL File Structure

maps/
  -> gm_fastdl.bsp ( or gm_fastdl.bsp.bz2 )

materials/
  -> my_material.vtf
  -> my_material2.vmt

models/
  -> etc

sounds/
  -> etc

resource/
  -> etc
```


## Configuring FastDL


#### Preparation 

Depending on what type of server you're running you'll need to approach this setup in a different way, if you're on shared oversubscribed hosting ( this is where you buy a cheap hosting service that only gives you a panel and ftp where many people are put on the same machine you are on making it very slow and overused), or you are running completely dedicated from a company who does instances e.g AWS, OVH, Vultr.

#### Shared Host Scenario

- If you are on shared hosting with a panel and don't have access to SSH on the machine then see solution below.


First steps, you'll need to get a webhost with decent bandwidth( quota ), I heavily suggest using Amazon Web Services' (AWS) S3 bucket as they offer cheaper storage options with other bolt on services that can help the flexibility of your FastDL - but we'll go with a instance from OVH(VPS SSD 1, $4.99 per month) on Ubuntu 18.04.

1.) We'll Login to our ovh instance and run an update and upgrade `sudo apt-get update && sudo apt-get upgrade`

2.) Now we will install our web server package, in this scenario we will use apache, to install this `sudo apt-get install apache2` let that install, secondly we'll have to install ftp to get our files onto our instance to do this `sudo apt-get install vsftpd`

3.) Once all of our packages are done we'll have to configure them, navigate to `/etc/apache2/sites-enabled` and using nano we'll edit edit our config `000-default.conf` 
  - something to also mention if you already have a website running on port 80 apache2 will spit the dummy and say that the binding port address is already in use, if you already have a website running on port 80 you can change the port that it will listen on in the following files `/etc/apache2/ports.conf` and `/etc/apache2/sites-enabled/000-default.conf`
  find and edit the following line `DocumentRoot` it's default should be `DocumentRoot /var/www/html/` we'll change this to `DocumentRoot /var/www/fastdl/`

4.) Drop down the following few lines you will see `<Directory />` this permissions for the specified document root we defined above, make sure that your directory config looks something like this
```
<Directory />
  Options Indexes FollowSymLinks MultiViews
   AllowOverride All
  Require all granted
</Directory>
```
- Reason for allowing the indexing is because I have had issues with the client not being able to download files because the indexing was turned off. ( nginx & lighttpd also has this feature unless you explicitly turn it on)
5.) Create the folder that apache will use by running `sudo mkdir /var/www/fastdl`, then reboot apache2 `sudo systemctl restart apache2` or `service restart apache2.service` .

6.) Now we need to configure FTP(vsftpd), edit the following file using this command `nano /etc/vsftpd.conf`, add the following lines to your config file
```
listen=YES
listen_ipv6=NO
connect_from_port_20=YES
 
anonymous_enable=NO
local_enable=YES
write_enable=YES
chroot_local_user=YES
allow_writeable_chroot=YES
secure_chroot_dir=/var/run/vsftpd/empty
 
pam_service_name=vsftpd
 
pasv_enable=YES
pasv_min_port=40000
pasv_max_port=45000
 
userlist_enable=YES
userlist_file=/etc/vsftpd.userlist
userlist_deny=NO
```
7.) You'll want to go create this file `/etc/vsftpd.userlist` which will contain JUST the username for the user you'll login as to upload your files. Restart vsftpd `sudo systemctl restart vsftpd` or `service restart vsftpd.service`. then use your GUI or cli tool of choice to connect to the ftp server, upload your files that you want to be on your fastdl server. Once you're done uploading we can move onto the next step.

8.) Your addons will contain certain folders like
```
ADDON_Name/
-> lua/
-> materials/
-> sounds/
-> resource/
-> models/
```
you're going to want to copy these folders `models/, materials/, sounds/ and resource/`, see script attached in this GitHub repo to automate this, be sure to adjust the script to your settings.

9) Open up your Garry's Mod folder and navigate to the `garrysmod/cfg/` directory, here you'll want to edit this file `server.cfg`. You'll see the line `sv_downloadurl ""` if you do not then add the line yourself, set this value`sv_downloadurl http://yourwebserverhere/`.

10.) You'll need to configure what the clients will download for example the maps are the most common thing to download upon joining a server. See the script attached to automate this process, but if you want to manually add something use this format.
```
-- garrysmod/lua/autorun/server/fastdl.lua

if (SERVER) then
  -- EXAMPLE
  -- resource.AddFile("maps/gm_construct.bsp") -- Uncompressed
  -- resource.AddFIle("maps/gm_construct.bsp.bz2") -- Compressed ( smaller filesize )
```
![Filesize](https://i.imgur.com/MVedelQ.png)

_CONSTRUCTION PAST HERE_


## Acknowledgements / Resources

#### Crident(.dot)net

Using their blog page in my endevour helped to understand some ways to push content to their users. 

#### maurits(.dot)tv

Providing their wiki to the WWW, their documentation is a little outdated :/

#### linuxmint(.dot)com

Providing a easy to read config for vsftpd
