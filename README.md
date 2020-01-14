# ZPS-SM-Gamedata
Updated gamedata files for ZPS which includes offsets and signatures all necessary for a working copy of Sourcemod.

The following files have been updated with the latest signatures/offsets:
- core.cfg (disables automatic SM updates)
- engine.sdk2013.txt (core.games)
- engine.ep2.txt (core.games)
- game.zpanic.txt (sdkhooks.games)
- game.zpanic.txt (sdktools.games)


# How To Install
The files are setup in the same directory structure as they would be for a regular sourcemod install, so simply copy the addons folder into your game server's main directory and copy over any files.

As an fyi, if you are using core.cfg for anything or have set the config to use different settings, make a copy of your files before overwriting them.


# How to Find Offsets/Signatures

This is for those who want to find out how to do this for themselves. It is important to know how to do this in the event that SM stops working and I'm not around to fix it anymore. There is a wealth of information on the Sourcemod forums and the internet in general that can help you, but I'll help out leading you to the things that helped me the most when looking up this stuff.

### Finding Offsets
Of all the things involving gamedata, finding offsets is much easier to do and has several tools available to help with this effort. Knowing the offsets of something lets you make calls to virtual functions from the Source engine which allows you to manipulate things like game events and all that fun stuff. See this wikipage for more information about offsets here: https://wiki.alliedmods.net/Virtual_Offsets_(Source_Mods) 

To find offsets, you'll need the following to begin:

- Linux server binaries (.so files), specifically "server_srv.so" which will be in the bin folder of a server
    * If you are running windows, you'll need to download the Linux server binaries via SteamCMD as they do not come with the Windows server files. To find these files, look up how to download things with SteamCMD and use this page here for finding the ZPS Linux binaries: https://steamdb.info/app/17505/depots/
- A tool to automatically generate offsets. Luckily, there exists a free and open source tool made by Asherkin from the SM forums here: https://asherkin.github.io/vtable/

Simply follow the instructions on the tool page and drop the .so file onto the page. It will generate the offsets needed, but you'll need to look them up. As of this writing, most offsets fall under CHL2MP_Player (which is where a majority of your offsets will be coming from). You'll notice the W (Windows) and L (Linux) with numbers on the left-hand which has a bunch of numbers underneath them. These are your offsets for their respective OS. All you need to do is find the functions used in the gamedata and find their associated numbers. 

Keep in mind that some functions might be named differently in the SM gamedata than what it is known as in the vtable tool, which does make looking up these things a bit more difficult. In cases such as this, you may need to compare offset numbers and parts of function names to find the actual function being used. This can be a bit time consuming since verifying/testing everything can be an issue. If you have a way to verify old offsets using older server binaries, you could look up the offsets that worked in those to find the specific function names (this was what I had to do when looking up offsets for ZPS 3.0 when we were transitioning from ZPS 2.4.1 and it works very well). 

As a final note, offsets are easy to find, but they change the most often, so be ready to look up offsets after every update as any additional function/code added to the game may change the functions enough to change their virtual offset, meaning the gamedata will need to be updated. It doesn't always happen, but its usually the first thing to check after an update. Also, some offsets tie into game events (such as player death) so, for convenience, here is a link to a page with ZPS events: https://wiki.alliedmods.net/Zombie_Panic!_Source_Events

### Finding Signatures
Finding signatures is much more difficult than finding offsets as it is not an easy thing to automate and it actually requires reverse engineering to find what you are looking for. This is a much more complex thing to do and is far more time consuming and it requires some knowledge on programming, hex codes, usage of decompiler tools, and some instruction sets. Here are some things you'll need:

- A decompiler tool such as IDA Pro (keep in mind that this tool is NOT free, but is highly recommended to be used to find signatures).
- Linux server binaries (.so files), specifically "server_srv.so"
    * If you are running Windows, you'll need to download the Linux server binaries via SteamCMD as they do not come with the Windows server files. To find these files, look up how to download things with SteamCMD and use this page here for finding the ZPS Linux binaries: https://steamdb.info/app/17505/depots/
- Windows server binaries (.dll files), specifically "server.dll"
    * If you are running Linux, you'll probably need to do what Windows users have to do for Linux files. Simply do the same steps listed previously
