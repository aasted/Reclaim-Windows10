Aasted: Run win10.ps1 to clean up windows 10 with the settings I used in 2023.

## Description

This is a PowerShell script for automation of routine tasks done after fresh installations of Windows 10 and Windows Server 2016. This is by no means any complete set of all existing Windows tweaks and neither is it another "antispying" type of script. It's simply a setting which I like to use and which in my opinion make the system less obtrusive.

This repository has been originally created as complementary to article <https://www.dasm.cz/clanek/jak-z-windows-10-udelat-desktopovy-system> (written in Czech) which explains the respective snippets a bit more in detail. The article was last updated on 2016-08-15 and will not be updated further. All development and discussion has been moved here.

## Usage
If you just want to run the script with default preset, simply right click on the *Win10.ps1* file, choose *Run with PowerShell*, and confirm execution policy change. Make sure your account is a member of *Administrators* group as the script attempts to run with elevated privileges.

### Advanced usage
The script consists of separate functions, each of which contains one tweak. The functions can be grouped to *presets*. Preset is simply a list of function names which should be called. If you don't supply any specific preset, the default preset defined by `$tweaks` array in the beginning of the script will be applied. Any function which is not present or is commented in a preset will not be called, thus the corresponding tweak will not be applied. If you choose to fork the script and adjust the defaults instead of creating a customized preset file, then all you have to modify is the `$tweaks` array.

To supply a customized preset, you can either pass the function names directly as parameters.

    powershell.exe -NoProfile -ExecutionPolicy Bypass -File Win10.ps1 EnableFirewall EnableDefender

Or you can create a file where you write the function names (one function name per line, no commas, whitespaces allowed, comments on separate lines starting with `#`) and then pass the filename using *-preset* parameter.  
Example of a preset file `mypreset.txt`:

    # Security tweaks
    EnableFirewall
    EnableDefender

    # UI tweaks
    ShowKnownExtensions
    ShowHiddenFiles

Command using the preset file above:

    powershell.exe -NoProfile -ExecutionPolicy Bypass -File Win10.ps1 -preset mypreset.txt

## FAQ

**Q:** Can I run the script safely?  
**A:** Definitely not. You have to understand what the functions do and what will be the implications for you if you run them. Some functions lower security, hide controls or uninstall applications. **If you're not sure what the script does, do not attempt to run it!**

**Q:** Can I run the script repeatedly?  
**A:** Yes! In fact the script has been written to support exactly this as it's not uncommon that big Windows Updates reset some of the settings.

