# User group and management

[Course] (https://app.pluralsight.com/library/courses/lfcs-linux-user-group-management/table-of-contents)

## Users & services

```bash
cat /etc/passwd
getent passwd       #users for everydomain (ldap, active directory)
getent { group | networks | services }      # access db


grep passwd /etc/nsswitch.conf  # config where it comes from (db, files, sss ?)
> #passwd:    db files nisplus nis
> passwd:     files     #sss: system security 


history -c      #clear history

```

## Login scripts

/etc/profile vs /etc/bashrc

In profile we set paths, alias, exports (run only once)
Profile:
- PATH=$PATH:~/bin
- export PATH

In bashrc set stuff run for every bash instance (PS1, ...)
Bashrc:
- PS1="[\u@\h \w] \$"

su - or su -l
1. /etc/bashrc
2. /etc/profile
3. ~/.bashrc
4. ~/.bash_profile

su
1. ~/.bashrc
2. /etc/bashrc

```bash
su - (su -l)    # switch to root as full privileges (change user, pwd, ...)
su              # only privileges not full login (echo $USER will show user logged in)

# info on user
echo $USER
whoami
id

exit            #run a ~/.bash_logout if exists

cd /etc
ls profile*

> profile
> profile.d/

ls /etc/bash*
> /etc/bashrc

cd /etc/skel
ls -A
.bash_logout  .bash_profile  .bashrc

```

## Create User locally, set password

```bash
id
id -g       #show primary group
id -Gn      #show complementary groups name

sudo useradd -m user1 -g users -G adm  -s /bin/bash         #-g primary group ; -G secondary group ; -m:create home dir ; -s shell to use
sudo useradd -M test                                        # No home directory
tail -n 1 /etc/passwd
> user1:x:1001:1001::/home/user1:/bin/bash                  # x: password is in shadow

ls -l /sbin/useradd
> lrwxrwxrwx. 1 root root 7 Apr  1 09:30 /sbin/adduser -> useradd


sudo passwd user1
grep user1 /etc/shadow      #sha512 encryption

> user1:$6$B6TwADxk$LJSFS/qc5oX/Qc/MnAKCvqNYYIkmETGVhXE2h0SFjQZ3AcX0RnUnKeUVrDNnD4iTOrHtJwGQVmB/ZGlt0HoAb/:18376:0:99999:7:::
> user2:!!:18376:0:99999:7:::   #user_id - !! invalid - 18376: date changed - 99999 nbr days valid -7 days warning password to be changed

echo 'user2:Password1' | sudo chpasswd      #change password in one line
echo Password1 | sudo passwd user3 --stdin  #Centos only - not

chage -l <user>
Last password change                                : never
Password expires                                    : never
Password inactive                                   : never
Account expires                                     : never
Minimum number of days between password change      : 0
Maximum number of days between password change      : 99999
Number of days of warning before password expires   : 7


sud0 pwunconv       #store password in /etc/passwd instead of shadow file - warning read privileges can brute force the hash
sudo pwconv         #convert to shadow (more secure)

chage -help
Options:
  -d, --lastday LAST_DAY        set date of last password change to LAST_DAY
  -E, --expiredate EXPIRE_DATE  set account expiration date to EXPIRE_DATE
  -h, --help                    display this help message and exit
  -I, --inactive INACTIVE       set password inactive after expiration
                                to INACTIVE
  -l, --list                    show account aging information
  -m, --mindays MIN_DAYS        set minimum number of days before password
                                change to MIN_DAYS
  -M, --maxdays MAX_DAYS        set maximum number of days before password
                                change to MAX_DAYS
  -R, --root CHROOT_DIR         directory to chroot into
  -W, --warndays WARN_DAYS      set expiration warning days to WARN_DAYS

chage -M 30 <user_id>       # set max days 30 to user_id

sudo passwd -l user1        # lock password for user1
sudo passwd -u user1        # unlock password

```

### Defaults values

```bash
chsh -l                     #List of shell available
less /etc/login.defs        #define some configs (create a home account, ...)

sudo useradd -D             # show default values for user creation
cat /etc/default/useradd

sudo usermod -D                         #List of options
sudo usermod -c "User One" user1        #Set user name
sudo usermod -s /bin/bash <user>        #Set shell script for <user>

sudo useradd -Ds /bin/sh    # set bourne shell to be default shell for useradd script
```

### Delete users

```bash

userdel -r user1                        #-r remove cron, folder dir, mail spool ...
find /home -uid 1002 -delete
```

### Groups

```bash
grep <user> /etc/group
id <user> -Gn     #Show all groups
id <user> -g      #Show primary group

newgrp wheel      #change group primary for a user during the session

sudo groupadd sales
sudo groupdel <group_id>
sudo grep sales /etc/gshadow

sudo usermod -G sales,wheel <user>  #Add user to a list of secondary group
sudo gpasswd -a <user>  <group>     #Append a user to a group
sudo gpasswd -M bob,jo,tux group1 group2 ... #Set lis of members of Group

sudo gpasswd --help


#SGID - group id bit on a directory
sudo chgrp -R apache /var/www
sudo chmod -R o= /var/www

sudo chmod g+x /var/www/html  #set group bit
umask 027                     #set mask to set the correct permission for new files/folders


#/etc/gshadow
sudo gpasswd adm    #add password to a  - NOT A GOOD IDEA
newgrp adm          #will ask the group password
```

### PAM (plugable authentication module)

```bash
ls /etc/pam.d/          #programs can login through PAM
ls /lib64/security      #
ls /etc/security        #config for pam

rpm -qa | grep oddjob   #

yum install oddjob
systemctl enable oddjobd

sudo authconfig --enablemkhomedir --update    #Create a home dir at login

#Password policies
cat /etc/pam.d/system-auth      #pam_pwquality
vim /etc/security/pwquality.conf

pwscore     #to test quality of password / base on pam_pwquality

#Restrict access to Resources
ulimit -a
ulimit -u 10      #set child processes to 10

#/etc/security/limits.conf
> @users		soft	nproc		50    #set limit soft of number of processes
> @users		hard	nproc		75    #set hard limit (cannot exceed)

ulimit - u 
> ulimit: max user processes: cannot modify limit: Operation not permitted


#add login restriction
vim /etc/pam.d/sshd
+ account   required    pam_time.so

#/etc/security/time.conf
+ *;*;tux|bob;!Wk0800~1800   #allow tux, bob to connect NOT weekdays between 800-1800
```


## Open LDAP

```bash
firewall-cmd --permanent --add-service=ldap
firewall-cmd --reload

yum install -y openldap openldap-clients openldap-servers migrationtools.noarch

cp /usr/share/openldap-servers/DB_CONFIG.example /var/lib/ldap/DB_CONFIG

slaptest    #create db
chown ldap:ldap /var/lib/ldap/*

systemctl {enable|start} slapd

netstat -ltn|lt   #check port listen

cd /etc/opendldap/schema
ldapadd - Y EXTERNAL -H ldapi:/// -D "cn=config" -f cosine.ldif
ldapadd - Y EXTERNAL -H ldapi:/// -D "cn=config" -f nis.ldif

slappasswd -s camper -n > rootpwd
cat rootpwd

# create a config.ldif
# create a structure.ldiff

ldapadd -x -W -D "cn=Manager,dc=example,dc=com" -f structure.ldif
```
