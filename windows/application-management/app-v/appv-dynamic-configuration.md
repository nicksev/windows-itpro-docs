---
title: About App-V Dynamic Configuration (Windows 10)
description: About App-V Dynamic Configuration
author: MaggiePucciEvans
ms.pagetype: mdop, appcompat, virtualization
ms.mktglfcycl: deploy
ms.sitesec: library
ms.prod: w10
ms.date: 04/19/2017
---


# About App-V Dynamic Configuration

**Applies to**
-   Windows 10, version 1607

You can use the dynamic configuration to customize an App-V package for a user. Use the following information to create or edit an existing dynamic configuration file.

When you edit the dynamic configuration file it customizes how an App-V package will run for a user or group. This helps to provide a more convenient method for package customization by removing the need to re-sequence packages using the desired settings, and provides a way to keep package content and custom settings independent.

## Advanced: Dynamic Configuration


Virtual application packages contain a manifest that provides all the core information for the package. This information includes the defaults for the package settings and determines settings in the most basic form (with no additional customization). If you want to adjust these defaults for a particular user or group, you can create and edit the following files:

-   User Configuration file

-   Deployment configuration file

The previous .xml files specify package settings and allow for packages to be customized without directly affecting the packages. When a package is created, the sequencer automatically generates default deployment and user configuration .xml files using the package manifest data. Therefore, these automatically generated configuration files simply reflect the default settings that the package innately as from how things were configured during sequencing. If you apply these configuration files to a package in the form generated by the sequencer, the packages will have the same default settings that came from their manifest. This provides you with a package-specific template to get started if any of the defaults must be changed.

**Note**  
The following information can only be used to modify sequencer generated configuration files to customize packages to meet specific user or group requirements.

 

### Dynamic Configuration file contents

All of the additions, deletions, and updates in the configuration files need to be made in relation to the default values specified by the package's manifest information. Review the following table:

<table>
<colgroup>
<col width="100%" />
</colgroup>
<tbody>
<tr class="odd">
<td align="left"><p>User Configuration .xml file</p></td>
</tr>
<tr class="even">
<td align="left"><p>Deployment Configuration .xml file</p></td>
</tr>
<tr class="odd">
<td align="left"><p>Package Manifest</p></td>
</tr>
</tbody>
</table>

 

The previous table represents how the files will be read. The first entry represents what will be read last, therefore, its content takes precedence. Therefore, all packages inherently contain and provide default settings from the package manifest. If a deployment configuration .xml file with customized settings is applied, it will override the package manifest defaults. If a user configuration .xml file with customized settings is applied prior to that, it will override both the deployment configuration and the package manifest defaults.

The following list displays more information about the two file types:

-   **User Configuration File (UserConfig)** – Allows you to specify or modify custom settings for a package. These settings will be applied for a specific user when the package is deployed to a computer running the App-V client.

-   **Deployment Configuration File (DeploymentConfig)** – Allows you to specify or modify the default settings for a package. These settings will be applied for all users when a package is deployed to a computer running the App-V client.

To customize the settings for a package for a specific set of users on a computer or to make changes that will be applied to local user locations such as HKCU, the UserConfig file should be used. To modify the default settings of a package for all users on a machine or to make changes that will be applied to global locations such as HKEY\_LOCAL\_MACHINE and the all users folder, the DeploymentConfig file should be used.

The UserConfig file provides configuration settings that can be applied to a single user without affecting any other users on a client:

-   Extensions that will be integrated into the native system per user:- shortcuts, File-Type associations, URL Protocols, AppPaths, Software Clients and COM

-   Virtual Subsystems:- Application Objects, Environment variables, Registry modifications, Services and Fonts

-   Scripts (User context only)

The DeploymentConfig file provides configuration settings in two sections, one relative to the machine context and one relative to the user context providing the same capabilities listed in the UserConfig list above:

-   All UserConfig settings above

-   Extensions that can only be applied globally for all users

-   Virtual Subsystems that can be configured for global machine locations e.g. registry

-   Product Source URL

