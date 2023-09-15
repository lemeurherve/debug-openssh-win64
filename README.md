# Debug repo to add more logs for https://github.com/jenkinsci/docker-ssh-agent/issues/302

Content extracted from the archive used in the Nanoserver image of jenkinsci/docker-ssh-agent to install OpenSSH: https://github.com/PowerShell/Win32-OpenSSH/releases/download/v9.2.2.0p1-Beta/OpenSSH-Win64.zip

Note: tried the official install but it appears there is no Add-WindowsCapability on Nanoserver images, and while digging it I failed to install what's needed for Add-WindowsCapability...

# Error in nanoserver jenkinsci/docker-ssh-agent image build logs

```
Step 24/32 : RUN [Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12 ;     $url = 'https://github.com/PowerShell/Win32-OpenSSH/releases/download/{0}/OpenSSH-Win64.zip' -f $env:OPENSSH_VERSION ;     Write-Host "Retrieving $url..." ;     Invoke-WebRequest -Uri $url -OutFile C:/openssh.zip -UseBasicParsing ;     Expand-Archive c:/openssh.zip 'C:/Program Files' ;     Remove-Item C:/openssh.zip ;     $env:PATH = '{0};{1}' -f $env:PATH,'C:\Program Files\OpenSSH-Win64' ;     & 'C:/Program Files/OpenSSH-Win64/Install-SSHd.ps1' ;     if(!(Test-Path 'C:\ProgramData\ssh')) { New-Item -Type Directory 
-Path 'C:\ProgramData\ssh' | Out-Null } ;     Copy-Item 'C:\Program Files\OpenSSH-Win64\sshd_config_default' 'C:\ProgramData\ssh\sshd_config' ;     $content = Get-Content -Path "C:\ProgramData\ssh\sshd_config" ;     $content | ForEach-Object { $_ -replace '#PermitRootLogin.*','PermitRootLogin no'                         -replace '#PasswordAuthentication.*','PasswordAuthentication no'                         -replace '#PermitEmptyPasswords.*','PermitEmptyPasswords no'                         -replace '#PubkeyAuthentication.*','PubkeyAuthentication yes'                         -replace '#SyslogFacility.*','SyslogFacility LOCAL0'                         -replace '#LogLevel.*','LogLevel INFO'                         -replace 'Match Group administrators',''                         -replace '(\s*)AuthorizedKeysFile __PROGRAMDATA__/ssh/administrators_authorized_keys',''                 } |     Set-Content -Path "C:\ProgramData\ssh\sshd_config" ;     Add-Content -Path "C:\ProgramData\ssh\sshd_config" -Value 'ChallengeResponseAuthentication no' ;     Add-Content -Path "C:\ProgramData\ssh\sshd_config" -Value 'HostKeyAgent \\.\pipe\openssh-ssh-agent' ;     Add-Content -Path "C:\ProgramData\ssh\sshd_config" -Value ('Match User {0}' -f $env:JENKINS_AGENT_USER) ;     Add-Content -Path "C:\ProgramData\ssh\sshd_config" -Value ('       AuthorizedKeysFile C:/Users/{0}/.ssh/authorized_keys' -f $env:JENKINS_AGENT_USER) ;     New-Item -Path HKLM:\SOFTWARE -Name OpenSSH -Force | Out-Null ;     New-ItemProperty -Path HKLM:\SOFTWARE\OpenSSH -Name DefaultShell -Value 'C:\Program Files\Powershell\pwsh.exe' -PropertyType string -Force | Out-Null
 ---> Running in 4e7902f9dca7
Retrieving https://github.com/PowerShell/Win32-OpenSSH/releases/download/V8.6.0.0p1-Beta/OpenSSH-Win64.zip ...
C:\"C:\Program Files\OpenSSH-Win64\openssh-events.man"(0): Error 0x8007007b: At column=0, The filename, directory name, or volume label syntax is incorrect.

Failed to load XML document C:\"C:\Program Files\OpenSSH-Win64\openssh-events.man".
The filename, directory name, or volume label syntax is incorrect.
  [*] C:\Program Files\OpenSSH-Win64\moduli
Inheritance is removed from 'C:\Program Files\OpenSSH-Win64\moduli'.
'BUILTIN\Users' now has Read access to 'C:\Program Files\OpenSSH-Win64\moduli'. 
      Repaired permissions

C:\"C:\Program Files\OpenSSH-Win64\openssh-events.man"(0): Error 0x8007007b: At column=0, The filename, directory name, or volume label syntax is incorrect.

Failed to load XML document C:\"C:\Program Files\OpenSSH-Win64\openssh-events.man".
The filename, directory name, or volume label syntax is incorrect.

```
