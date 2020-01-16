## How to Setup FastDL in Garry's Mod


## Introduction

In this document should give you a general concept on how `FastDL` works and any bugs you may encounter along side other underlying child 'content-pushers'.

I've decided to write a general guide on how to setup `FastDL` for Garry's Mod since not many resources give any guidelines or information on how it is setup or ran. Due to other servers within the Oceanic region of the world dabbling in smoke and daggers in regards to information regarding it. From what I have seen it is more often than not 'flexing' on other smaller communities or individuals in order to 'trample' certain categories of the server ranking list.

A lot of documentation for it is usually shrouded by WorkshopDL hence why it may be hard to find documentation on this.


## FastDL vs. WorkshopDL / ServerDL

### General concept a head I'm not spoon feeding, instead regurgitating information.


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

- FastDL utilizes the HTTP protocol to push content to the clients externally using your own service, hence by using your own service there are no restrictions placed upon the client's transfer(download) rates instead of the 20kbps.

- Garry's mod just also happens to be able to open, read and decompile bzip2 files, BZ2(bzip2) files are compressed using either 7zip or another program - Eitherway bz2 utilizes the Burrows–Wheeler algorithm which significantly reduces file sizes for comparison. A map file (32 MegaBytes) has to be downloaded upon joining, simply put increasing loading times. the same map file but compressed using bzip2 almost halves the size of the map (16 MegaBytes) effectively 'halfing' loading times and quota you'll use pushing content.



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

models/

sounds/

resource/

```




## Acknowledgements

#### Crident(.dot)net

Using their blog page in my endevour helped to understand some ways to push content to their users. 

#### maurits(.dot)tv

Providing their wiki to the WWW, their documentation is a little outdated :/