-   Scripts (Machine context only)

-   Controls to Terminate Child Processes

### File structure

The structure of the App-V Dynamic Configuration file is explained in the following section.

### Dynamic User Configuration file

**Header** - the header of a dynamic user configuration file is as follows:

```
<?xml version="1.0" encoding="utf-8"?>
<UserConfiguration PackageId="1f8488bf-2257-46b4-b27f-09c9dbaae707" DisplayName="Reserved" xmlns="http://schemas.microsoft.com/appv/2010/userconfiguration">
```

The **PackageId** is the same value as exists in the Manifest file.

**Body** - the body of the Dynamic User Configuration file can include all the app extension points that are defined in the Manifest file, as well as information to configure virtual applications. There are four subsections allowed in the body:

**Applications** - All app-extensions that are contained in the Manifest file within a package are assigned with an Application ID, which is also defined in the manifest file. This allows you to enable or disable all the extensions for a given application within a package. The **Application ID** must exist in the Manifest file or it will be ignored.

```
    <UserConfiguration PackageId="1f8488bf-2257-46b4-b27f-09c9dbaae707" DisplayName="Reserved" xmlns="http://schemas.microsoft.com/appv/2010/userconfiguration">
    <Applications>
    <!-- No new application can be defined in policy. AppV Client will ignore any application ID that is not also in the Manifest file -->
    <Application Id="{a56fa627-c35f-4a01-9e79-7d36aed8225a}" Enabled="false">
    </Application>
    </Applications>
    …
    </UserConfiguration>
```

**Subsystems** - AppExtensions and other subsystems are arranged as subnodes under the <Subsystems>:

```
    <UserConfiguration **PackageId**="1f8488bf-2257-46b4-b27f-09c9dbaae707" DisplayName="Reserved" xmlns="http://schemas.microsoft.com/appv/2010/userconfiguration">
    <Subsystems>
    ..
    </Subsystems>
    ..
    </UserConfiguration>
```

Each subsystem can be enabled/disabled using the “**Enabled**” attribute. Below are the various subsystems and usage samples.

**Extensions:**

Some subsystems (Extension Subsystems) control Extensions. Those subsystems are:- shortcuts, File-Type associations, URL Protocols, AppPaths, Software Clients and COM

Extension Subsystems can be enabled and disabled independently of the content.  Thus if Shortcuts are enabled, The client will use the shortcuts contained within the manifest by default. Each Extension Subsystem can contain an <Extensions> node. If this child element is present, the client will ignore the content in the Manifest file for that subsystem and only use the content in the configuration file.

Example using the shortcuts subsystem:

**Example 1**<br>If the user defined this in either the dynamic or deployment config file:

```
                                     <Shortcuts  Enabled="true">
                                                 <Extensions
                                                  ...
                                                 </Extensions>
                                     </Shortcuts>
```

Content in the manifest will be ignored.   

**Example 2**<br>If the user defined only the following:

                                    `<Shortcuts  Enabled="true"/>`
    
Then the content in the Manifest will be integrated during publishing.

**Example 3**<br>If the user defines the following

```
                           <Shortcuts  Enabled="true">
                                                 <Extensions/>
                                     </Shortcuts>
```

Then all the shortcuts within the manifest will still be ignored. There will be no shortcuts integrated.

The supported Extension Subsystems are:

**Shortcuts:** This controls shortcuts that will be integrated into the local system. Below is a sample with 2 shortcuts:

