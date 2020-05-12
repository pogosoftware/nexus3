# Nexus 3

This repository shows how to setup nexus 3 on the local environemnt using vagrant and virtualbox

## Requirements
* VirtualBox
* Vagrant

## Creating vm
To create vm execute below command
```bash
vagrant up
```

Vagrant will create certificates in `certs` directory. The nexus will be available on `https://192.168.192.100` or `https://nexus.internal` if you have configured hosts file.

## Update hosts file
Certificate is configured with domain `nexus.internal` so you need to add `192.168.192.100 nexus.internal` to your hosts file.

### Windows
Open notepad with administrator priviliges and open the file `C:\Windows\System32\drivers\etc\hosts`, paste above record and save the file.

### Linux
```bash
echo '192.168.192.100 nexus.internal` >> /etc/hosts
```

## Import Certificate

### Windows
```pwsh
certutil -importpfx -f -user ./certs/nexus.client.internal.pfx
```

### Linux
```bash
cp ./ca.pem /usr/local/share/ca-certificates/nexus-ca.crt
chmod 0644 /usr/local/share/ca-certificates/nexus-ca.crt
update-ca-certificates
```