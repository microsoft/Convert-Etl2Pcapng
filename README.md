# Overview

This tool acts as a simple wrapper for [etl2pcapng.exe](https://github.com/microsoft/etl2pcapng).

ETL files, generated by using commands such as "netsh trace start", are created by Windows in-box packet capture and event collection solutions. Think of ETL/ETW logging as something similar to tcpdump plus strace plus dtrace in Linux/Unix, but in a single tool. 

The ETL file format cannot be natively opened by any currently supported Microsoft tool. This poses a problem for people who want to use Windows in-box packet capture functionality.

etl2pcapng was built to extract packets out of ETL files and convert them to a Wireshark readable format, pcapng. This PowerShell wrapper extends the functionality of, and provides automated management and updates for, etl2pcapng.exe.

# Install

The module can be downloaded using the following command in PowerShell.

`Install-Module Convert-Etl2Pcapng -Scope CurrentUser`

Use this command to install without any prompts, assuming you accept the MIT license used.

`Install-Module Convert-Etl2Pcapng -Scope CurrentUser -Force`

It is possible that a new version of PowerShellGet will be needed before the module will install from PSGallery. Run these three commands to update all the necessary components, restart PowerShell, and then try to install Convert-Etl2Pcapng again.

```
    # Make sure all other instances of PowerShell, including VS Code, PowerShell IDE, etc. are closed
    Install-PackageProvider -Name NuGet -MinimumVersion 2.8.5.201 -Force
    Install-Module -Name PowerShellGet -MinimumVersion 2.2.4.1 -Force -AllowClobber
    
    # A restart of PowerShell is required if the module was updated
 ```


# Usage

### Convert-Etl2Pcapng

Used to automate etl2pcapng conversion. Accepts a literal path to a location containing ETL files or the literal path to a single ETL file. Paths from the pipeline are accepted.

The Recurse parameter will traverse child directories for ETL files. Only valid when the path is a directory.

The Out parameter can be used to store the results in a new location; otherwise, the same path as the ETL file is used.

### Register-Etl2Pcapng

__*Requires elevated rights (Run as administrator)*__

Registers a shell context menu item for Convert-Etl2Pcapng. Right-clicking on an ETL fill will show an option "Convert with etl2pcapng". This will execute Convert-Etl2Pcapng with default settings against the ETL file.

UseVerbose and UseDebug can be used to enable cli logging to troubleshoot issues with the menu option.

### Unregister-Etl2Pcapng

__*Requires elevated rights (Run as administrator)*__

Unregisters the shell context menu item for Convert-Etl2Pcapng. This will remove the option to right-click on an ETL file and select "Convert with etl2pcapng". 

### Update-Etl2Pcapng

Gets the newest version of etl2pcapng.exe from GitHub and returns the path to etl2pcapng.exe to the caller. This cmdlet generally does not need to be run as Convert-Etl2Pcapng executes this cmdlet. 

GitHub is only queried every 7 days or when the Force parameter is used.

The module files, including etl2pcapng, are stored in %LocalAppData%\etl2pcapng so an elevated prompt is not needed to execute the commands.


# Cleaning up old versions

These commands should cleanup all Convert-Etl2Pcapng modules and files.

```
# cleanup old versions of Convert-Etl2Pcapng
Get-Module -ListAvailable Convert-Etl2Pcapng | Uninstall-Module -Force

# OneDrive safe cleanup method
$modPaths = $env:PSModulePath -split ';'

foreach ($path in $modPaths)
{
    $isC2PFnd = Get-Item "$path\Convert-Etl2Pcapng" -EA SilentlyContinue

    if ($isC2PFnd)
    {
        # first delete all the files    
        $childs = Get-ChildItem -LiteralPath "$($isC2PFnd.FullName)" -Recurse -Force -File
        foreach ($child in $childs) 
        {
            $child.Delete()
        }

        # take a nap while OneDrive catches up
        sleep 5

        # now get the directories
        $childs = Get-ChildItem -LiteralPath "$($isC2PFnd.FullName)" -Recurse -Force
        foreach ($child in $childs) 
        {
            $child.Delete()
        }

        # take a nap while OneDrive catches up
        sleep 5

        # finally nuke the root dir
        $isC2PFnd.Delete($true)
    }
}
```

Rerun the `# OneDrive safe cleanup method` portion of the commands a second time if you get errors during the initial execution. OneDrive and PowerShell don't always play nice with each other.

If all else fails then go in and delete the modules manually.


# Known issues

All known breaking errors have been fixed. Please post an Issue if you find one. You can enter debug mode for the shell menu by registering using this command.

```
Register-Etl2Pcapng -UseDebug
```

All cmdlets also support -Verbose and file redirection for troubleshooting.



# Privacy

This PowerShell module does not collect or upload data to Microsoft, third-parties, or partners. 

Tracking and other statistical website data may be collected by PowerShellGallery.com when the module is downloaded, and by Github.com when the etl2pcapng.zip file is downloaded or updated by the module during cmdlet execution.


# Contributing

This project welcomes contributions and suggestions.  Most contributions require you to agree to a
Contributor License Agreement (CLA) declaring that you have the right to, and actually do, grant us
the rights to use your contribution. For details, visit https://cla.opensource.microsoft.com.

When you submit a pull request, a CLA bot will automatically determine whether you need to provide
a CLA and decorate the PR appropriately (e.g., status check, comment). Simply follow the instructions
provided by the bot. You will only need to do this once across all repos using our CLA.

This project has adopted the [Microsoft Open Source Code of Conduct](https://opensource.microsoft.com/codeofconduct/).
For more information see the [Code of Conduct FAQ](https://opensource.microsoft.com/codeofconduct/faq/) or
contact [opencode@microsoft.com](mailto:opencode@microsoft.com) with any additional questions or comments.