```
    <Subsystems>
    <Shortcuts Enabled="true">
      <Extensions>
        <Extension Category="AppV.Shortcut">
          <Shortcut>
            <File>\[{Common Programs}\]\\Microsoft Contoso\\Microsoft ContosoApp Filler 2010.lnk</File>
            <Target>\[{PackageRoot}\]\\Contoso\\ContosoApp.EXE</Target>
            <Icon>\[{Windows}\]\\Installer\\{90140000-0011-0000-0000-0000000FF1CE}\\inficon.exe</Icon>
            <Arguments />
            <WorkingDirectory />
            <AppUserModelId>ContosoApp.Filler.3</AppUserModelId>
            <Description>Fill out dynamic forms to gather and reuse information throughout the organization using Microsoft ContosoApp.</Description>
            <Hotkey>0</Hotkey>
            <ShowCommand>1</ShowCommand>
            <ApplicationId>\[{PackageRoot}\]\\Contoso\\ContosoApp.EXE</ApplicationId>
          </Shortcut>
      </Extension>
      <Extension Category="AppV.Shortcut">
        <Shortcut>
          <File>\[{AppData}\]\\Microsoft\\Contoso\\Recent\\Templates.LNK</File>
          <Target>\[{AppData}\]\\Microsoft\\Templates</Target>
          <Icon />
          <Arguments />
          <WorkingDirectory />
          <AppUserModelId />
          <Description />
          <Hotkey>0</Hotkey>
          <ShowCommand>1</ShowCommand>
          <!-- Note the ApplicationId is optional -->
        </Shortcut>
      </Extension>
     </Extensions>
    </Shortcuts>
```

**File-Type Associations:** Associates File-types with programs to open by default as well as setup the context menu. (MIME types can also be setup using this susbsystem). Sample File-type Association is below:

```
    <FileTypeAssociations Enabled="true">
    <Extensions>
      <Extension Category="AppV.FileTypeAssociation">
        <FileTypeAssociation>
          <FileExtension MimeAssociation="true">
          <Name>.docm</Name>
          <ProgId>contosowordpad.DocumentMacroEnabled.12</ProgId>
          <PerceivedType>document</PerceivedType>
          <ContentType>application/vnd.ms-contosowordpad.document.macroEnabled.12</ContentType>
          <OpenWithList>
            <ApplicationName>wincontosowordpad.exe</ApplicationName>
          </OpenWithList>
         <OpenWithProgIds>
            <ProgId>contosowordpad.8</ProgId>
          </OpenWithProgIds>
          <ShellNew>
            <Command />
            <DataBinary />
            <DataText />
            <FileName />
            <NullFile>true</NullFile>
            <ItemName />
            <IconPath />
            <MenuText />
            <Handler />
          </ShellNew>
        </FileExtension>
        <ProgId>
           <Name>contosowordpad.DocumentMacroEnabled.12</Name>
            <DefaultIcon>\[{Windows}\]\\Installer\\{90140000-0011-0000-0000-0000000FF1CE}\\contosowordpadicon.exe,15</DefaultIcon>
            <Description>Blah Blah Blah</Description>
            <FriendlyTypeName>\[{FOLDERID\_ProgramFilesX86}\]\\Microsoft Contoso 14\\res.dll,9182</FriendlyTypeName>
            <InfoTip>\[{FOLDERID\_ProgramFilesX86}\]\\Microsoft Contoso 14\\res.dll,1424</InfoTip>
            <EditFlags>0</EditFlags>
            <ShellCommands>
              <DefaultCommand>Open</DefaultCommand>
              <ShellCommand>
                 <ApplicationId>{e56fa627-c35f-4a01-9e79-7d36aed8225a}</ApplicationId>
                 <Name>Edit</Name>
                 <FriendlyName>&Edit</FriendlyName>
                 <CommandLine>"\[{PackageRoot}\]\\Contoso\\WINcontosowordpad.EXE" /vu "%1"</CommandLine>
              </ShellCommand>
              </ShellCommand>
                <ApplicationId>{e56fa627-c35f-4a01-9e79-7d36aed8225a}</ApplicationId>
                <Name>Open</Name>
                <FriendlyName>&Open</FriendlyName>
                <CommandLine>"\[{PackageRoot}\]\\Contoso\\WINcontosowordpad.EXE" /n "%1"</CommandLine>
                <DropTargetClassId />
                <DdeExec>
                  <Application>mscontosowordpad</Application>
                  <Topic>ShellSystem</Topic>
                  <IfExec>\[SHELLNOOP\]</IfExec>
                  <DdeCommand>\[SetForeground\]\[ShellNewDatabase "%1"\]</DdeCommand>
                </DdeExec>
              </ShellCommand>
            </ShellCommands>
          </ProgId>
         </FileTypeAssociation>
       </Extension>
      </Extensions>
      </FileTypeAssociations>
```

