```
         _____ _____ _   _  _____ _      ______    _____   ____   _____ ______
        / ____|_   _| \ | |/ ____| |    |  ____|  |  __ \ / __ \ / ____|  ____|
       | (___   | | |  \| | |  __| |    | |__     | |  | | |  | | (___ | |__
        \___ \  | | | . ` | | |_ | |    |  __|    | |  | | |  | |\___ \|  __|
        ____) |_| |_| |\  | |__| | |____| |____   | |__| | |__| |____) | |____
       |_____/|_____|_| \_|\_____|______|______|  |_____/ \____/|_____/|______|
        _______________________________________________________________________
       |_______________________________________________________________________|
                                                                 [GitHub: Wra7h]
```
Configure and create process injection binaries. 

## Basic Usage:  
Creating the first binary can be accomplished using the commands below. Just enter the commands below and fill in the brackets as you see fit.  
```
  +--> settings
  +-->> output [output directory]
  +-->> mode [mode #]
  +-->> exit
  +--> show techniques
  +--> build [technique name/number]
```
### "What's going on here?"    
When you first execute Single Dose, you are at the Main Menu. Entering "settings" takes you to the settings submenu where you can configure how Single Dose builds your binary. 
The next 2 commands are from this submenu. "Output" tells Single Dose where to store your .cs and .exe, and the "mode" command will tell Single Dose whether you plan on embedding injection data or providing data at the time of execution. The "Static" mode will embed all injection data into the binary, "Dynamic" and "Download" will require the user specify flags at execution. **Important: Download mode only supports downloads over http, and not https at this time.** Entering "exit" from the Settings submenu will take you back to Main Menu. "Output" and "Mode" are the only required settings in order to successfully build a technique. Lastly, "show techniques" will display all of the techniques supported, and "build" will start the build process for a binary.  

## Main Menu Commands:  
```
                 SUBMENUS
            ------------------
       Settings :: Enter submenu for configuring settings
       Triggers :: Enter submenu for configuring execution conditions.

                  BUILD
            ------------------
       Build :: Build a binary using the configured settings and triggers.
          > Example Usage: build <technique>
          > Example Usage: build 3

                TECHNIQUES
              --------------
          1) CreateRemoteThread: Inject a DLL into a remote process and execute with CreateRemoteThread. [DLL]
          2) SRDI: Convert DLL into shellcode and inject. [DLL]
          3) EarlyBird_QueueUserAPC: Inject Shellcode into a newly spawned process. [Shellcode]
          4) Suspend_QueueUserAPC: Inject Shellcode into a process currently running. [Shellcode]
          5) Syscall_CreateThread: Inject Shellcode using direct syscalls. [Shellcode]

              MISC. COMMANDS
            ------------------
       Show :: Display current configuration, or techniques.
          > Example Usage: show
          > Example Usage: show techniques
       Describe :: Describe techniques.
          > Example Usage: describe <technique>
       Clear :: Clear the terminal, settings or triggers.
          > Example Usage: clear
          > Example Usage: clear settings
       Exit :: Exit the program.
```  

## Settings Commands:  
Like I mentioned before in the basic usage, this is the submenu that allows you to configure how Single Dose builds your binary. Mode and Output were covered in the basic usage. Using the "Compile" command tells Single Dose to only generate the .cs file. This allows you to modify the binary prior to compiling yourself. You can compile the binary yourself from within command prompt using `C:\Windows\Microsoft.NET\Framework64\v3.5\csc.exe C:\path\to\output\<file>.cs`. The `-unsafe` may be necessary depending on the technique.  
```
                              Settings Commands:
                             --------------------
       Mode :: Select whether to embed injection content or provide injection content at execution.
          1) Static: Injection content will be embedded in binary.
          2) Dynamic: Injection content will be provided at execution with -PID and -DLL/-Bin.
          3) Download: Injection content will be provided at execution with -PID and -URI.

          > Example Usage: mode static
          > Example Usage: mode 2

       Output :: Set the output directory. The directory will be created if necessary.
          > Example Usage: output .\binaries
          > Example Usage: output C:\Users\user\Desktop\output

       Compile :: a switch to only generate the .cs file. If this command is executed while true,
                  the value becomes false and vice versa. (Default = true)
          > Example Usage: compile

       Show :: Display current configuration.

       Clear :: Clear the terminal, settings or triggers.
          > Example Usage: clear
          > Example Usage: clear settings

       Triggers :: Enter the triggers submenu
       Help :: Display settings help
       Exit :: Return to Main Menu  
