# Mod Loader

Mod Loader is a plugin for Grand Theft Auto III, Vice City and San Andreas that adds an easy and user-friendly way to install and uninstall modifications into the game, as if the game had official modding support. No changes are **ever** made to the original game files, everything is injected on the fly, at runtime!

The usage is as simple as inserting the mod files into the *modloader/* directory. Uninstalling is as easy as that too, delete the mod files and you are done. Hot swapping mods while the game is running? By using Mod Loader you can!

Still not sure? Check out [this](https://www.youtube.com/watch?v=TvRpQa8dJ7E) nice video from Ivey. For more, check out our [GTAForums](http://gtaforums.com/topic/669520-mod-loader/) thread and our [GTAGarage](http://www.gtagarage.com/mods/show.php?id=25377) page.

### Multiple game instances (local fork)

This fork isolates the menu's shared memory by process ID and uses an unnamed
semaphore for the sound-bank worker. This prevents a second game from using the
first game's menu pointers or consuming its sound-loading notifications.
The menu and sound replacements can remain enabled (`EnableMenu = true`, with
no `gta3.std.bank=0` priority override).

Build and replace both `modloader.asi` and
`modloader/.data/plugins/gta3/std.bank.dll` with all game instances closed.
Keep backups of both original files. To roll back, restore both backups; the
temporary workaround is `EnableMenu = false` and `gta3.std.bank=0` under
`[Priority]` in `modloader/.data/plugins/plugins.ini`.

These changes do not remove the game's own single-instance restriction; a
compatible multiprocess patch is still required. In-game validation with two
instances, the menu, and replacement sounds is required after installation.
Other plugins using the old, unsuffixed `GTA_PLUGIN_SDK` mapping will no longer
share this menu controller; cooperating MenuExtender plugins need the same
process-ID naming scheme.

### Building and Installing

Requirements:

+ [Premake 5](https://premake.github.io/download/) *(install with `winget install -e --id Premake.Premake.5.Beta`, or download from the official website)*
+ [Visual Studio](https://www.visualstudio.com/downloads) 2017 or greater.
+ [Windows XP Platform Toolset](https://learn.microsoft.com/en-us/cpp/build/configuring-programs-for-windows-xp) is required; add it from Visual Studio’s Individual Components Installer if needed.

Run the following command in the root of this directory to generate the project files:

    premake5 vs2022

On a Windows 10+ machine with only the v143 toolset, the two changed binaries
can also be built with the installed Windows SDK (adjust its version if needed):

```powershell
MSBuild build/modloader.vcxproj /p:Configuration=Release /p:Platform=Win32 /p:PlatformToolset=v143 /p:WindowsTargetPlatformVersion=10.0.26100.0 /m
MSBuild build/std.bank.vcxproj /p:Configuration=Release /p:Platform=Win32 /p:PlatformToolset=v143 /p:WindowsTargetPlatformVersion=10.0.26100.0 /m
```

This override does not preserve the upstream Windows XP compatibility target.

You can install the generated binaries into your game directory by running:

    premake5 install "C:/Program Files (x86)/Rockstar Games/GTA San Andreas"

Or, you might want the files to be automatically installed every time you build the solution:
 
    premake5 vs2022 "--idir=C:/Program Files (x86)/Rockstar Games/GTA San Andreas"
