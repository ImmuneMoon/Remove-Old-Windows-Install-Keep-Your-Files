# 🧹 Remove an Old Windows Install and Keep Your Files (Windows 10 & 11)

## The Problem
You installed Windows on a new drive, and your old drive still has the previous Windows install on it along with your files. The new drive is too small to hold those files, so they need to stay where they are. Formatting the old drive would erase everything, so you need to take Windows off it and leave your files in place.

## How an Old Install Actually Works
An old install is mostly a handful of folders: `Windows`, `Program Files`, `Program Files (x86)`, `ProgramData`, and `Users`. Deleting them sounds simple, but four things get in the way:

* **Your files are inside those folders.** Your Documents, Pictures, and Desktop all live under `Users`, so they have to move out first.
* **The folders are locked.** Their permissions belong to Windows itself and to user accounts that no longer exist, so your new account can't simply delete them.
* **The folders contain hidden links.** Old Windows folders hold junctions, which act like shortcuts to paths such as `C:\Users`. On the old install, `C:` meant the old drive. Now `C:` means your **new** Windows, so a careless command can follow a link off the old drive and into your new system.
* **Your new Windows may need the old drive to boot.** If the old drive was connected during the install, the new Windows may have put its boot files there.

The commands in this guide are built around those last two problems, which is why they differ from the ones you'll usually find online (see **Fixes That Don't Help**).

> **The old drive holds the only copy of your files.** Nothing here should touch them, but you'll be running delete commands on the drive they live on. Copy anything you can't afford to lose somewhere else first, even if it's only your most important folder on a USB stick.

## Before You Start
Do these while you can still boot the old Windows. If it's already too late, skip to **Step 1**.

