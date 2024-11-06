### Repositório para o curso Manipulando Arquivos no Linux do bootcamp Linux Experience da DIO

# Manipulando Arquivos no Linux

## Navegando pelo sitema de arquivos

- /$ cd /var ou cd var
- /home$ cd ..
- /home$ cd /var/etc

## Filtando exibição de arquivos
- /etc$ ls s*
- /etc$ ls s?h*
- /etc$ ls s*
- /alisson$ ls arquivo[1-2]*

## Localizando arquivos
- /$ find -name ar?u*

## Criando diretórios
- /home/alisson$ mkdir planilhas
- /$ mkdir /home/alisson/planilhas

## Excluindo arquivos e diretórios
- /home/alisson# rmdir planilhas
- /home/alisson# ls ?l*
- /home/alisson# rm texto1.txt
- /home/alisson# rm -rf Docs -> -r recursiva -f force -> removes todos os arquivos e a pasta Docs
- /home/alisson# rm arquivo[1-2]*

## Obtendo ajuda
- /home/alisson# rm -rfvi Docs -> v verbaliza o que tá fazendo, i pergunta se quer confirmar
- man cd
- man ls
- help ls = ls --help
- ls -lh -> formato humano
- ls -R /etc. -> mostra o conteúdo das pastas e subpastas

diretório		dono   grupo    tam				nome da pasta
drwxrwxrwx 4   root     root     4096   nov 21 2022   boot

link
lrwxrwxrwx 4   root     root     4096   nov 21 2022  

arquivo
-rwxrwxrwx 4   root     root     4096   nov 21 2022 

- root# su alisson
- alisson$ su

- /# export HISTTIMEFORMAT="%d-%m-%y %H:%M:S "
- /# export HISTTIMEFORMAT="%c " =>  269  Sat Aug 13 23:15:07 2022 history | grep ls

## Executando tarefas administrativas como root

Uso do comando sudo antes dos comandos, para processamentos que precisam da permissão de admin

:/$ sudo rm texto.txt
:/$ cat /etc/group


## Logando como usuário root

Nunca fique ligado o tempo todo como root

sudo passwd root

Para logar como super usuário:
su
-> pede a senha do root
root -> enter
root@alisson:/#
su alisson -> volta para o usuário alisson


## Liberando acesso remoto do usuário root


sudo nano /etc/ssh/sshd_config
Altera o arquivo

Restartar:
sudo systemctl restart sshd


## Trabalhando com arquivos de texto

vi leia-me.txt
nano novo-texto.txt

## Histórico de comandos 

history
!10 -> executa o comando 10 da lista do history
!! -> executa o último comando
history | grep “Planilhas"

export HISTTIMEFORMAT=“%c “ -> data e hora no history

history -c -> limpa o histórico

set +o history -> não grava os próximos comandos   
set -o history -> volta

Apenas 50 comandos sejam armazenados
Na pasta do usuário:
ls -la =>
nano .bashrc
Altera a linha: HISTSIZE=50
Salva

history -w => cria um arquivo com a lista de comandos do usuário, na pasta do usuário: .bash_history


# Gerenciando Usuários no Linux

## Criando e excluindo usuários

- /# useradd alisson -m -c "Alisson Rangel"
- /# passwd alisson
New password: 
Retype new password: 
- /# su alisson
- /# chsh --help
Usage: chsh [options] [LOGIN]

Options:
  -h, --help                    display this help message and exit
  -R, --root CHROOT_DIR         directory to chroot into
  -s, --shell SHELL             new login shell for the user account

- /# chsh -s /bin/bash alisson -> cria o shell para o novo usuário, pois ao criá-lo não foi configurado o seu shell

- /# userdel -r -f alisson
- /# useradd alisson -m -c "Alisson Rangel" -s /bin/bash

