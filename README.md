# README: Remove an Old Windows Install and Keep Your Files

![Windows 10 | 11](https://img.shields.io/badge/Windows-10%20%7C%2011-0078D4?logo=windows&logoColor=white)
![Troubleshooting guide](https://img.shields.io/badge/Type-troubleshooting-6f42c1)
![Requires administrator](https://img.shields.io/badge/Requires-Administrator-d73a49)
[![Last updated](https://img.shields.io/github/last-commit/ImmuneMoon/Remove-Old-Windows-Install-Keep-Your-Files?label=updated)](https://github.com/ImmuneMoon/Remove-Old-Windows-Install-Keep-Your-Files/commits/main)
[![License: CC BY 4.0](https://img.shields.io/badge/License-CC%20BY%204.0-lightgrey)](LICENSE)

**📄 [Download the PDF](Remove-Old-Windows-Install-Keep-Your-Files.pdf)**

## 📖 Overview
**Remove an Old Windows Install and Keep Your Files** is a step-by-step guide for a common situation after moving to a new boot drive: the old drive still holds a Windows install and all your files, and the new drive is too small to take them. Formatting isn't an option, so the guide removes Windows from the old drive and leaves your files where they are.

It also covers two problems most online guides skip: a new Windows that secretly depends on the old drive to boot, and cleanup commands that can follow hidden links off the old drive and into your new system.

## 🗂 Contents of the Guide
The main document (`Guide.md`) is divided into these sections:
1. **How an Old Install Actually Works** - What's on the drive, and what makes it hard to remove.
2. **Before You Start** - Backups, drive encryption, and saving your passwords while the old Windows still boots.
3. **Step 1: Install Windows With the Old Drive Unplugged** - Keeping boot files off the old drive, using the [Windows Install Guide](https://github.com/ImmuneMoon/Windows-Install-Guide).
4. **Step 2: Confirm the New Drive Boots on Its Own** - The test that makes everything after it safe, and what to do if it fails.
5. **Step 3: Find the Old Drive's Letter** - Pointing every later command at the right drive.
6. **Step 4: Move Your Files Out of the Windows Folders** - An instant move that needs no free space.
7. **Step 5: Delete the Old Windows Folders** - Commands that won't follow hidden links.
8. **Step 6: Fix the Permissions on Your Kept Files** - So your new account can open everything.
9. **Optional: Remove the Old Hidden Partitions** - For anyone who wants the last bit of cleanup.
10. **Another Way: If You Have Somewhere to Put Everything** - The cleaner option if you can borrow a drive.
11. **Fixes That Don't Help** - Common advice that won't work or isn't safe here.
12. **Quick Checklist** - A short summary to work through.

## ⚠️ Important Disclaimer
This guide runs permanent delete commands as an administrator on the drive that holds your files. Back up anything you can't afford to lose first, and check the drive letter in **Step 3** before you run anything. Deleted Windows folders can't be recovered, and your old install won't boot afterward. Don't use `/d y` with `takeown`, even though many other guides do.

## 🚀 How to Use
Download the PDF above, or open `Guide.md` in any Markdown viewer, text editor, or code editor (such as VS Code, Notepad++, or Obsidian). Start with **Before You Start** and work through the steps in order. Don't skip **Step 2**.

If you're staying on Windows 10, read the [Windows 10 Community Support Guide](https://github.com/ImmuneMoon/Windows-10-Community-Support-Guide) before you install.

## ☕ Support the Project

If you find this project helpful and want to support further development by Fulllion Creative Works, consider leaving a tip!

* [Donate via PayPal](https://www.paypal.com/donate/?hosted_button_id=LCDZX75HR4CLC)
* [Support on Ko-fi](https://ko-fi.com/fulllion)

---
© 2026 Fulllion Creative Works. All rights reserved.