```

## Triggers Commands:
The triggers tells the binary to only inject under certain conditions. Triggers are not required but need to be configured before building the binary if you choose to use them.  

Example Scenarios:
  1. "I want my binary to exit if notepad.exe is not currently running on the system."  
     - For this you would use the `avoid` trigger. Command: `avoid notepad.exe`  
     
  2. "My binary should _only_ execute if notepad.exe and pid 2048 are running on the system."  
     - This would be the `require` trigger. Command:
     ``` 
     require notepad.exe
     require 2048
     ```
     
  3. "I want my binary to pause execution, *but not exit* while notepad.exe is not running on the system."  
     - Check out the `hibernate` trigger. Hibernate will sleep until the condition is met. The sleep time is randomly generated.  
       The console will remain open during sleep, with status messages provided every time the binary checks the system for the conditions.  
       If you only want to inject when notepad is running, the command is: `hibernate notepad.exe`  
       
  4. "I want my binary to pause execution, *but not exit* while explorer.exe is running on the system."
     - This is the `persist` trigger. Persist will sleep while the condintion is met. The sleep time is randomly generated. Like, `hibernate`  
       the console will remain open during sleep, and status messages provided every time the binary checks the system for the conditions.  
       Command: `persist explorer.exe`. This means the binary will only inject, if explorer.exe is _not_ running on the system when the conditions
       are reevaluated.   
 
```
                                Triggers Commands:
                               --------------------
       1) Avoid :: Do not execute if condition is met. Binary will exit if condition is met.
                  Accepts PID, Module (DLL), or Process name (EXE).
                 'Avoid *' will enter an interface allowing you to add multiple conditions.
          > Example Usage: avoid *
          > Example Usage: avoid iexplore.exe

       2) Require :: Only execute if all conditions are met. Binary will exit if conditions are not met.
                  Accepts PID, Module (DLL), or Process name (EXE).
                 'Require *' will enter an interface allowing you to add multiple conditions.
          > Example Usage: require *
          > Example Usage: require notepad.exe

       3) Hibernate :: Similar to the require trigger, but the binary will sleep instead of exit while 
                    the condition is not met. The sleep value is a random value between 90 seconds and 
                    5 min. Accepts PID, Module (DLL), or Process name (EXE).
                   'Hibernate *' will enter an interface allowing you to add multiple conditions.
          > Example Usage: hibernate *
          > Example Usage: hibernate notepad.exe

       4) Persist :: Similar to the avoid trigger, but the binary will sleep instead of exit while the 
                     condition is met. The sleep value is a random value between 90 seconds and 5 min. 
                     Accepts PID, Module (DLL), or Process name (EXE).
                     
          > Example Usage: persist *
          > Example Usage: persist notepad.exe


       Show :: Display current configuration.

       Clear :: Clear the terminal, settings or triggers.
          > Example Usage: clear
          > Example Usage: clear settings

       Settings :: Enter the settings Menu
       Help :: Display help
       Exit :: Return to Main Menu
```  

## What's next?
This has been a project built in my spare time. I plan on adding more techniques as time goes on, but I won't have much time to dedicate for a few weeks. I suppose now is a good time to drop this here.  

## Check these references out:  
  - https://www.ired.team/offensive-security/code-injection-process-injection/dll-injection
  - https://github.com/monoxgas/sRDI
  - https://3xpl01tc0d3r.blogspot.com/2019/12/process-injection-part-v.html
  - https://sevrosecurity.com/2020/04/13/process-injection-part-2-queueuserapc/
  - https://jhalon.github.io/utilizing-syscalls-in-csharp-1/
  - https://jhalon.github.io/utilizing-syscalls-in-csharp-2/
  - https://www.solomonsklash.io/syscalls-for-shellcode-injection.html
