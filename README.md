--Powershell ch.sh--
  ##Basic psh##
  Search Intire file system for file
    Get-ChildItem -Path C:\ -Filter "filename.txt" -Recurse -ErrorAction SilentlyContinue
    
  Search intire file system for hidden ads
    Get-ChildItem -Path C:\ -Recurse -File -ErrorAction SilentlyContinue | Get-Item -Stream * | Where-Object { $_.Stream -ne ':$DATA' }

  
  
  Getting Methods 
    Get-Process | Get-Member | Where-Object {$_.Membertype -match "Method"}
    # Displays all objects with Method in their name from the results from
    Get-Member of the Get-Process cmdlet

  To determine whether individual profiles have been created on the local computer: 
    Test-Path -Path $profile.currentUsercurrentHost
    Test-Path -Path $profile.currentUserAllHosts
    Test-Path -Path $profile.AllUsersAllHosts
    Test-Path -Path $profile.AllUserscurrentHost

  Get-Cimclass * # Lists all CIM Classes 
    Get-CimInstance –Namespace root\\securitycenter2 –ClassName antispywareproduct #Lists the antispywareproduct class from the root/security instance 
    Get-CimInstance -ClassName Win32_LogicalDisk -Filter “DriveType=3” | gm # Shows properties and methods for this Instance 
    Get-WmiObject -Class Win32_LogicalDisk -Filter “DriveType=3” # Using the Windows Management Instrumentation method
  Get-CPU
    Get-CimInstance -ClassName Win32_Processor

  ##Unzip file 1000x##
    # Set the initial file name
$zipFile = "flag.zip"
$extractPath = "./extracted"

for ($i = 1; $i -le 1000; $i++) {
    # Extract the current zip file
    Expand-Archive -Path $zipFile -DestinationPath $extractPath -Force
    
    # Find the next zip file inside the extraction folder
    $nextZip = Get-ChildItem -Path $extractPath -Filter "*.zip" | Select-Object -First 1
    
    if ($null -eq $nextZip) {
        Write-Host "No more zip files found at layer $i."
        break
    }

    # Update $zipFile to the newly extracted file for the next iteration
    $zipFile = $nextZip.FullName
    
    # Optional: Log progress
    if ($i % 100 -eq 0) {
        Write-Host "Layer $i completed..."
    }
}