## Editando informações do usuário
- /# useradd guest -c "Convidado" -m -e 15/08/2022 -> data de expiração do convidado
- /# passwd guest
- /# chsh guest -s /bin/bash
- /# usermod guest -s /bin/bash  -> fazer alteração no user 
- /# usermod guest -s /bin/bash -c "Convidado da silva"
- /# passwd guest -e -> obriga o usuário guest a trocar a senha quando entrar. é preciso que ele já tenha uma senha cadastrada.
- /# su guest -> troca a senha e entra no usuario
- /# cat /etc/passwd -> mostra informações dos users

## Shell Script - Criando usuários em lote
- /# useradd conv1 -m -c "Covidado especial" -s /bin/bash -p $(openssl passwd -crypt conv1)
- nano criar_user.sh 
```
#!/bin/bash -> indica que é um script que eu tou criando

echo "Criando usuarios do sistema..."

useradd guest10 -c "Usuario convidado" -s /bin/bash -m -p $(openssl passwd -crypt conv1)
passwd guest10 -e

useradd guest11 -c "Usuario convidado" -s /bin/bash -m -p $(openssl passwd -crypt conv1)
passwd guest11 -e

useradd guest12 -c "Usuario convidado" -s /bin/bash -m -p $(openssl passwd -crypt conv1)
passwd guest12 -e

echo "Finalizado!!"
```

- /scripts# chmod +x criar_user.sh 
- /scripts# ls -l
total 4
-rwxr-xr-x 1 root root 295 Aug 14 16:35 criar_user.sh

- /scripts# ./criar_user.sh 
Criando usuarios do sistema...
passwd: password expiry information changed.
passwd: password expiry information changed.
passwd: password expiry information changed.
Finalizado!!

## Adicionando usuários a grupos
- useradd guest12 -c "Usuario convidado" -s /bin/bash -m -p $(openssl passwd -crypt conv1)
- /# usermod -G adm,sudo conv1 -> user conv1 irá para os grupos adm e sudo

## Criando novos grupos
crio grupos para poder dar permissões para quem estão em certos grupos
- /# cat /etc/group 
/# groupadd grp_adm
root@2e185a91a1a2:/# groupadd grp_ven
root@2e185a91a1a2:/# groupadd grp_test
root@2e185a91a1a2:/# groupdel grp_test

useradd adm10 -c "Usuario convidado" -s /bin/bash -m -G grp_adm
useradd admt11 -c "Usuario convidado" -s /bin/bash -m -G grp_adm
useradd adm12 -c "Usuario convidado" -s /bin/bash -m -G grp_adm
useradd ven10 -c "Usuario convidado" -s /bin/bash -m -G grp_ven
useradd ven11 -c "Usuario convidado" -s /bin/bash -m -G grp_ven

root@2e185a91a1a2:/scripts# usermod -G grp_adm,sudo,adm alisson -> aloca alisson nesses grupos
root@2e185a91a1a2:/scripts# gpasswd -d alisson grp_adm  -> remove alisson apenas do grupo grp_adm
root@2e185a91a1a2:/scripts# gpasswd -a alisson grp_ven  -> adiciona alisson nesse grupo

## Conhecendo o sistema de permissões
chown (change owner) -> troca o dono do arquivo ou pasta e o grupo do arquivo ou pasta
root@2e185a91a1a2:/# chown admt11:grp_adm  /adm/ -> user admt11, grupo grp_adm, pasta /adm
root@2e185a91a1a2:/# chown ven10:grp_ven /ven/
root@2e185a91a1a2:/ven# chown ven10:grp_ven ven10.txt 

## Alterando as permissões de um diretório - arquivo

r - leitura - 4
w - gravação - 2
x - execução ou entrar na pasta - 1
nenhuma - 0

d[rwx]dono[rwx]grupo[r-x]outros   2 ven10  grp_ven 4096 Aug 14 18:23 ven
chmod 777 /ven/

chmod 750 /adm/  -> drwxr-x---

## Entendendo melhor as permissões de execução para scripts
criando o script date.sh dentro da pasta scripts
```
#!/bin/bash
echo "Mostrando a data atual"
date
```
Ele não vem com a permissão de execução para ninguém
chmod +x date.sh -> adiciona a permissão de execução para todos
chmod -x date.sh 