**URL Protocols**: This controls the URL Protocols that are integrated into the local registry of the client machine e.g. “mailto:”.

```
    <URLProtocols Enabled="true">
    <Extensions>
    <Extension Category="AppV.URLProtocol">
    <URLProtocol>
      <Name>mailto</Name>
      <ApplicationURLProtocol>
      <DefaultIcon>\[{ProgramFilesX86}\]\\Microsoft Contoso\\Contoso\\contosomail.EXE,-9403</DefaultIcon>
      <EditFlags>2</EditFlags>
      <Description />
      <AppUserModelId />
      <FriendlyTypeName />
      <InfoTip />
    <SourceFilter />
      <ShellFolder />
      <WebNavigableCLSID />
      <ExplorerFlags>2</ExplorerFlags>
      <CLSID />
      <ShellCommands>
      <DefaultCommand>open</DefaultCommand>
      <ShellCommand>
      <ApplicationId>\[{ProgramFilesX86}\]\\Microsoft Contoso\\Contoso\\contosomail.EXE</ApplicationId>
      <Name>open</Name>
      <CommandLine>\[{ProgramFilesX86}\\Microsoft Contoso\\Contoso\\contosomail.EXE" -c OEP.Note /m "%1"</CommandLine>
      <DropTargetClassId />
      <FriendlyName />
      <Extended>0</Extended>
      <LegacyDisable>0</LegacyDisable>
      <SuppressionPolicy>2</SuppressionPolicy>
       <DdeExec>
      <NoActivateHandler />
      <Application>contosomail</Application>
      <Topic>ShellSystem</Topic>
      <IfExec>\[SHELLNOOP\]</IfExec>
      <DdeCommand>\[SetForeground\]\[ShellNewDatabase "%1"\]</DdeCommand>
      </DdeExec>
      </ShellCommand>
      </ShellCommands>
      </ApplicationURLProtocol>
      </URLProtocol>
      </Extension>
      </Extension>
      </URLProtocols>
```

**Software Clients**: Allows the app to register as an Email client, news reader, media player and makes the app visible in the Set Program Access and Computer Defaults UI. In most cases you should only need to enable and disable it. There is also a control to enable and disable the email client specifically if you want the other clients still enabled except for that client.

```
    <SoftwareClients Enabled="true">
      <ClientConfiguration EmailEnabled="false" />
    </SoftwareClients>
```

**AppPaths**: If an application for example contoso.exe is registered with an apppath name of “myapp”, it allows you type “myapp” under the run menu and it will open contoso.exe.

```
    <AppPaths Enabled="true">
    <Extensions>
    <Extension Category="AppV.AppPath">
    <AppPath>
      <ApplicationId>\[{ProgramFilesX86}\]\\Microsoft Contoso\\Contoso\\contosomail.EXE</ApplicationId>
      <Name>contosomail.exe</Name>
      <ApplicationPath>\[{ProgramFilesX86}\]\\Microsoft Contoso\\Contoso\\contosomail.EXE</ApplicationPath>
      <PATHEnvironmentVariablePrefix />
      <CanAcceptUrl>false</CanAcceptUrl>
      <SaveUrl />
    </AppPath>
    </Extension>
    </Extensions>
    </AppPaths>
```

**COM**: Allows an Application register Local COM servers. Mode can be Integration, Isolated or Off. When Isol.

`    <COM Mode="Isolated"/>`

**Other Settings**:

In addition to Extensions, other subsystems can be enabled/disabled and edited:

**Virtual Kernel Objects**:

`    <Objects Enabled="false" />`

**Virtual Registry**: Used if you want to set a registry in the Virtual Registry within HKCU

