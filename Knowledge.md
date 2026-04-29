--Linux--
  ##Process##
    - init (/sbin/init) has a process ID of 1, and its parent, the kernel, has a PID of 0.
    - Modern Linux kernels and distros also have [kthreadd], which is a kernel thread daemon. It is second after init and has a PID of 2.
    - All kernel processes are fork()`ed from `[kthreadd]
    - All user processes are fork()`ed from `/sbin/init or a direct ancestor
    - Kernel processes can be identified by names enclosed in square brackets [ ]
    - 
    RUID - Real User ID; Who actualy started the process
    EUID -  Effective User ID; Whose "permissions" the process is currently using
    SID  -  Sessions ID or assigigned depsite of UID
      NOTE: Attackers obfucsate there actions by hopping between account SID's remain the same and allowes you to expose the intire attack surface --POC
    AUID -  
    UID  -

    Zombie - Are process that have stoped excuting but havent been reaped (They cannot be killed, they take pids, and use zero resources)
    Orphan - Orphan process are sub-process that had there parrent process reaped (Adopted by /bin/init with a PPID of 1, is killed)
    Daemon - Intentionaly Oprhaned process that douse not die until told so (PPID of 1 ex: ssh)
    
  ##Files##
    

  /sbin/init - 
  /lib/systemd/systemd - 
   /etc/crontab - 
    NOTE: look for scripts running as root
   /var/spool/cron/crontabs/ - 
    NOTE: look for scripts running as root
    /var/log/auth.log -
      NOTE: Check file for bruteforce attempts and failed logings --POC
    /var/log/secure -
      NOTE: Check file for bruteforce attempts and failed logings --POC
    /etc/login.defs - 
    /etc/passwd - username:password:UID:GID:comment:home_directory:shell 
    /etc/group - shows which users belongs to which group
    /etc/shadow -  password file
    /etc/sudoers or /etc/sudoers.d/ -  Shows Permissions for User accounts
      NOTE: If System Account have a NOPASSWD priv may be a sighn of compromise ----POC/Priv Esc
    
    
  ##User accounts##
    HUMAN
        - typicaly run /bin/bash or /bin/sh
    SYSTEM
        - typicaly run /bin/nologin or /bin/false
          note: System Users running a normal shell may be a sign of compromise ----POC


--Windows--
  Registry Locations for Persistence
  
    HKLM\Software\Microsoft\Windows\CurrentVersion\Run
    
    HKLM\Software\Microsoft\Windows\CurrentVersion\RunOnce
    
    HKU\<SID>\Software\Microsoft\Windows\CurrentVersion\Run
    
    HKU\<SID>\Software\Microsoft\Windows\CurrentVersion\RunOnce
    
    HKLM\SYSTEM\CurrentControlSet\services
    
    HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\Shell Folders
          
  Registry Locations for Persistence
  
    HKLM\Software\Microsoft\Windows\CurrentVersion\Run
    
    HKLM\Software\Microsoft\Windows\CurrentVersion\RunOnce
    
    HKU\<SID>\Software\Microsoft\Windows\CurrentVersion\Run
    
    HKU\<SID>\Software\Microsoft\Windows\CurrentVersion\RunOnce
    
    HKLM\SYSTEM\CurrentControlSet\services
    
    HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\Shell Folders

  