### PAREI AQUI 31/10/2024
# Gerenciamento de Pacotes Linux

## Gerenciamento de pacotes (UBUNTU-DEBIAN)

- apt-get => mais baixo nivel sem verbosidade
- apt => mais moderno, mais verboso
- apt list --installed
- apt list --upgradable
- apt install net-tools
- ifconfig
- apt install wget
- wget https://github.com/denilsonbonatti/linux-projeto1-iac/archive/refs/heads/main.zip
- apt remove net-tools -y
- apt edit-sources

## Atualização do sistema operacional

- apt upgrade -y


## Gerenciamento de pacotes (FEDORA RED HAT CenTOS)
- dnf
- yum
- *.rpm => executavel

## Realizando a instalação de arquivos DEB
- *.deb

# Gerenciamento de Discos Linux

## Discos, sistemas de arquivos e partições
- lsblk
- fdisk -l
- fdisk -n -> cria uma partição
- mkfs.ext4 /dev/sdb -> formatar
- precisa montar o disco para poder usa-lo
- :/mnt# mount /dev/sdb /mnt/disco2/
- umount /dev/sdb
- nano /etc/fstab


# Copiando Arquivos e Manipulando Processos

## Copiando arquivos

- cp /home/disco2/arq1.txt /home/disco1/
- cp ./* /home/disco3 -r -v -i

## Renomeando Movendo arquivos
- mv /home/disco3/arq1.txt . -v
- mv image1.jpg image01.jpg

## Iniciando, visualizando e encerrando um processo
- ps
- ps aux
- kill id
- killall chrome
- w
- who -a
- kill pid

rm -rfv /home/teste/publico
rm -rfv /home/teste/adm
rm -rfv /home/teste/ven
rm -rfv /home/teste/sec

groupdel GRP_ADM
groupdel GRP_VEN
groupdel GRP_SEC

userdel -r -f carlos
userdel -r -f maria
userdel -r -f joao
userdel -r -f debora
userdel -r -f sebastiana
userdel -r -f roberto
userdel -r -f josefina
userdel -r -f amanda
userdel -r -f rogerio

groupadd GRP_ADM
groupadd GRP_VEN
groupadd GRP_SEC

mkdir publico
mkdir adm 
mkdir ven 
mkdir sec

chown root:root publico
chown root:GRP_ADM adm
chown root:GRP_VEN ven
chown root:GRP_SEC sec

chmod 777 publico
chmod 770 adm
chmod 770 ven
chmod 770 sec

useradd carlos -c "Carlos" -s /bin/bash -m -G GRP_ADM
useradd maria -c "Maria" -s /bin/bash -m -G GRP_ADM
useradd joao -c "Joao" -s /bin/bash -m -G GRP_ADM
useradd debora -c "Debora" -s /bin/bash -m -G GRP_VEN
useradd sebastiana -c "Sebastiana" -s /bin/bash -m -G GRP_VEN
useradd roberto -c "Roberto" -s /bin/bash -m -G GRP_VEN
useradd josefina -c "Josefina" -s /bin/bash -m -G GRP_SEC
useradd amanda -c "Amanda" -s /bin/bash -m -G GRP_SEC
useradd rogerio -c "Rogerio" -s /bin/bash -m -G GRP_SEC

# Servidores de Arquivos com Linux


# Infraestrutura como Código - Script de Provisionamento de um Servidor Web (Apache)

echo "Atualizando o Linux"
apt-get update
apt-get upgrade -y

echo "Instalando o Apache2"
apt-get install apache2 -y
service apache2 restart


echo "Instalando o Unzip e o Wget"
apt-get install unzip -y
apt-get install wget -y

echo "Baixando o site do github"
cd /tmp
wget https://github.com/denilsonbonatti/linux-site-dio/archive/refs/heads/main.zip
unzip main.zip

echo "Copiando os arquivos para a pasta /var/www/html/"
cd linux-site-dio-main
cp -R * /var/www/html/

echo "Finalizado"