```
    <Registry Enabled="true">
    <Include>
    <Key Path="\\REGISTRY\\USER\\\[{AppVCurrentUserSID}\]\\Software\\ABC">
    <Value Type="REG\_SZ" Name="Bar" Data="NewValue" />
     </Key>
      <Key Path="\\REGISTRY\\USER\\\[{AppVCurrentUserSID}\]\\Software\\EmptyKey" />
     </Include>
    <Delete>
      </Registry>
```

**Virtual File System**

`          <FileSystem Enabled="true" />`

**Virtual Fonts**

`          <Fonts Enabled="false" />`

**Virtual Environment Variables**

```
    <EnvironmentVariables Enabled="true">
    <Include>
           <Variable Name="UserPath" Value="%path%;%UserProfile%" />
           <Variable Name="UserLib" Value="%UserProfile%\\ABC" />
           </Include>
          <Delete>
           <Variable Name="lib" />
            </Delete>
            </EnvironmentVariables>
```

**Virtual services**

`          <Services Enabled="false" />`

**UserScripts** – Scripts can be used to setup or alter the virtual environment as well as execute scripts at time of deployment or removal, before an application executes, or they can be used to “clean up” the environment after the application terminates. Please reference a sample User configuration file that is output by the sequencer to see a sample script. The Scripts section below provides more information on the various triggers that can be used.

### Dynamic Deployment Configuration file

**Header** - The header of a Deployment Configuration file is as follows:

```
<?xml version="1.0" encoding="utf-8"?><DeploymentConfiguration PackageId="1f8488bf-2257-46b4-b27f-09c9dbaae707" DisplayName="Reserved" xmlns="http://schemas.microsoft.com/appv/2010/deploymentconfiguration">
```

The **PackageId** is the same value as exists in the manifest file.

**Body** - The body of the deployment configuration file includes two sections:

-   User Configuration section –allows the same content as the User Configuration file described in the previous section. When the package is published to a user, any appextensions configuration settings in this section will override corresponding settings in the Manifest within the package unless a user configuration file is also provided. If a UserConfig file is also provided, it will be used instead of the User settings in the deployment configuration file. If the package is published globally, then only the contents of the deployment configuration file will be used in combination with the manifest.

-   Machine Configuration section–contains information that can be configured only for an entire machine, not for a specific user on the machine. For example, HKEY\_LOCAL\_MACHINE registry keys in the VFS.

```
<DeploymentConfiguration PackageId="1f8488bf-2257-46b4-b27f-09c9dbaae707" DisplayName="Reserved" xmlns="http://schemas.microsoft.com/appv/2010/deploymentconfiguration">
<UserConfiguration>
  ..
</UserConfiguration>
<MachineConfiguration>
..
</MachineConfiguration>
..
</MachineConfiguration>
</DeploymentConfiguration>
```

**User Configuration** - use the previous **Dynamic User Configuration file** section for information on settings that are provided in the user configuration section of the Deployment Configuration file.

Machine Configuration - the Machine configuration section of the Deployment Configuration File is used to configure information that can be set only for an entire machine, not for a specific user on the computer. For example, HKEY\_LOCAL\_MACHINE registry keys in the Virtual Registry. There are four subsections allowed in under this element

1.  **Subsystems** - AppExtensions and other subsystems are arranged as subnodes under <Subsystems>:

```
    <MachineConfiguration>
      <Subsystems>
      ..
      </Subsystems>
    ..
    </MachineConfiguration>
```

The following section displays the various subsystems and usage samples.

**Extensions**:

Some subsystems (Extension Subsystems) control Extensions which can only apply to all users. The subsystem is application capabilities. Because this can only apply to all users, the package must be published globally in order for this type of extension to be integrated into the local system. The same rules for controls and settings that apply to the Extensions in the User Configuration also apply to those in the MachineConfiguration section.

**Application Capabilities**: Used by default programs in windows operating system Interface. Allows an application to register itself as capable of opening certain file extensions, as a contender for the start menu internet browser slot, as capable of opening certain windows MIME types.  This extension also makes the virtual application visible in the Set Default Programs UI.:

```
    <ApplicationCapabilities Enabled="true">
      <Extensions>
       <Extension Category="AppV.ApplicationCapabilities">
        <ApplicationCapabilities>
         <ApplicationId>\[{PackageRoot}\]\\LitView\\LitViewBrowser.exe</ApplicationId>
         <Reference>
          <Name>LitView Browser</Name>
          <Path>SOFTWARE\\LitView\\Browser\\Capabilities</Path>
         </Reference>
       <CapabilityGroup>
        <Capabilities>
         <Name>@\[{ProgramFilesX86}\]\\LitView\\LitViewBrowser.exe,-12345</Name>
         <Description>@\[{ProgramFilesX86}\]\\LitView\\LitViewBrowser.exe,-12346</Description>
         <Hidden>0</Hidden>
         <EMailSoftwareClient>Lit View E-Mail Client</EMailSoftwareClient>
         <FileAssociationList>
          <FileAssociation Extension=".htm" ProgID="LitViewHTML" />
          <FileAssociation Extension=".html" ProgID="LitViewHTML" />
          <FileAssociation Extension=".shtml" ProgID="LitViewHTML" />
         </FileAssociationList>
         <MIMEAssociationList>
          <MIMEAssociation Type="audio/mp3" ProgID="LitViewHTML" />
          <MIMEAssociation Type="audio/mpeg" ProgID="LitViewHTML" />
         </MIMEAssociationList>
        <URLAssociationList>
          <URLAssociation Scheme="http" ProgID="LitViewHTML.URL.http" />
         </URLAssociationList>
         </Capabilities>
      </CapabilityGroup>
       </ApplicationCapabilities>
      </Extension>
    </Extensions>
    </ApplicationCapabilities>
```

**Other Settings**:

In addition to Extensions, other subsystems can be edited:

**Machine Wide Virtual Registry**: Used when you want to set a registry key in the virtual registry within HKEY\_Local\_Machine

```
    <Registry>
    <Include>
      <Key Path="\\REGISTRY\\Machine\\Software\\ABC">
        <Value Type="REG\_SZ" Name="Bar" Data="Baz" />
       </Key>
      <Key Path="\\REGISTRY\\Machine\\Software\\EmptyKey" />
     </Include>
    <Delete>
    </Registry>
```

**Machine Wide Virtual Kernel Objects**

```
    <Objects>
    <NotIsolate>
       <Object Name="testObject" />
     </NotIsolate>
    </Objects>
```

**ProductSourceURLOptOut**: Indicates whether the URL for the package can be modified globally through PackageSourceRoot (to support branch office scenarios). Default is false and the setting change takes effect on the next launch.

```
    <MachineConfiguration>
      .. 
      <ProductSourceURLOptOut Enabled="true" />
      ..
    </MachineConfiguration>
```

**MachineScripts** – Package can be configured to execute scripts at time of deployment, publishing or removal. Please reference a sample deployment configuration file that is generated by the sequencer to see a sample script. The Scripts section below provides more information on the various triggers that can be used

**TerminateChildProcess**:- An application executable can be specified, whose child processes will be terminated when the application exe process is terminated.

```
    <MachineConfiguration>
      ..   
      <TerminateChildProcesses>
        <Application Path="\[{PackageRoot}\]\\Contoso\\ContosoApp.EXE" />
        <Application Path="\[{PackageRoot}\]\\LitView\\LitViewBrowser.exe" />
        <Application Path="\[{ProgramFilesX86}\]\\Microsoft Contoso\\Contoso\\contosomail.EXE" />
      </TerminateChildProcesses>
      ..
    </MachineConfiguration>
```

### Scripts

The following table describes the various script events and the context under which they can be run.

