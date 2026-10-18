# Deploying Log Lion in an organization

Organizations with a Log Lion license get a signed MSI installer, `LogLion-x64.msi`, that
installs silently with Intune, Configuration Manager, or any tool that runs a command as an
administrator. This page is also on our website as
[Log Lion for the enterprise](https://www.regallogic.com/enterprise/enterprise.html).

The installer is signed by REGAL LOGIC, LLC. It installs for every user of the machine and
needs administrator rights.

## Silent install

Install everything, with no user interface:

```
msiexec /i LogLion-x64.msi /qn
```

Each of the included [Enterprise extensions](../README.md#enterprise-extensions) is its own
installer feature. Leave out the ones you don't use with `REMOVE`, separating several with
commas:

```
msiexec /i LogLion-x64.msi /qn REMOVE=Ext_splunk,Ext_grafana_mcp
```

Or name exactly what to install with `ADDLOCAL`. `Complete` is Log Lion itself and is
required; any extension not listed is left out:

```
msiexec /i LogLion-x64.msi /qn ADDLOCAL=Complete,Ext_docker
```

| Feature | Installs |
| --- | --- |
| `Complete` | Log Lion, its help, and Start menu and desktop shortcuts (required) |
| `Ext_docker` | The Docker extension |
| `Ext_splunk` | The Splunk extension |
| `Ext_grafana_mcp` | The Grafana MCP extension |

Choosing extensions needs the Log Lion 3.1.4 installer or later; the 3.1.3 installer always
installs Docker. Feature names never change once released, so your scripts keep working.

The usual Windows Installer options work too:

```
msiexec /i LogLion-x64.msi /qn INSTALLDIR="D:\Tools\Log Lion" /l*v "%TEMP%\LogLion-install.log"
```

## Deploying the license

Log Lion looks for a `.Lic` file in two places. To license every user of a machine, copy the
file into the installation folder after installing:

```
copy /y LogLion.Lic "%ProgramFiles%\Log Lion\"
```

A license in the user's own `%APPDATA%\Log Lion` folder takes precedence, which is also
where **Load License** in **Help ▸ About** puts it. Upgrades leave a copied license file in
place.

Without a license file, Log Lion runs as a 30-day tryout with every feature, so you can pilot
it on a few machines first.

## Upgrades

Install a newer MSI the same way; there is no need to uninstall first. The older version is
removed and each machine keeps its choices: an extension left out before stays out. An
extension that is new in the upgrade is installed, like any default, so add its feature to
`REMOVE` if you don't want it. An older MSI will not install over a newer one.

## Deployment tools

### Microsoft Intune

**Apps ▸ Windows ▸ Add**, and choose **Line-of-business app**. Upload `LogLion-x64.msi`,
and under **App install command-line arguments** enter the features to leave out, for
example `REMOVE=Ext_splunk`. Intune installs it silently in the device context. Assign it
as Required to device groups. For a new version, update the app's package file.

### Configuration Manager

Create an application from the MSI (Windows Installer type), then set its installation
program to:

```
msiexec /i "LogLion-x64.msi" /qn REMOVE=Ext_splunk
```

Keep the detection method it creates from the product code. Each release has a new product
code, so add a new application for it that supersedes the previous one.

### A script

For Group Policy startup scripts, PDQ Deploy, or any tool that runs a command as an
administrator:

```bat
msiexec /i "\\server\share\LogLion-x64.msi" /qn REMOVE=Ext_grafana_mcp /l*v "%TEMP%\LogLion-install.log"
set rc=%errorlevel%
if %rc% equ 3010 set rc=0
if %rc% equ 0 copy /y "\\server\share\LogLion.Lic" "%ProgramFiles%\Log Lion\"
```

Exit code 0 is success and 3010 is success with a restart pending. Anything else is a
failure, explained in the log.

### Uninstalling

**Apps ▸ Installed apps**, or:

```
msiexec /x LogLion-x64.msi /qn
```

Your users' filters, profiles and settings in `%APPDATA%\Log Lion` are left alone.

## Adding extensions later

An administrator can add an extension for every user of a machine by copying its folder
into `%ProgramData%\Log Lion\Extensions`. The installer creates that folder so that only
administrators can write to it, and Log Lion refuses to load from it if ordinary users can.
Users can still install extensions for themselves from **Tools ▸ Extensions ▸ Install**. See
[Extensions](extensions.md).

---

[All documentation](README.md)
