## how-to-setup-fastdl


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

#### FastDL


## Acknowledgements

#### Crident(.dot)net

Using their blog page in my endevour helped to understand some ways to push content to their users.

#### maurits(.dot)tv

Providing their wiki to the WWW, their documentation is a little outdated :/