<table style="width:100%;">
<colgroup>
<col width="16%" />
<col width="16%" />
<col width="16%" />
<col width="16%" />
<col width="16%" />
<col width="16%" />
</colgroup>
<thead>
<tr class="header">
<th align="left">Script Execution Time</th>
<th align="left">Can be specified in Deployment Configuration</th>
<th align="left">Can be specified in User Configuration</th>
<th align="left">Can run in the Virtual Environment of the package</th>
<th align="left">Can be run in the context of a specific application</th>
<th align="left">Runs in system/user context: (Deployment Configuration, User Configuration)</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td align="left"><p>AddPackage</p></td>
<td align="left"><p>X</p></td>
<td align="left"><p></p></td>
<td align="left"><p></p></td>
<td align="left"><p></p></td>
<td align="left"><p>(SYSTEM, N/A)</p></td>
</tr>
<tr class="even">
<td align="left"><p>PublishPackage</p></td>
<td align="left"><p>X</p></td>
<td align="left"><p>X</p></td>
<td align="left"><p></p></td>
<td align="left"><p></p></td>
<td align="left"><p>(SYSTEM, User)</p></td>
</tr>
<tr class="odd">
<td align="left"><p>UnpublishPackage</p></td>
<td align="left"><p>X</p></td>
<td align="left"><p>X</p></td>
<td align="left"><p></p></td>
<td align="left"><p></p></td>
<td align="left"><p>(SYSTEM, User)</p></td>
</tr>
<tr class="even">
<td align="left"><p>RemovePackage</p></td>
<td align="left"><p>X</p></td>
<td align="left"><p></p></td>
<td align="left"><p></p></td>
<td align="left"><p></p></td>
<td align="left"><p>(SYSTEM, N/A)</p></td>
</tr>
<tr class="odd">
<td align="left"><p>StartProcess</p></td>
<td align="left"><p>X</p></td>
<td align="left"><p>X</p></td>
<td align="left"><p>X</p></td>
<td align="left"><p>X</p></td>
<td align="left"><p>(User, User)</p></td>
</tr>
<tr class="even">
<td align="left"><p>ExitProcess</p></td>
<td align="left"><p>X</p></td>
<td align="left"><p>X</p></td>
<td align="left"><p></p></td>
<td align="left"><p>X</p></td>
<td align="left"><p>(User, User)</p></td>
</tr>
<tr class="odd">
<td align="left"><p>StartVirtualEnvironment</p></td>
<td align="left"><p>X</p></td>
<td align="left"><p>X</p></td>
<td align="left"><p>X</p></td>
<td align="left"><p></p></td>
<td align="left"><p>(User, User)</p></td>
</tr>
<tr class="even">
<td align="left"><p>TerminateVirtualEnvironment</p></td>
<td align="left"><p>X</p></td>
<td align="left"><p>X</p></td>
<td align="left"><p></p></td>
<td align="left"><p></p></td>
<td align="left"><p>(User, User)</p></td>
</tr>
</tbody>
</table>

 

### Using multiple scripts on a single event trigger

App-V supports the use of multiple scripts on a single event trigger for App-V packages, including packages that you convert from App-V 4.6 to App-V for Windows 10. To enable the use of multiple scripts, App-V uses a script launcher application, named ScriptRunner.exe, which is included in the App-V client.

**How to use multiple scripts on a single event trigger:**

For each script that you want to run, pass that script as an argument to the ScriptRunner.exe application. The application then runs each script separately, along with the arguments that you specify for each script. Use only one script (ScriptRunner.exe) per trigger.

**Note**  
We recommended that you run the multi-script line from a command prompt first to make sure that all arguments are built correctly before adding them to the deployment configuration file.

 

**Example script and parameter descriptions**

Using the following example file and table, modify the deployment or user configuration file to add the scripts that you want to run.

``` syntax
<MachineScripts>
 <AddPackage>
   <Path>ScriptRunner.exe</Path>
   <Arguments>
   -appvscript script1.exe arg1 arg2 –appvscriptrunnerparameters –wait –timeout=10
   -appvscript script2.vbs arg1 arg2
   -appvscript script3.bat arg1 arg2 –appvscriptrunnerparameters –wait –timeout=30 –rollbackonerror
   </Arguments>
   <Wait timeout=”40” RollbackOnError=”true”/>
 </AddPackage>
</MachineScripts>
```

