---
title: "Zabbix 4.0 to 6.0 Upgrade, MySQL to PostgreSQL"
date: 2022-10-14T11:16:01+09:00
description: "Guidance on upgrading from Zabbix 4.0 to 6.0 LTS, and how to migrate your database from MySQL to PostgreSQL."
tags: ["Tech"]
cover: "https://live.staticflickr.com/65535/52290241609_e4b82e9b66_h.jpg"
coverCaption: "Roppongi (六本木) District, Tokyo"
images: ["https://live.staticflickr.com/65535/52290241609_e4b82e9b66_h.jpg"]
---

### Something I've recently been working on is upgrading a large number of Zabbix servers

- Zabbix 4.0 LTS to 6.0 LTS
- CentOS 7 to Ubuntu 20.04
- MySQL to PostgreSQL

### This is the workflow I settled on:

- Backup the original CentOS 7 server and do a DB dump
- Deploy a new *temporary* Ubuntu 20.04 server - we will use this server for the purposes of the upgrade and then delete it later
- Install Zabbix 4.0 and PostgreSQL 12 using Ansible (https://galaxy.ansible.com/community/zabbix, https://galaxy.ansible.com/geerlingguy/postgresql)
- Transfer the DB from the original server to the new server, and migrate it to PostgreSQL 12 using `pgloader`
- After validating the migration was successful, upgrade Zabbix 4.0 to 6.0
- Upgrade PostgreSQL from 12 to 13
- Start the Zabbix service and validate that we are now successfully running 6.0 on pgsql13 and the data is intact
- Power off the original CentOS 7 server, to avoid IP conflicts
- Deploy a new Ubuntu 20.04 server using the same IP as the original server - this will be the final destination for our Zabbix
- Install Zabbix 6.0 and PostgreSQL 13 using Ansible (https://galaxy.ansible.com/community/zabbix, https://galaxy.ansible.com/geerlingguy/postgresql)
- Import the DB from the temporary upgrade server, and start the Zabbix service
- Done!

### Here are my notes containing more details about each step:

You can also view them on [GitHub Gist](https://gist.github.com/mcbrineellis/18b04d2f77a2ee74ff6ca4ad6b460616)

<script src="https://gist.github.com/mcbrineellis/18b04d2f77a2ee74ff6ca4ad6b460616.js"></script>

A big thanks to these resources which helped me accomplish this task:

- [MySQL Database Migration To PostgreSQL - *Dmitry Lambert (YouTube)*](https://www.youtube.com/watch?v=S-C5NCZJnt0)
- [Upgrade Zabbix to 6.0 like a Pro - *bestmonitoringtools.com*](https://bestmonitoringtools.com/upgrade-zabbix-to-the-latest-version)
- [How to upgrade PostgreSQL from 12 to 13 - *Juraj Kostolanský*](https://www.kostolansky.sk/posts/upgrading-to-postgresql-12/)