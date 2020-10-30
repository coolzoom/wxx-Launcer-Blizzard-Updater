# Blizzard Installer Generator

### Quick Start

1. Put things to install in the "data" dir
2. Open "res/data" dir and edit patch.cmd to set the target and source version if updating the wow.exe 
3. Open "res/data" dir and edit patch.html and patch.txt with your custom notes.
2. run "createInstaller.bat"

### rules.txt

This file holds the ruleset for listgenerator. its format is pretty simple:
```
<realpath>;<replacement>;<patch>
```

## Making a patch for use with launcher

Make a new MPQ and add your Blizzard Installer to it.
Make a file called "prepatch.lst". Add these two lines to it:
(Replace test.exe with your installer name)

extract test.exe
execute test.exe

Add this file to the MPQ.

Now you can send this MPQ to people logging into the server.


I have a job that builds the installer like this:
```bash
cd "C:\Users\Administrator\Desktop\ClientBuild"
git pull
cd "C:\Users\Administrator\Desktop\Tools\Installer\Blizzard-Updater"
rmdir /s /q data
mkdir data
copy Wow_HourOfTwilight.exe data\Wow_HourOfTwilight.exe
robocopy "C:\Users\Administrator\Desktop\ClientBuild" "C:\Users\Administrator\Desktop\Tools\Installer\Blizzard-Updater\data" /S /E /xd ".git" "_DO_NOT_PACKAGE" /xf ".gitattributes" ".gitignore"
call "C:\Program Files\7-Zip\7z.exe" e -y -aoa "C:\Users\Administrator\Desktop\Tools\Installer\Blizzard-Updater\data\DBFilesClient\Item.zip" -oC:\Users\Administrator\Desktop\Tools\Installer\Blizzard-Updater\data\DBFilesClient
del C:\Users\Administrator\Desktop\Tools\Installer\Blizzard-Updater\data\DBFilesClient\Item.zip
bin\headergenerator.exe data data_tmp
bin\listgenerator.exe data_tmp data_tmp\patch.lst
echo Wow_HourOfTwilight.exe;pc-game-hdfiles\Wow_HourOfTwilight.exe;base >> "C:\Users\Administrator\Desktop\Tools\Installer\Blizzard-Updater\data_tmp\patch.lst"
xcopy res\data data_tmp /c /f /y /e /h
bin\mpqcreate.exe tmp.mpq data_tmp
bin\append.exe res\base.exe HourOfTwilight_Installer.exe tmp.mpq res\Installer.exe res\RichEd20.dll res\Unicows.dll
del tmp.mpq
rmdir data_tmp /Q /S
```