Write-Host "Done! Check the '$extractPath' folder for your flag."

      
  ##Process validation
    
    List Process
      Get-Process | Select-Object Name, ID, path | Where-object {$_.ID -lt 1000} # List all the processes with a PID lower than 1000
      (Get-Process | Select-Object Name, ID, path | Where-object {$_.ID -lt 1000}).count # Count processes with a PID lower than 1000
    
    Start or Stop a Process
      Start-Process calc # Open an instance of calculator
      (Get-Process calculator *).kill() # Stops a named process using the kill() method directly
      Stop-Process -name calculator* # Uses a cmdlet to call the Process.Kill method

    
  ##Finding network connections##
    Get-ChildItem "HKLM:\SOFTWARE\Microsoft\Windows NT\CurrentVersion\NetworkList\Profiles" | 
      ForEach-Object {Get-ItemProperty $_.PSPath} | 
      Select-Object ProfileName, Description, DateCreated, DateLastConnected

        $ProfilePath = "HKLM:\SOFTWARE\Microsoft\Windows NT\CurrentVersion\NetworkList\Profiles"       
        Get-ChildItem $ProfilePath | ForEach-Object {
            $item = Get-ItemProperty $_.PSPath
            [PSCustomObject]@{
                SSID           = $item.ProfileName
                Category       = if($item.Category -eq 1) {"Private"} else {"Public"}
                DateCreated    = $_.Name # This unique ID links to the 'Signatures' subkey
                RegistryPath   = $_.PSPath
            }
        } | Format-Table -AutoSize

  ##Finding Files and Searching Content##
    Get-ChildItem -Path C:\ -Recurse -Include *flag*, *secret*, *cred* -ErrorAction SilentlyContinue
    Searching for a flag: Get-ChildItem -Recurse | Select-String "CTF{"
    
    ##History command##
      -Get-Content (Get-PSReadlineOption).HistorySavePath 
      -Get-Content $env:APPDATA\Microsoft\Windows\PowerShell\PSReadLine\ConsoleHost_history.txt
      
    ##Account Reletad##
      -Get-LocalUser: Lists all local user accounts.
        Find specific details: Get-LocalUser -Name "guest" | Select-Object * (Check for interesting descriptions or last logon times).
        Filter for active accounts: Get-LocalUser | Where-Object Enabled -eq True
        
      -Get-LocalGroupMember: Shows who is in a specific group.
        Critical check: Get-LocalGroupMember -Group "Administrators"
        
      -New-LocalUser / Add-LocalGroupMember: Used for persistence if you have admin rights.
          New-LocalUser -Name "Hacker" -NoPassword.
          Add-LocalGroupMember -Group "Administrators" -Member "Hacker"                      ----Persistance

      ##Active Directory (AD) Account Enumeration##
        Get a list of AD Commands Available
          -Get-Command -Module activedirectory
        Get ADPolicy
          Get-ADDefaultDomainPasswordPolicy
          
        Check for any Fine-Grained Password Policies
          -Get-ADFineGrainedPasswordPolicy -Filter {name -like "*"}
        
        Get Forest details
          -Get-ADForest

        Get Domain details:
          -Get-ADDomain

        Get AD Groups
          -Get-ADGroup -Filter *

        Get a groups details
          -Get-ADGroup -Identity ''

        Get a list of a groups members
          -Get-ADGroupMember -Identity '*' -Recursive
          
        Get AD users
          -Get-ADUser -Filter 'Name -like "*"'

        To see additional properties, not just the default set
          -Get-ADUser -Identity 'Nina.Webster' -Properties Description
          Get Name Property from the Active Directory Group named 'Domain Admins'
          
        Get Name Property from the Active Directory Group named 'Domain Admins'
         (Get-AdGroupMember -Identity 'domain admins').Name
          Get-AdGroupMember -Identity 'domain admins' | select name
      
        Get Active Directory Group 'System' Admin Names 'LvL 1'
          (Get-AdGroupMember -Identity "System Admins LV1").Name
        
        Get Active Directory Group 'System Admin' Names
         (Get-AdGroupMember -Identity "System Admins").Name
         
        Get Active Directory Group 'System' Admin Names 'LVL 2'
          (Get-AdGroupMember -Identity "System Admins LV2").Name


        Find Disabled users
          -get-aduser -filter {Enabled -eq "FALSE"} -properties name, enabled

        Enable that user
          -Enable-ADAccount -Identity guest

        Change the password
          -Set-AdAccountPassword -Identity guest -NewPassword (ConvertTo-SecureString -AsPlaintext -String "PassWord12345!!" -Force)
          
        Add the user to an Admin Group
          -Add-ADGroupMember -Identity "Domain Admins" -Members guest

        Get Distinguished Name to match AD format
          -Get-ADuser -filter * | select distinguishedname, name

        Create a new user
          -New-ADUser -Name "Bad.Guy" -AccountPassword (ConvertTo-SecureString -AsPlaintext -String "PassWord12345!!" -Force) -path "OU=3RD PLT,OU=CCO,OU=3RDBN,OU=WARRIORS,DC=army,DC=warriors"

          Enable the user
            -Enable-ADAccount -Identity "Bad.Guy"

        Remove User
          -Remove-ADUser -Identity "Bad.Guy"

        Remove From Group
          -Remove-ADGroupMember -Identity "Domain Admins" -Members guest

        Get All Domain Admin Accounts
          Get-AdGroupMember -identity "Domain Admins" -Recursive | %{Get-ADUser -identity $_.DistinguishedName}   
          Get-AdGroupMember -identity "Domain Admins" -Recursive | %{Get-ADUser -identity $_.DistinguishedName} | select name, Enabled  

        Get ALL Enterprise Admin accounts
          -Get-AdGroupMember -identity "Enterprise Admins" -Recursive | %{Get-ADUser -identity $_.DistinguishedName} | select name, Enabled

        -Get-ADUser: Fetches domain user objects.
          Search for secrets: Get-ADUser -Filter * -Properties Description | Select-Object Name, Description (Often admins hide passwords in descriptions)
          List all Domain Users: Get-ADUser -Filter * | Select-Object SamAccountName, Description
          Identify Domain Admins: Get-ADGroupMember -Identity "Domain Admins"

        Track Administrative Group Changes: Use Get-WinEvent to find Event ID 4728 (Member added to security-enabled global group) or 4732 (Local group).
          -Get-WinEvent -FilterHashtable @{LogName='Security'; Id=4728,4732} | Select-Object TimeCreated, Message
          
        Search for Privileged Groups: Use the admincount attribute to find groups that have been granted elevated administrative rights.
          -Get-ADGroup -Filter "admincount -eq 1" | Select-Object Name

        Find Recently Modified Users: Detect if an attacker has modified an account for persistence.
          -Get-ADUser -Filter * -Properties WhenChanged | Where-Object { $_.WhenChanged -gt (Get-Date).AddDays(-1) }

        Inspect PowerShell Profiles: Check for unauthorized scripts that run every time PowerShell starts.
          -Test-Path $PROFILE.AllUsersAllHosts, 
          -Test-Path $PROFILE.CurrentUserAllHosts

        Search for Encoded Commands: Look through security logs for the -EncodedCommand flag, often used to hide malicious payloads.
          Get-WinEvent -FilterHashtable @{LogName='Security'; Id=4688} | Where-Object { $_.Message -match "-enc" -or $_.Message -match "-EncodedCommand" }

        Identify Suspicious Process Spawning: Monitor for processes like lsass.exe being dumped (indicates credential harvesting).
          Look for commands like procdump.exe -ma lsass.exe in process creation logs (Event ID 4688)
                    # Query Security logs for Event ID 4688 (Process Creation)
                    $Keywords = @("lsass", "minidump", "procdump", "mimikatz", "sekurlsa")
                    Get-WinEvent -FilterHashtable @{LogName='Security'; Id=4688} | Where-Object {
                      $_.Message -match ($Keywords -join "|")
                      } | Select-Object TimeCreated, @{n='CommandLine';e={$_.Properties[8].Value}}

                        # Query Sysmon for attempts to access LSASS memory
                        Get-WinEvent -FilterHashtable @{LogName='Microsoft-Windows-Sysmon/Operational'; Id=10} | Where-Object {
                            $_.Message -match "lsass.exe" -and $_.Message -match "PROCESS_VM_READ"
                        } | Select-Object TimeCreated, Message
        
        
        Find Accounts with Non-Expiring Passwords: These are prime targets for long-term compromise.
          -Get-ADUser -Filter 'PasswordNeverExpires -eq $true' -Properties PasswordNeverExpires

        Audit Trust Relationships: Ensure no unauthorized domain trusts have been established that could allow lateral movement from a compromised forest.
          -Get-ADTrust -Filter *
        
        Kerberoasting (Finding SPNs): Search for users with a ServicePrincipalName. These accounts can be targeted to crack their passwords offline.
          Get-ADUser -Filter {ServicePrincipalName -ne "$null"} -Properties ServicePrincipalName  ----Priv Esc
          
        AS-REP Roasting: Find users that do not require Kerberos pre-authentication, allowing you to request a ticket and crack it.
          Get-ADUser -Filter 'DoesNotRequirePreAuth -eq $True' -Properties DoesNotRequirePreAuth  ----Priv Esc

        Unconstrained Delegation: Identify computers or users trusted for delegation, which can be abused to impersonate any user who authenticates to them.
          Get-ADComputer -Filter {TrustedForDelegation -eq $True}

  ##Display Resultant Set of Policy(RSoP) Information##
    Display Help
      -gpresult /?

    Output the computer and user node settings of a user
      -gpresult /user Webster /v
      -gpresult /user Administrator /v
      
    Displays data about the machine and logged on user
      gpresult /r
      
    Displays data about the machine and logged on user
      gpresult /r
      
    Force any group policy setting to take affect immediately versus rebooting the computer
      gpupdate /force

  ##SMB share##
      # Lists all shares, including the path and description
      Get-SmbShare
      
      List shares on a REMOTE computer to check for anomalies
        Get-SmbShare -CimSession "RemotePCName"
      
      Filter for a specific suspicious user
        Get-SmbSession | Where-Object {$_.UserName -like "*TargetUser*"}
        
      Shows every file currently opened via SMB
        Get-SmbOpenFile
        
      Force close a specific open file (useful if a process is locked by an attacker)
        Close-SmbOpenFile -FileId <IDNumber>

    Lists who has permission to the share itself (not the folder)
      Get-SmbShareAccess -Name "FinanceData"