1. **Back up what you can't lose.** A USB stick or cloud storage is fine.
2. **Check for drive encryption.** This matters most, because the new Windows can't open an encrypted drive without its key.
   * **Windows 11:** **Settings** > **Privacy & security** > **Device encryption**.
   * **Windows 10:** **Settings** > **Update & Security** > **Device encryption**.
   * **Pro editions:** Search for **Manage BitLocker** in the Start menu.
   * If it's on, turn it off and wait for it to finish. If you can't, save your recovery key from [aka.ms/myrecoverykey](https://aka.ms/myrecoverykey).
3. **Save your browser passwords.** They're tied to your old Windows login, so copying the browser's folder won't bring them with you. Sign in to your browser's sync, or export them to your password manager.
4. **Note any license keys,** and sign out of apps that limit how many devices you can use.

> **Encrypted files:** If you ever used **Properties** > **Advanced** > **Encrypt contents to secure data** on a file, decrypt it now. It won't open from the new Windows otherwise.

## Step 1: Install Windows With the Old Drive Unplugged
Follow the [Windows Install Guide](https://github.com/ImmuneMoon/Windows-Install-Guide) to make your bootable USB and install Windows on the new drive, with one change:

1. **Shut down and unplug the old drive first.** Turn off the power supply, then disconnect the old drive's data cable. With the old drive connected, Windows Setup may reuse its boot partition instead of making one on the new drive. Everything would seem fine until you cleaned up the old drive, and then the PC wouldn't start.
2. When the installer asks where to put Windows, check that only the new drive is listed.
3. Finish setting up Windows.

**Not sure whether to install Windows 10 or 11?** The [Windows Install Guide](https://github.com/ImmuneMoon/Windows-Install-Guide) shows how to check your PC and what to do either way. If you're staying on Windows 10, read the [Windows 10 Community Support Guide](https://github.com/ImmuneMoon/Windows-10-Community-Support-Guide) too.

If you already installed Windows with the old drive connected, that's fine. **Step 2** will show you whether it caused a problem.

## Step 2: Confirm the New Drive Boots on Its Own
Don't skip this, even if you unplugged the old drive for the install.

1. Shut down and disconnect the old drive, if it isn't already.
2. Turn the PC on.
   * If Windows loads, the new drive stands on its own. Continue to step 3.
   * If you get a "no boot device" style error, go to **If the New Drive Won't Boot Alone**.
3. Shut down and reconnect the old drive.
4. Open your BIOS and put the new drive first in the boot order. The [Windows Install Guide](https://github.com/ImmuneMoon/Windows-Install-Guide) shows how to get there. You may see two entries called **Windows Boot Manager**, so pick the one showing the new drive's name.
5. If a menu now asks which Windows to start, remove the old entry:
   1. Press `Windows Key + R`, type `msconfig`, and press **Enter**.
   2. On the **Boot** tab, select the old install and click **Delete**.
   3. Make sure the current Windows is marked as the default, then click **OK**.

### If the New Drive Won't Boot Alone
The new Windows is using boot files on the old drive. There are two ways out.

**Easiest: reinstall.** The new install is fresh, so you have little to lose. Keep the old drive unplugged and run the Windows installer again.

**Or rebuild the boot files.** This assumes a UEFI system, which covers every Windows 11 PC and nearly every PC from the last decade. If `create partition efi` gives an error, your drive isn't set up that way, so reinstall instead.

1. Reconnect the old drive and boot into the new Windows.
2. Open the Start menu, type `cmd`, right-click **Command Prompt**, and choose **Run as administrator**.
3. Type these, using the size column to find the number of your **new** drive:
   ```cmd
   diskpart
   list disk
   select disk 0
   list partition
   ```
4. If the list shows a partition of type **System**, give it a letter, using its number in place of `1`:
   ```cmd
   select partition 1
   assign letter=S
   exit
   ```
   If there's no **System** partition, make one:
   ```cmd
   select volume C
   shrink desired=300
   create partition efi size=260
   format quick fs=fat32
   assign letter=S
   exit
   ```
5. Write the boot files, then go back to the start of **Step 2**:
   ```cmd
   bcdboot C:\Windows /s S: /f UEFI
   ```

## Step 3: Find the Old Drive's Letter
1. Open **File Explorer** > **This PC** and find the old drive. It will have a letter such as `D:` or `E:`.
   * **Not listed?** Right-click **Start** and choose **Disk Management**. If the disk says **Offline**, right-click it and choose **Online**. If its partition has no letter, right-click it and choose **Change Drive Letter and Paths**.
   * **Shows a padlock?** It's encrypted. Double-click it and enter your recovery key.
2. Open the Start menu, type `cmd`, right-click **Command Prompt**, and choose **Run as administrator**. Keep this window open for the rest of the guide.
3. Run these, replacing `D:` with your old drive's letter:
   ```cmd
   set OLD=D:
   echo %SystemDrive%
   dir /a "%OLD%\"
   ```
4. Check the results:
   * The `echo` line shows the drive you're running from. **If it matches your old drive's letter, stop.** You have the wrong drive.
   * The `dir` list should show the old drive's `Windows` and `Users` folders along with anything of your own.

Every command below uses `%OLD%`, so you can paste them as they are. If you close the window, run the `set` line again.

## Step 4: Move Your Files Out of the Windows Folders
Moving a folder within the same drive is instant and needs no free space, which is why this works when your new drive is too small.

1. Create a folder to hold everything, then list the old accounts:
   ```cmd
   mkdir "%OLD%\OLD_DATA_KEEP"
   dir /a "%OLD%\Users"
   ```
2. Move each account you want to keep, replacing `YourOldName` with the folder's name. This takes the whole account, including hidden things like game saves and app settings, so nothing gets missed. You can sort through it later.
   ```cmd
   move "%OLD%\Users\YourOldName" "%OLD%\OLD_DATA_KEEP\"
   ```
   Repeat this for any other accounts, and for `Public` if you kept files in the shared folders. Skip `Default`, `Default User`, and `All Users`. Those belong to Windows.
3. Check the other places your things may be hiding. File Explorer may ask for permission to open these, so click **Continue**.

   | Location | What to look for |
   |---|---|
   | `Program Files` and `Program Files (x86)` | Games and anything you installed by hand. Steam's `Steam\steamapps` folder holds your installed games, if you'd rather not download them again. |
   | `ProgramData` | Data for older apps and some games. |
   | `Windows.old` | Your files from before an earlier upgrade, if this folder exists. |
   | `$Recycle.Bin` | Anything you deleted but never emptied. |

   Move anything you want into `OLD_DATA_KEEP`. Folders of your own at the top of the drive are safe, because the commands below only delete folders they name.

## Step 5: Delete the Old Windows Folders
> **This is the point of no return.** Run `dir "%OLD%\OLD_DATA_KEEP"` and make sure your accounts are there. Use these commands exactly as written. In particular, keep `/d n` as it is, and don't swap it for `/d y` (see **Fixes That Don't Help**).

1. **Remove what you already have rights to.** This clears most of the hidden links first. Some `Access is denied` lines are normal.
   ```cmd
   rd "%OLD%\Documents and Settings"
   rd /s /q "%OLD%\Users"
   rd /s /q "%OLD%\ProgramData"
   ```
2. **Make a list of what to remove.** This doesn't delete anything yet.
   ```cmd
   set LIST="Windows" "Program Files" "Program Files (x86)" "ProgramData" "Users"
   set LIST=%LIST% "$SysReset" "$WinREAgent" "$WINDOWS.~BT" "Boot" "PerfLogs" "Recovery"
   ```
3. **Take ownership.** Run this whole block **twice**, letting it finish each time. Locked folders only open up on the second pass. It can take a while on a hard drive, and the window may sit quietly. `Access is denied` lines ending in `\*` are the hidden links refusing entry, which is what you want.
   ```cmd
   for %F in (%LIST%) do @if exist "%OLD%\%~F" (
     takeown /f "%OLD%\%~F" /r /d n >nul
     icacls "%OLD%\%~F" /grant Administrators:F /t /c /q >nul
   )
   ```
4. **Delete the folders.**
   ```cmd
   for %F in (%LIST%) do @if exist "%OLD%\%~F" rd /s /q "%OLD%\%~F"
   ```
5. **Delete the leftover system files.** `Could Not Find` lines are fine, since not every drive has all of them.
   ```cmd
   del /f /a "%OLD%\pagefile.sys" "%OLD%\hiberfil.sys" "%OLD%\swapfile.sys"
   del /f /a "%OLD%\bootmgr" "%OLD%\BOOTNXT" "%OLD%\DumpStack.log.tmp"
   ```
6. **Check what's left.**
   ```cmd
   dir /a "%OLD%\"
   ```
   If a listed folder is still there, repeat parts 3 and 4. Leave `System Volume Information` alone, because Windows uses it for every drive. `Windows.old` and `$Recycle.Bin` are left on purpose. Once you've checked them, add them to the `LIST` line and repeat parts 2 to 4.

## Step 6: Fix the Permissions on Your Kept Files
Your kept files still have permissions from the old accounts, and they still contain hidden links. This removes the links and gives the folder the drive's normal permissions, so your new account can open everything without prompts.

```cmd
for /f "delims=" %i in ('dir "%OLD%\OLD_DATA_KEEP" /a:l /s /b 2^>nul') do @rd "%i"
icacls "%OLD%\OLD_DATA_KEEP" /reset /t /c /q
```

`The directory name is invalid` lines are harmless. They come from link-type files such as cloud placeholders, and plain `rd` can't delete anything with contents, so your files are safe.

You can now close the window and sort through `OLD_DATA_KEEP` at your own pace. To see `AppData`, turn on hidden items in File Explorer's **View** menu.

## Optional: Remove the Old Hidden Partitions
The old drive still has small hidden partitions from Windows, usually under 1 GB in total. They're harmless, so you can leave them. Removing them mostly gets rid of the extra **Windows Boot Manager** entry in your BIOS.

Only do this after passing **Step 2**, because these partitions are what your PC would be booting from if it failed. Disk Management won't delete them, so use `diskpart`:

```cmd
diskpart
list disk
select disk 1
list partition
```

Use the size column to pick your **old** drive, then remove each hidden partition by its number:

```cmd
select partition 1
delete partition override
```

Only delete partitions of type **System**, **Reserved**, or **Recovery**. **Never delete a Primary partition,** because that's the one holding your files. Type `exit` when you're done.

## Another Way: If You Have Somewhere to Put Everything
If you can borrow an external drive big enough for your files, wiping the old drive is cleaner. It removes every hidden partition and permission problem in one go.

1. Copy your files to the external drive, then open a few to confirm they copied properly.
2. In an administrator Command Prompt, run `diskpart`, `list disk`, and `select disk` with your **old** drive's number, then run `clean` and `exit`. This erases the whole drive.
3. In **Disk Management**, initialize the disk as **GPT** when asked. Right-click the unallocated space, choose **New Simple Volume**, and follow the prompts.
4. Copy your files back.

## Fixes That Don't Help
* **Disk Cleanup's "Previous Windows installation(s)" box.** This cleans up a `Windows.old` folder left behind by an upgrade. An old install on another drive isn't one of those, so it won't remove it.
* **Deleting the folders in File Explorer.** Windows blocks this with permission errors, even for administrators.
* **Removing the install in `msconfig`.** This only takes the entry off the boot menu. Every file stays on the drive.
* **`takeown` with `/d y`.** This is in most online guides, and it's the reason this guide uses `/d n`. Hidden links refuse to be opened, and `y` tells `takeown` to overwrite that refusal, so it walks through the link and changes files on your new `C:` drive.
* **Mirroring an empty folder with `robocopy`.** This popular trick for deleting stubborn folders is dangerous here. In testing it deleted files on the far side of a link, even with the `/XJ` option meant to skip links.

## Quick Checklist
- [ ] Backed up what I can't lose
- [ ] Turned off encryption, or saved the recovery key
- [ ] Saved browser passwords and license keys
- [ ] Installed Windows with the old drive unplugged
- [ ] New drive boots with the old drive disconnected (**Step 2**)
- [ ] Set the new drive first in the BIOS boot order
- [ ] Set `OLD` and confirmed it isn't the system drive
- [ ] Moved accounts into `OLD_DATA_KEEP` and checked the other folders
- [ ] Deleted the Windows folders, using `/d n`
- [ ] Reset permissions on `OLD_DATA_KEEP`