- Research into the topic. There are a lot of articles about the concepts and some information on the AlliedModders forums about this subject. Also, this thread specifically is a great start in how to find signatures from Source games (it may be AMX mod, but the concept is exactly the same). See this thread here: https://forums.alliedmods.net/showthread.php?t=147998
- Patience, the ability to walk away when it gets frustrating, and a lot of time. Seriously, the process of getting signatures is actually difficult and can be very daunting, especially for first timers, but it gets easier once you get into the swing of things, so don't fret too much!

You'll need to load the binaries into the tool and find the signatures as they are presented in the gamedata files as the appear differently from one system to another. 

The Linux files will have something like "@_ZN17CBaseEntityOutput10FireOutputE9variant_tP11CBaseEntityS2_f" as the signature (this is signature for "FireOutput" currently). Finding these signatures can be easier as you can look up certain parts of the word (such as "FireOutput") in the editor and it will generally show you the functions signatures necessary for Linux, though keep in mind that you'll need to verify that this is the actual function you need.

The Windows files will have something like "\x55\x8B\xEC\x81\xEC\x2A\x2A\x2A\x2A\xA1\x2A\x2A\x2A\x2A\x33\xC5\x89\x2A\x2A\x53\x8B\x5D\x2A\x8B\xC1\x8B\x2A\x2A\x56\x57" as the signature (this is signature for "FireOutput" currently). Finding Windows signatures can be far more difficult as its not as convenient to do as it is in Linux. The best way to look up these signatures is to attempt to look them up by using part of the hex codes (which you'll need to format properly for the decompile tool to search for). This can be very difficult if there has been a very big change (such as an engine upgrade) which can easily change the code of the functions in question, making it a bit harder to grab the right hex codes to help latch on to the correct function. The best way, in some cases, is comparing the old and new hex codes/binaries against one another to help narrow down what the new signature should be. This will take some study, patience, and some guessing in order to do this properly. Also, I highly suggest reading the information from the thread that was linked earlier as it will help you out much more than this overly simplified explaination will. For reference, the thread I am refering to is here: https://forums.alliedmods.net/showthread.php?t=147998

Finally, keep in mind that ALL signatures MUST be unique as each function is unique. If you have the same signature for FireOutput as you do FindEntityByClassname, then you definitely will have a problem and it will not work correctly or it will be confused as to which function you are referring to. You'll need to ensure that each signature is unqiue from the other signatures in the gamedata for that game, so be aware of that.

While signatures look and sound like a real pain in the butt to deal with, the overall upside to signatures is that they rarely change unless something big changed (such as engine version or the function as a whole) so this won't need to be updated as often, if at all, after every update. Small updates/changes to the game may not actually mean that the signatures have changed, so looking these up during every update is not necessary, but you may want to verify it just in case the game has a large update/overhaul to the code/functions in the engine as it might affect the signatures as well. Also, when in doubt, ask about it on the Allied Modders forums (specifically the Sourcemod forums) as it might be helpful.


# Changelog
Update (01-12-2020)
-------------------------------
- Added the ZPS 2.4.1 gamedata files for those who want to put up a 2.4.1 legacy server. 
    * Just like with the ZPS 3.1 gamedata, Sourcemod does not have ZPS 2.4.1's gamedata by default, so you will need to disable Sourcemod's autoupate feature.
    * The configuration file for Sourcemod, core.cfg, is provided with the repo. 
    * You will need the ZPS 2.4.1 server binaries in order to use this gamedata.
- Created separate directories for ZPS 3.1 and ZPS 2.4.1 in the repo.

Update (01-19-2019)
-------------------------------
- Update SDKTool offsets for ZPS 3.1, this update was pretty small and none of the signatures have changed, so there wasn't much to update here.
- These changes have not been rolled into Sourcemod yet, but once ZPS 3.1 is released publicly (and not in beta) and all changes are finalized, Sourcemod will be updated not long after. 

Update (06-28-2018)
-------------------------------
- Updated signaures found in game.zpanic.txt for the SDKTools gamedata for Windows/Linux. This fixed some issues with common functions used by SDKTools.
- This update was eventually merged/updated to Sourcemod so that people would get the updates automatically with all updates. 

Update/Release (06-07-2018)
-------------------------------
- Initial update to files, primarily just vtable offsets.