**Q:** Can I run the script in multi-user environment?  
**A:** Yes, to certain extent. Some tweaks (most notably UI tweaks) are set only for the user currently executing the script. As stated above, the script can be run repeatedly; therefore it's possible to run it multiple times, each time as different user. Due to the nature of authentication and privilege escalation mechanisms in Windows, the script can be successfully applied only for users belonging to *Administrators* group. Standard users will get an UAC prompt asking for admin credentials which then causes the tweaks to be applied to the given admin account instead of the original non-privileged one. To circumvent this, add the standard user to the *Administrators* group, run the script, and then remove the user from *Administrators* group again. There are a few ways how the same functionality can be achieved programmatically, but I'm not planning to include any of them as it would negatively impact code complexity and readability. If you still wish to try to use the script in multi-user environment, check [this answer in issue #29](https://github.com/Disassembler0/Win10-Initial-Setup-Script/issues/29#issuecomment-333040591) for some pointers.

**Q:** Can I run the script on Windows Server 2016?  
**A:** Yes. Starting from version 2.5, Windows Server 2016 is supported. There are even few tweaks specific to Server environment. Keep in mind though, that the script is still primarily designed for Windows 10, so you have to create your own preset for Windows Server 2016.

**Q:** Can I run the script on Windows 7, 8, 8.1 or other versions of Windows?  
**A:** No. Although some tweaks may work also on older versions of Windows, the script is developed only for Windows 10 and Windows Server 2016. There are no plans to add support for older versions.

**Q:** Did you test the script?  
**A:** Yes. I'm testing new additions on up-to-date Home and Enterprise editions in VMs. I'm also regularly using it for all my home installations after all bigger updates.

**Q**: I've run the script and it did xxx, how can I undo it?  
**A:** For every tweak, there is also a corresponding function which restores the default settings. The default is considered freshly installed Windows 10 or Windows Server 2016 with no adjustments made during or after the installation. Use the tweaks to create and run new preset. Alternatively, since most functions are just automation for actions which can be done using GUI, find appropriate control and modify it manually.

**Q:** I've run the script and it broke my computer / killed neighbor's dog / caused world war 3.  
**A:** I don't care. Also, that's not a question.

**Q:** I'm using a tweak for xxx on my installation, can you add it?  
**A:** Submit a PR or drop me a message. If I find the functionality simple, useful and not dependent on any 3rd party modules or executables, I might add it.

**Q:** Can I use the script or modify it for my / my company's needs?  
**A:** Sure, knock yourself out. Just don't forget to include copyright notice as per MIT license requirements. I'd also suggest including a link to this GitHub repo as it's very likely that something will be changed, added or improved to keep track with future versions of Windows 10.

**Q:** Why are there repeated pieces of code throughout some functions?  
**A:** So you can directly take the function block and use it elsewhere, without elaborating on any dependencies.

**Q:** For how long are you going to maintain the script?  
**A:** As long as I use Windows 10.

**Q:** I really like the script. Is there any way to express gratitude and send donation?  
**A:** Feel free to send donations via [PayPal donation link](https://www.paypal.com/cgi-bin/webscr?cmd=_donations&business=disassembler%40dasm%2ecz&item_name=Disassembler&no_shipping=1). Any amount is appreciated, just be aware that PayPal charges [mediation fees](https://www.paypal.com/selfhelp/article/FAQ690). Also be aware that donations are completely voluntary and I'm not obliged to make any script adjustments in your favor regardless of the donated amount.

## Contribution guidelines

Following is a list of rules which I'm trying to apply in this project. The rules are not binding and I accept pull requests even if they don't adhere to them, as long as their purpose and content are clear. In rare cases, when there are too many rule violations, I might simply redo the whole functionality and reject the PR (while still crediting you). If you'd like to make my work easier, please consider adhering to the following rules too.

### Function naming
Try to give a function a meaningful name up to 25 characters long, which gives away the purpose of the function. Use verbs like `Enable`/`Disable`, `Show`/`Hide`, `Install`/`Uninstall`, `Add`/`Remove` in the beginning of the function name. In case the function doesn't fit any of these verbs, come up with another name, beginning with the verb `Set`, which indicates what the function does, eg. `SetCurrentNetworkPrivate` and `SetCurrentNetworkPublic`.

### Revert functions
Always add a function with opposite name (or equivalent) which reverts the behaviour to default. The default is considered freshly installed Windows 10 or Windows Server 2016 with no adjustments made during or after the installation. If you don't have access to either of these, create the revert function to the best of your knowledge and I will fill in the rest if necessary.

### Function similarities
Check if there isn't already a function with similar purpose as the one you are trying to add. As long as the name and objective of the existing function is unchanged, feel free to add your tweak to that function rather than creating a new one.

### Function grouping
Try to group functions thematically. There are already five major groups (privacy, services, user interface, applications and server-specific), but even within these, some tweaks may be related to each other. In such case, add a new tweak below the existing one and not to the end of the whole group.

### Default preset
Always add a reference to the tweak and its revert function in the `$tweaks` array containing the default set of tweaks. Add references to both functions on the same line (mind the quotes and commas) and always comment out the revert function. Whether to comment out also the tweak in the default preset is a matter of personal preference. The rule of thumb is that if the tweak makes the system faster, smoother, more secure and less obtrusive, it should be enabled by default. Usability has preference over performance (that's why e.g. indexing is kept enabled). Also don't forget to add the function references to all test files.

### Repeatability
Unless applied on unsupported system, all functions have to be applicable repeatedly without any errors. When you're creating a registry key, always check first if the key doesn't happen to already exist. When you're deleting registry value, always append `-ErrorAction SilentlyContinue` to prevent errors while deleting already deleted values.

### Input / output hiding
Suppress all output generated by commands and cmdlets using `| Out-Null` or `-ErrorAction SilentlyContinue` where applicable. Whenever an input is needed, use appropriate parameters to suppress the prompt and programmatically provide values for the command to run (eg. using `-Confirm:$false`). The only acceptable output is from the `Write-Host` cmdlets in the beginning of each function and from non-suppressible cmdlets like `Remove-AppxPackage`.

### Registry
Create the registry keys only if they don't exist on fresh installation if Windows 10 or Windows Server 2016. When deleting registry, delete only registry values, not the whole keys. When you're setting registry values, always use `Set-ItemProperty` instead of `New-ItemProperty`. When you're removing registry values, choose either `Set-ItemProperty` or `Remove-ItemProperty` to reinstate the same situation as it was on the clean installation. Again, if you don't know what the original state was, let me know in PR description and I will fill in the gaps. When you need to use `HKEY_USERS` registry hive, always add following snippet before the registry modification to ensure portability.

```powershell
If (!(Test-Path "HKU:")) {
    New-PSDrive -Name HKU -PSProvider Registry -Root HKEY_USERS | Out-Null
}
```

### Force usage
Star Wars jokes aside, don't use `-Force` parameter unless absolutely necessary. The only permitted case is when you're creating a new registry key (not a value) and you need to ensure that all parent keys will be created as well. In such case always check first if the key doesn't already exist, otherwise you will delete all its existing values.

### Comments
Always add a simple comment above the function briefly describing what the function does, especially if it has an ambiguous name or if there is some logic hidden under the hood. If you know that the tweak doesn't work on some editions of Windows 10 or on Windows Server, state it in the comment too. Add a `Write-Host` cmdlet with the short description of action also to the first line of the function body, so the user can see what is being executed and which function is the problematic one whenever an error occurs. The comment is written in present simple tense, the `Write-Host` in present continuous with ellipsis (resp. three dots) at the end.

### Coding style
Indent using tabs, enclose all string values in double quotes (`"`) and strictly use `PascalCase` wherever possible. Put opening curly bracket on the same line as the function name or condition, but leave the closing bracket on a separate line for readability.

### Examples

**Naming example**: Consider function `EnableFastMenu`. What does it do? Which menu? How fast is *fast*? A better name might be `EnableFastMenuFlyout`, so it's a bit clearer that we're talking about the menu flyouts delays. But the counterpart function would be `DisableFastMenuFlyouts` which is not entirely true. We're not *disabling* anything, we're just making it slow again. So even better might be to name them `SetFastMenuFlyouts` and `SetSlowMenuFlyouts`. Or better yet, just add the functionality to already existing `SetVisualFXPerformance`/`SetVisualFXAppearance`. Even though the names are not 100% match, they aim to tweak similar aspects and operate within the same registry keys.

**Coding example:** The following code applies most of the rules mentioned above (naming, output hiding, repeatability, force usage, comments and coding style).

```powershell
# Enable some feature
Function EnableSomeFeature {
    Write-Host "Enabling some feature..."
    If (!(Test-Path "HKLM:\Some\Registry\Key")) {
        New-Item -Path "HKLM:\Some\Registry\Key" -Force | Out-Null
    }
    Set-ItemProperty -Path "HKLM:\Some\Registry\Key" -Name "SomeValueName" -Type String -Value "SomeValue"
}

# Disable some feature
Function DisableSomeFeature {
    Write-Host "Disabling some feature..."
    Remove-ItemProperty -Path "HKLM:\Some\Registry\Key" -Name "SomeValueName" -ErrorAction SilentlyContinue
}
```
