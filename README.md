# How to make GameBoy music

## Software required
OpenMPT (Recommended v1.29) [download](https://openmpt.org/download)

## Setup OpenMPT
OpenMPT is a tool for writing music that is designed for MIDI manipulation. Along with a useful tool from AntonioND [gbt-player]((https://github.com/AntonioND/gbt-player) this can be used to create the files needed to design GameBoy music for your game.  
Unfortunately the latest version of gbt-player no longer supports gbdk but thankfully a working version of the required files can be found [here](https://github.com/gingemonster/GamingMonstersGameBoySampleCode/tree/master/19_making_music) (Included in this repository in the 'gbp-player' folder).  
To make music that will be compatible with the GameBoy you need to use the template file that is provided in this repository (gbt-player/template.mod). This has samples that aproximate the sounds the gameboy can make so you can play back your music and estimate what it will sound like on the GameBoy.  
Download OpenMPT and open the 'template.mod' file. To make sure you don't overwrite and loose this file I recommend saving a copy for you to work on by going to File->Save as and choosing a location to save the file. Also you may want to name the song which you can do by clicking on the 'General' tab and entering a name in the 'name' box, but this can be done later.  

## Writing music
The two OpenMPT features that we will be using are the Patterns and Sequences. A pattern is a 64 row table split into 4 main channels were you place the notes of the track. A sequence is a collection of patterns, for example you could have 3 patterns and play them in the order 0->1->2->3 or 2->3->2->1.  
The template file contains 4 empty patterns in a 0->1->2->3 sequence. The image below explains how the interface is layed out:
![Labelled OpenMPT interface](/docs/labelledinterface.PNG?raw=true)
Notes can be added to the pattern by clicking in the pattern table and then using your computer keyboard as a musical keyboard. The top row of the keyboard from 'Q' to ']' will write notes from C4 to B4. The next row from 'A' to '#' will write notes from C5 to B5 and so on. Also these keys can be used to record music live by making sure the record indicator is on and then pressing play. As you play notes these will be added to the pattern. To remove a note, select it with the mouse and press the space bar.  
To change the instrument you are writing with select it from the drop down list at the top. Make sure to only use the instrument in the correct channels otherwise it will not work on the GameBoy. For example, channel 4 should only use instruments from 16->32, these are labelled with a '4' in the instrument name.  
Double click on a note to change it's data, this will bring up the following dialog:
![OpenMPT note edit dialog](/docs/noteeditdialog.PNG?raw=true)
Change the note or instrument here and also adjust the effect for this note, most commonly you will want to adjust the volume.  
To adjust the pattern sequence click and drag them along the sequence boxes. Right click to copy/delete patterns.

## Playback 
Playback can be controlled either for the pattern you are writing or for the whole sequence:
![Labelled OpenMPT playback interface](/docs/labelledplayback.PNG?raw=true)
A useful button to know about is the 'Stop hanging note' button, this is needed as sometimes a note will continue to play even when the playback is stopped. 

## GameBoy conversion
Once you have your song written the next step is to convert the '.mod' file that OpenMPT uses into the code which can be used to compile in your Game Boy game. The tools used for this is 'mod2gbt' and is provided in the 'gbt-player' folder of this repository.
Use the following command in the terminal to convert the '.mod' file:
```
mod2gbt [FILENAME].mod [SONGNAME] -c 2
```
Replace [FILENAME] with your '.mod' filename and [SONGNAME] with the name of the song e.g.:
```
mod2gbt sample.mod sampleSong -c 2
```
This will create a output.c file with the music data stored in an array. To use this file your game needs to be compiled with the gbt_player files 'gbt_player.h', 'gbt_player.s', and 'gbt_player_bank1.s' provided in this repository in the 'gbt-player' folder. The '.s' files are assembly files which hold the code to be able to playback music. I won't go into detail here about compiling GameBoy files, but note that you will need to compile these into '.obj' files and then compile your game. For example:
```
lcc -Wa-l -Wl-m -Wl-j -DUSE_SFR_FOR_REG -c -o main.o main.c
lcc -Wa-l -Wl-m -Wl-j -DUSE_SFR_FOR_REG -c -o output.o output.c
lcc -Wa-l -Wl-m -Wl-j -DUSE_SFR_FOR_REG -c -o gbt_player.o gbt_player.s
lcc -Wa-l -Wl-m -Wl-j -DUSE_SFR_FOR_REG -c -o gbt_player_bank1.o gbt_player_bank1.s

lcc -Wa-l -Wl-m -Wl-j -DUSE_SFR_FOR_REG -Wl-yt1 -Wl-yo4 -Wl-ya0 -o music.gb main.o output.o gbt_player.o gbt_player_bank1.o
```
See [GBJam8](https://github.com/GamesKnightStudios/GBJam8) for a game that uses this for it's music. 
