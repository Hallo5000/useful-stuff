## This is on how to increase the player cap for the game RV There Yet? (on Steam)

The following is working for Windows **AND** Linux (Steam with Proton)

### How To:

**Where?**
- Windows: `%localappdata%\Ride\Saved\Config\Windows\`
- Linux: `SteamLibrary/steamapps/compatdata/3949040/pfx/drive_c/users/steamuser/AppData/Local/Ride/Saved/Config/Windows/`

**What?**

Inside the path add the following to `Game.ini`: (if it doesn't exist, create the file)
```
[/script/engine.gamesession]
MaxPlayers=8
```

Lastly: Make the file `Read-Only` (under `Properties` of the file)


### Sources/Additional Infos:
- The change to `GameUserSettings.ini` is not necessary
- The value is apparently saved in an Integer and therefore is capped at `2147483647` (higher numbers result in the same cap)
- Everything lower than `2` (including negative numbers) results in the lobby not being shown to others and decimal numbers are always rounded down
- It seems like even though in the "Join" list it states the modified cap, the game doesn't let more than 8 players join one lobby (tested with a public lobby)

Source: https://steamcommunity.com/sharedfiles/filedetails/?id=3595429276