--Linux ch.sh--
  
  ##Basic Recon##
    find any file that matches name pattern
      find / -name * -exec ls -lisa {} + 2>/dev/null
    
    sudo -l
      list commands you are allowed to run with/without sudo
      shows your /etc/sudoers.d/<Yourusername>

    sudo -l -U <Checks users perms>
    
    command shows what groups ur in..
      groups

    Shows uid,gid, and groups for a user
      id
      id <USERNAME>

    Recon for strange ports open:
      sudo ss -tulpin
      suod lsof -i -P -n
    
    Get Default target on sysmd machines
      - systemctl get-default
      
    recon for priv esc  
      find / -perm -4000 -type f 2>/dev/null ----POC/Priv esc
    
    
      who: Shows who is logged in right now and from where.

      last: Shows a history of recent logins. This is vital for finding who was on the system during an incident.
      
      id <username>: Quickly tells you the UID and group memberships for a specific name.
      
  ##Logs and Auditing##
  Check for kernal messages from previous boots:
    journalctl -k
    journalctl -k -b -1
      
  List all available boots:
    journalctl --list-boots

  View kernel messages from a specific date:
      journalctl -k --since "2026-04-26 23:00:00" --until "2026-04-27 01:00:00"

      
  ##MBR##
    Backup the MBR (first 512 bytes):
      - dd if=/dev/sda of=mbr_backup.bin bs=512 count=1

    Extract the Partition Table only:
      - dd if=/dev/sda of=partition_table.bin bs=1 skip=446 count=64
      
    Create a full disk image:
      dd if=/dev/sdb of=/path/to/evidence_disk.img bs=4k conv=noerror,sync
      
    Check if Secure  boot is enabled:
      dmesg | grep -i "secure boot"
      
  ##MD5 hacks##
    If need to hash a specific word
      echo -n "word" | md5sum
      
  ##Process Validation##
      Spot temporary process like orphans or zombies
        htop --> ps -elf
      
      See the user (UID) and the command for all running processes
        ps -eo user,pid,ppid,command ----if detect a suspionss process running
        
      List everything the process has open
        sudo lsof -p [PID]
        
      Check for Ancestry
        ps -fjp [PID]
      -p shows PIDs, -s shows parents of the specific PID, -u shows user transitions
        pstree -aps [PID]
        
      allows you to see every interaction between the process and the Linux kernel.
      sudo strace -p [PID] -s 80
      
      Check for sus port/process
        sudo lsof -i -P -n
      
      /proc investagation
        cat /proc/<PID>/cmdline
        ls /proc/<PID>/exe
        string /proc/<PID>/environ

      ##Xpather##
      Greping for Ip addr
        xmllint --xpath "//address[@addrtype='ipv4']/@addr" newfile.xml
        
      Greping for Ip and port
        xmllint --xpath "//address[@addrtype='ipv4']/@addr | //port/@portid" newfile.xml


      
        
      Summary Checklist for a Suspicious Process:
        Where is it? (ls -l /proc/[PID]/exe)
        
        Who started it? (ps -o user,ppid,start -p [PID])
        
        What is it saying? (sudo lsof -i -p [PID])
        
        What is it doing right now? (sudo strace -p [PID])

  
            
