# SSE-Ghidra-Tutorial

## Tutorial

The goal of this tutorial is to describe how to install a setup to retro-engineer skyrim,
and show basic examples of how to do it.

### Setup

#### Install Ghidra

Ghidra is an open-source java software retro-engineering suite of tools.
It will work on Windows, Linux, MacOS.   
https://ghidra-sre.org/

At the time of this writing, I'm using ghidra_10.2.3_PUBLIC.

#### Steamless skyrim.exe

You need to steamless the skyrim executable to be able to retro-engineer it efficiently.   
Here is the software:
https://github.com/atom0s/Steamless/releases

Then apply it on your skyrim executable, produce the steamlessed version and save it.
We will work on this one. 

In this version of the tutorial, we'll focus on using the 1.5.97 version of the skyrim executable.

At the time of this writing, I'm using Steamless v3.0.0.12.

#### Create your Ghidra project

File -> New project -> Non-Shared project -> next.
For your Project directory, choose as you wish; I have set mine as C:\Games\Ghidra\SE 1.5.97   
and my project name as "SE1597".

My steamlessed executable is renamed SkyrimSE1597Steamlessed.exe and put into C:\Games\Ghidra\SE 1.5.97 imports\   
That way, if I want to recreate another project based on it, I know exactly where it is and which executable it is.

#### Import the required files into your ghidra project

Add to your imports folder the binkw64.dll that you'll find in your skyrim executable folder.   
If you have installed an ENB, you might want to add too the d3d11.dll provided with your enb executable, that you should have installed in your skyrim executable folder.   

Then import in this order binkw64.dll, d3d11.dll if you wish, and your steamlessed skyrim executable.

To import them:   
File -> Import -> select your file.   
Choose the default options, then let ghidra import the file.

About the messages:
- "Skipping library which is the wrong architecture:": you can ignore these messages.
- "failed to create WEVTResource at [address]: Failed to resolve data length for WEVTResource": I have no idea what it means. ***If you know, please let me know.***

#### Load the pdb files

Add to your imports folder the pdb file found in the archive available here in the optional files:
https://www.nexusmods.com/skyrimspecialedition/mods/59818?tab=files   

Add the pdb you'll find there too: -------

These reverse engineered PDB files will help identify SkyrimSSE addresses and attribute names.

Right-click on the exe -> Open with -> CodeBrowser.
Select ‘No’ when asked to analyze the program.   
In CodeBrowser, File -> Load PDB File.   
There is a warning below on the "Config..." button, click on it and set your symbols folder. I've set mine as "C:\Games\Ghidra\SE 1.5.97 symbols".   
In PDB location, select your pdb file, then click on Load. Do this for both pdb files.   

About the messages:
- There are warnings about ghidra not being able to set things as void, and using undefined instead. Ignore them.
- Ignore the messages like this one:
> Demangler Microsoft> Apply failure (DemangledFunction: IllegalArgumentException: Defined data at address: 141509510)
> 	?setg@?$basic_streambuf@DU?$char_traits@D@std@@@std@@IEAAXPEAD00@Z"

*To Be Confirmed:*
- *should we import the system dlls like kernel32.dll that ghidra complains about having not found them?*
- *Should we use other options than the default options when importing a file?*
- *I wonder about the "Config..." button warning. Should we have configured ghidra a certain way first? Check in the ghidra doc.*
- *Is it really ok to just ignore those warnings?*
***If you know the answers to these questions, please let me know.***

#### Analyze the skyrim executable

In CodeBrowser, Analysis -> Auto-Analyze   
Use the defaults option, and launch the analysis. Wait (around 10 minutes on my computer (10900KF cpu)).   
File -> Save all.

### Retro-engineering Skyrim

#### Hooks

Mods modify skyrim behaviour through different ways: replacing resources like textures or sounds, adding plugins, or modifying directly the skyrim binary code.
To modify the binary code, we declare new code which will begin when a certain memory address of the skyrim binary is attained, and will come back to (that|another?) address when it's finished running.
We "hook" on these addresses.

Some mods use hardcoded addresses, for example FSMP: https://github.com/DaymareOn/hdtSMP64/blob/master/hdtSMP64/Offsets.h
Some mods use address libraries:
- Address Library For SKSE Plugins: https://www.nexusmods.com/skyrimspecialedition/mods/32444
- CommonLibSSE: https://github.com/Ryan-rsm-McKenzie/CommonLibSSE
- CommonLibSSE-NG: https://github.com/CharmedBaryon/CommonLibSSE-NG
- SKSE: https://skse.silverlock.org/

For the project justifying this tutorial, I'm looking at 3 addresses for Skyrim 1.5.97 defined in the FSMP Offsets.h file above:
- 0x003D87B0: BSFaceGenNiNode_SkinAllGeometry
- 0x003D8840: BSFaceGenNiNode_SkinSingleGeometry
- BSFaceGenNiNode_SkinSingleGeometry_bug = BSFaceGenNiNode_SkinSingleGeometry + 0x96
The 2 first addresses are the beginning of two functions of the class BSFaceGenNiNode, that the FSMP code names SkinAllGeometry and SkinSingleGeometry.
The class BSFaceGenNiNode has been defined by SKSE, on which FSMP is based.

## TODO

- Bookmarks, comments, functions, function signatures, variables, types
- Version track to other versions (1.6.640, 1.6.353, VR)
- After done, do version tracking and do auto version analysis. That may take a few days to process though.
- There's a types.h floating on the the re server with 1.5.97 data structures from like 3 years ago to get started.
- We really need some tooling to just convert commonlib to something importable for types
- Convert the available Skyrim SE IDA rename scripts to be compatible with Ghidra. meh321 has made an IDA rename script for Skyrim SE 1.5.97.0
available in the utilities channel of the SkyrimSE RE server, and the Address Library Manager/Database will provide with rename scripts for
Skyrim SE 1.6+. https://github.com/meh321/AddressLibraryDatabase
- The latest version of the Address Library Manager is available here: https://github.com/meh321/AddressLibraryManager/releases/tag/3
- look at the resources on MP reverse-engineering channel

## Credits

- XXX
