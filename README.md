# **Введение**

В данном домашнем задании нам необходимо установить и использовать FreeIPA на CentOS.

---
# Работа со стендом и настройка Server и Client.
1. Для выполнения данного ДЗ, необходимо скачать стенд и запустить его.
```
git clone https://github.com/Dmitriy-Iv/Otus-Pr-linux-HW25.git
cd Otus-Pr-linux-HW25/Ansible/
vagrant up
```
2. После это у нас поднимутся два виртуальных сервера, на одном из которых будет стоять Freeipa-server, а другой будет клиентом.
Для настройки был использован пакетный режим, информация взята из следующих источников:
- https://www.freeipa.org/page/Quick_Start_Guide#Preparing_a_Platform
- https://www.altlinux.org/FreeIPA/%D0%A3%D1%81%D1%82%D0%B0%D0%BD%D0%BE%D0%B2%D0%BA%D0%B0_%D1%81%D0%B5%D1%80%D0%B2%D0%B5%D1%80%D0%B0_FreeIPA#%D0%98%D0%BD%D1%82%D0%B5%D1%80%D0%B0%D0%BA%D1%82%D0%B8%D0%B2%D0%BD%D0%B0%D1%8F_%D1%83%D1%81%D1%82%D0%B0%D0%BD%D0%BE%D0%B2%D0%BA%D0%B0
- https://www.altlinux.org/FreeIPA/%D0%9A%D0%BB%D0%B8%D0%B5%D0%BD%D1%82
- https://linux.die.net/man/1/ipa-server-install
Для добавления пользователей и групп использовались готовые ansible module - `ipa_user` и `ipa_group`.

3. В ходе ДЗ мы настроили Freeipa-server и подключили к нему client. Также с client создали 2 пользователей - `petya` и `vasya`, и добавили их в группу `manager`.
```
[root@clientipa ~]# kinit admin
Password for admin@IPA.TEST:

[root@clientipa ~]# klist
Ticket cache: KEYRING:persistent:0:0
Default principal: admin@IPA.TEST
Valid starting       Expires              Service principal
11.07.2022 19:17:32  12.07.2022 19:17:23  krbtgt/IPA.TEST@IPA.TEST

[root@clientipa ~]# ipa user-find --all
---------------
3 users matched
---------------
  dn: uid=admin,cn=users,cn=accounts,dc=ipa,dc=test
  User login: admin
  Last name: Administrator
  Full name: Administrator
  Home directory: /home/admin
  GECOS: Administrator
  Login shell: /bin/bash
  Principal alias: admin@IPA.TEST
  User password expiration: 20221009160847Z
  UID: 227800000
  GID: 227800000
  Account disabled: False
  Preserved user: False
  Member of groups: admins, trust admins
  ipauniqueid: 2ba85bc6-0133-11ed-8fb3-5254004d77d3
  krbextradata: AAIPS8xicm9vdC9hZG1pbkBJUEEuVEVTVAA=
  krblastpwdchange: 20220711160847Z
  objectclass: top, person, posixaccount, krbprincipalaux, krbticketpolicyaux, inetuser, ipaobject, ipasshuser, ipaSshGroupOfPubKeys

  dn: uid=petya,cn=users,cn=accounts,dc=ipa,dc=test
  User login: petya
  First name: petya
  Last name: ivanov
  Full name: petya ivanov
  Display name: petya ivanov
  Initials: pi
  Home directory: /home/petya
  GECOS: petya ivanov
  Login shell: /bin/sh
  Principal name: petya@IPA.TEST
  Principal alias: petya@IPA.TEST
  User password expiration: 20220711160955Z
  Email address: petya@ipa.test
  UID: 227800001
  GID: 227800001
  Account disabled: False
  Preserved user: False
  Member of groups: ipausers, manager
  ipauniqueid: e818f07c-0133-11ed-b04b-5254004d77d3
  krbextradata: AAJTS8xicm9vdC9hZG1pbkBJUEEuVEVTVAA=
  krblastpwdchange: 20220711160955Z
  mepmanagedentry: cn=petya,cn=groups,cn=accounts,dc=ipa,dc=test
  objectclass: top, person, organizationalperson, inetorgperson, inetuser, posixaccount, krbprincipalaux, krbticketpolicyaux, ipaobject, ipasshuser, ipaSshGroupOfPubKeys, mepOriginEntry

  dn: uid=vasya,cn=users,cn=accounts,dc=ipa,dc=test
  User login: vasya
  First name: vasya
  Last name: sidorov
  Full name: vasya sidorov
  Display name: vasya sidorov
  Initials: vs
  Home directory: /home/vasya
  GECOS: vasya sidorov
  Login shell: /bin/sh
  Principal name: vasya@IPA.TEST
  Principal alias: vasya@IPA.TEST
  User password expiration: 20220711160956Z
  Email address: vasya@ipa.test
  UID: 227800003
  GID: 227800003
  Account disabled: False
  Preserved user: False
  Member of groups: ipausers, manager
  ipauniqueid: e8a18b08-0133-11ed-8671-5254004d77d3
  krbextradata: AAJUS8xicm9vdC9hZG1pbkBJUEEuVEVTVAA=
  krblastpwdchange: 20220711160956Z
  mepmanagedentry: cn=vasya,cn=groups,cn=accounts,dc=ipa,dc=test
  objectclass: top, person, organizationalperson, inetorgperson, inetuser, posixaccount, krbprincipalaux, krbticketpolicyaux, ipaobject, ipasshuser, ipaSshGroupOfPubKeys, mepOriginEntry
----------------------------
Number of entries returned 3
----------------------------

[root@clientipa ~]# ipa group-find --all
----------------
5 groups matched
----------------
  dn: cn=admins,cn=groups,cn=accounts,dc=ipa,dc=test
  Group name: admins
  Description: Account administrators group
  GID: 227800000
  Member users: admin
  ipauniqueid: 2baa79c4-0133-11ed-910b-5254004d77d3
  objectclass: top, groupofnames, posixgroup, ipausergroup, ipaobject, nestedGroup

  dn: cn=editors,cn=groups,cn=accounts,dc=ipa,dc=test
  Group name: editors
  Description: Limited admins who can edit other users
  GID: 227800002
  ipauniqueid: 2babd7ce-0133-11ed-ad87-5254004d77d3
  objectclass: top, groupofnames, posixgroup, ipausergroup, ipaobject, nestedGroup

  dn: cn=ipausers,cn=groups,cn=accounts,dc=ipa,dc=test
  Group name: ipausers
  Description: Default group for all users
  Member users: petya, vasya
  ipauniqueid: 2baba5ec-0133-11ed-9e73-5254004d77d3
  objectclass: top, groupofnames, nestedgroup, ipausergroup, ipaobject

  dn: cn=manager,cn=groups,cn=accounts,dc=ipa,dc=test
  Group name: manager
  GID: 227800004
  Member users: vasya, petya
  ipauniqueid: e9579fce-0133-11ed-9d66-5254004d77d3
  objectclass: top, groupofnames, nestedgroup, ipausergroup, ipaobject, posixgroup

  dn: cn=trust admins,cn=groups,cn=accounts,dc=ipa,dc=test
  Group name: trust admins
  Description: Trusts administrators group
  Member users: admin
  ipauniqueid: ae6ca526-0133-11ed-9ec4-5254004d77d3
  objectclass: top, groupofnames, ipausergroup, nestedgroup, ipaobject
----------------------------
Number of entries returned 5
----------------------------
```
Из вывода видно, что клиент у нас получил ticket и работает с сервером FreeIpa.