<table>
<colgroup>
<col width="50%" />
<col width="50%" />
</colgroup>
<thead>
<tr class="header">
<th align="left">Parameter in the example file</th>
<th align="left">Description</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td align="left"><p><code><AddPackage></code></p></td>
<td align="left"><p>Name of the event trigger for which you are running a script, such as adding a package or publishing a package.</p></td>
</tr>
<tr class="even">
<td align="left"><p><code><Path>ScriptRunner.exe</Path></code></p></td>
<td align="left"><p>The script launcher application that is included in the App-V client.</p>
<div class="alert">
<strong>Note</strong>  
<p>Although ScriptRunner.exe is included in the App-V client, the location of the App-V client must be in %path% or ScriptRunner will not run. ScriptRunner.exe is typically located in the C:\Program Files\Microsoft Application Virtualization\Client folder.</p>
</div>
<div>
 
</div></td>
</tr>
<tr class="odd">
<td align="left"><pre class="syntax" space="preserve"><code><Arguments>
-appvscript script1.exe arg1 arg2 –appvscriptrunnerparameters –wait –timeout=10

-appvscript script2.vbs arg1 arg2

-appvscript script3.bat arg1 arg2 –appvscriptrunnerparameters –wait –timeout=30 -rollbackonerror
</Arguments></code></pre></td>
<td align="left"><p><code>-appvscript</code> - Token that represents the actual script that you want to run.</p>
<p><code>script1.exe</code> – Name of the script that you want to run.</p>
<p><code>arg1 arg2</code> – Arguments for the script that you want to run.</p>
<p><code>-appvscriptrunnerparameters</code> – Token that represents the execution options for <code>script1.exe</code></p>
<p><code>-wait</code> – Token that informs ScriptRunner to wait for execution of <code>script1.exe</code> to complete before proceeding to the next script.</p>
<p><code>-timeout=x</code> – Token that informs ScriptRunner to stop running the current script after <code>x</code> number of seconds. All other specified scripts will still run.</p>
<p><code>-rollbackonerror</code> – Token that informs ScriptRunner to stop running all scripts that haven't yet run and to roll back an error to the App-V client.</p></td>
</tr>
<tr class="even">
<td align="left"><p><code><Wait timeout=”40” RollbackOnError=”true”/></code></p></td>
<td align="left"><p>Waits for overall completion of ScriptRunner.exe.</p>
<p>Set the timeout value for the overall runner to be greater than or equal to the sum of the timeout values on the individual scripts.</p>
<p>If any individual script reported an error and rollbackonerror was set to <code>true</code>, then ScriptRunner would report the error to App-V client.</p></td>
</tr>
</tbody>
</table>

 

ScriptRunner will run any script whose file type is associated with an application installed on the computer. If the associated application is missing, or the script’s file type is not associated with any application on the computer, the script will not run.

### Create a Dynamic Configuration file using an App-V Manifest file

You can create the Dynamic Configuration file using one of three methods: either manually, using the App-V Management Console or sequencing a package, which will be generated with 2 sample files.

For more information about how to create the file using the App-V Management Console see, [How to Create a Custom Configuration File by Using the App-V Management Console](appv-create-a-custom-configuration-file-with-the-management-console.md).

To create the file manually, the information above in previous sections can be combined into a single file. We recommend you use files generated by the sequencer.

## Have a suggestion for App-V?

Add or vote on suggestions on the [Application Virtualization feedback site](https://appv.uservoice.com/forums/280448-microsoft-application-virtualization).<br>For App-V issues, use the [App-V TechNet Forum](https://social.technet.microsoft.com/Forums/en-US/home?forum=mdopappv).

## Related topics

[How to Apply the Deployment Configuration File by Using Windows PowerShell](appv-apply-the-deployment-configuration-file-with-powershell.md)

[How to Apply the User Configuration File by Using Windows PowerShell](appv-apply-the-user-configuration-file-with-powershell.md)

[Operations for App-V](appv-operations.md)
