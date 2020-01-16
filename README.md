## How to Setup FastDL in Garry's Mod




## Introduction

In this document should give you a general concept on how `FastDL` works and any bugs you may encounter along side other underlying child 'content-pushers'.

I've decided to write a general guide on how to setup `FastDL` for Garry's Mod since not many resources give any guidelines or information on how it is setup or ran. Due to other servers within the Oceanic region of the world dabbling in smoke and daggers in regards to information regarding it. From what I have seen it is more often than not 'flexing' on other smaller communities or individuals in order to 'trample' certain categories of the server ranking list.

A lot of documentation for it is usually shrouded by WorkshopDL hence why it may be hard to find documentation on this.


## FastDL vs. WorkshopDL / ServerDL

### General concept ahead I'm not spoon feeding, instead regurgitating information.


#### ServerDL

- ServerDL is enabled by default with this method of pushing content to the clients it DIRECTLY downloads content from the server within garrysmod if you hadn't already setup any external way to push content to the client. By default the download limit is capped in Garrysmod at 20 kilobits per second. (20kbps)

#### WorkshopDL

- WorkshopDL is typically combined with a workshop then accommodated with the commandline parameter +host_workshop_collection xxxxxx. This is using Valve's own CDN to push content to your clients via the workshop if they hadn't already subscribed to your content pack or your individual addons.

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

- Unforunately I haven't tested the exact speed of WorkshopDL but I definitely know that it's much slower than FastDL. I suspect it's under the same threshold as ServerDL(20kbps). 



#### FastDL

- FastDL utilizes the HTTP protocol to push content to the clients externally using your own service(webhost), hence by using your own service there are no restrictions placed upon the client's transfer(download) rates instead of the 20kbps and if there are any restrictions it would be on your service - an average user would NOT exceed this restriction.

- FastDL can also support compressed files in the format bz2(Bzip2). Garry's mod just also happens to be able to open, read and decompile bzip2 files, BZ2(bzip2) files are compressed using either 7zip or another program - Eitherway bz2 utilizes the Burrows–Wheeler algorithm which significantly reduces file sizes; for comparison, A map file (32 MegaBytes) has to be downloaded upon joining, simply put increasing loading times due to the download of the map. We take the same map file but compress it using bzip2, it almost halves the size of the map (16 MegaBytes) effectively 'halfing' loading times and quota you'll use pushing content.

- Knowing this information about compression, quota usage and other parent 'content-pushers' we can take that information and also advise ourselves on the possiblities that you could run into.

  -`Bandwidth(quota) costs may incur if you have a very popular server hence serving content to hundreds of users ( typical web server hosts allow around 500gb/1tb of quota before you start paying per gb of usage )`
  
  -`Stealing FastDL Content - it's something that you WILL need to counter otherwise other people can use your fastdl server at YOUR expense, if you have access to your webserver's SSH terminal you can check your webserver package's logs(nginx,apache, lighttpd, etc) you'll see the referring header hl2://your.server.ip.address you can deny anyone with a mismatching referer header which effectively kills almost everyone attempting to use your fastdl server.`
  
 


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

- As instead FastDL parses things in the main root directories that need to be downloaded.

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


- _CONSTRUCTION PAST HERE_




## Acknowledgements

#### Crident(.dot)net

Using their blog page in my endevour helped to understand some ways to push content to their users. 

#### maurits(.dot)tv

Providing their wiki to the WWW, their documentation is a little outdated :/


