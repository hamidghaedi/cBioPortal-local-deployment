# cBioPortal Local Deployment

This guide walks you through the process of deploying a local instance of cBioPortal using Docker on a Windows machine, including customization and importing studies. The deployment is done within a Windows Subsystem for Linux (WSL) environment.
Please note that Docker needs to be installed and running on Windows before initiating the WSL terminal.


## Deployment
### Repo Cloning

My wd is as :
```bash
usr@LAPTOP-7A1QP0HH:~/cbioportal-docker-compose$ pwd
/home/usr/cbioportal-docker-compose
```

The `init.sh` script initializes the setup and downloads necessary files, including seed data.

```bash
git clone https://github.com/cBioPortal/cbioportal-docker-compose.git
cd cbioportal-docker-compose
./init.sh

# --2024-02-21 09:07:33--  https://raw.githubusercontent.com/cBioPortal/cbioportal/v5.3.6/db-scripts/src/main/resources/cgds.sql
# Resolving raw.githubusercontent.com (raw.githubusercontent.com)... 185.199.109.133, 185.199.110.133, 185.199.111.133, ...
# Connecting to raw.githubusercontent.com (raw.githubusercontent.com)|185.199.109.133|:443... connected.
# HTTP request sent, awaiting response... 200 OK
# Length: 30598 (30K) [text/plain]
# Saving to: ‘cgds.sql’

# cgds.sql                                             100%[===================================================================================================================>]  29.88K  --.-KB/s    in 0.006s

# 2024-02-21 09:07:33 (5.28 MB/s) - ‘cgds.sql’ saved [30598/30598]

# --2024-02-21 09:07:33--  https://github.com/cBioPortal/datahub/raw/master/seedDB/seedDB_hg19_archive/seed-cbioportal_hg19_v2.12.14.sql.gz
# Resolving github.com (github.com)... 140.82.114.3, 198.51.44.8, 198.51.45.8, ...
# Connecting to github.com (github.com)|140.82.114.3|:443... connected.
# HTTP request sent, awaiting response... 302 Found
# Location: https://raw.githubusercontent.com/cBioPortal/datahub/master/seedDB/seedDB_hg19_archive/seed-cbioportal_hg19_v2.12.14.sql.gz [following]
# --2024-02-21 09:07:33--  https://raw.githubusercontent.com/cBioPortal/datahub/master/seedDB/seedDB_hg19_archive/seed-cbioportal_hg19_v2.12.14.sql.gz
# Resolving raw.githubusercontent.com (raw.githubusercontent.com)... 185.199.109.133, 185.199.110.133, 185.199.111.133, ...
# Connecting to raw.githubusercontent.com (raw.githubusercontent.com)|185.199.109.133|:443... connected.
# HTTP request sent, awaiting response... 200 OK
# Length: 51337072 (49M) [application/octet-stream]
# Saving to: ‘seed.sql.gz’

# seed.sql.gz                                          100%[===================================================================================================================>]  48.96M  8.50MB/s    in 5.8s

# 2024-02-21 09:07:40 (8.45 MB/s) - ‘seed.sql.gz’ saved [51337072/51337072]

# --2024-02-21 09:07:40--  https://cbioportal-datahub.s3.amazonaws.com/lgg_ucsf_2014.tar.gz
# Resolving cbioportal-datahub.s3.amazonaws.com (cbioportal-datahub.s3.amazonaws.com)... 52.216.89.252, 52.216.205.27, 52.216.54.57, ...
# Connecting to cbioportal-datahub.s3.amazonaws.com (cbioportal-datahub.s3.amazonaws.com)|52.216.89.252|:443... connected.
# HTTP request sent, awaiting response... 200 OK
# Length: 739531 (722K) [application/x-tar]
# Saving to: ‘lgg_ucsf_2014.tar.gz’

# lgg_ucsf_2014.tar.gz                                 100%[===================================================================================================================>] 722.20K  2.99MB/s    in 0.2s

# 2024-02-21 09:07:40 (2.99 MB/s) - ‘lgg_ucsf_2014.tar.gz’ saved [739531/739531]
# lgg_ucsf_2014/
# lgg_ucsf_2014/LICENSE
# lgg_ucsf_2014/case_lists/
# lgg_ucsf_2014/case_lists/cases_sequenced.txt
# lgg_ucsf_2014/case_lists/cases_all.txt
# lgg_ucsf_2014/data_mutations.txt
# lgg_ucsf_2014/data_timeline_treatment.txt
# lgg_ucsf_2014/meta_mutations.txt
# lgg_ucsf_2014/meta_clinical_sample.txt
# lgg_ucsf_2014/meta_timeline_status.txt
# lgg_ucsf_2014/data_clinical_patient.txt
# lgg_ucsf_2014/meta_study.txt
# lgg_ucsf_2014/data_timeline_surgery.txt
# lgg_ucsf_2014/meta_clinical_patient.txt
# lgg_ucsf_2014/meta_timeline_treatment.txt
# lgg_ucsf_2014/data_timeline_status.txt
# lgg_ucsf_2014/data_clinical_sample.txt
# lgg_ucsf_2014/meta_timeline_surgery.txt
# lgg_ucsf_2014/meta_timeline_specimen.txt
# lgg_ucsf_2014/data_timeline_specimen.txt
```
### Setting up the database container

```bash
docker-compose up cbioportal-database

# [+] Running 2/1
#  ✔ Network cbioportal-docker-compose_cbio-net  Created                                                                                                                                                      0.1s
#  ✔ Container cbioportal-database-container     Created                                                                                                                                                      0.1s
# Attaching to cbioportal-database-container ...
#.
#.
#.
#cbioportal-database-container  | 2024-02-21T14:02:40.020479Z 0 [Note] InnoDB: Buffer pool(s) load completed at 240221 14:02:40
#cbioportal-database-container  | 2024-02-21T14:02:40.024527Z 0 [Warning] Insecure configuration for --pid-file: Location '/var/run/mysqld' in the path is accessible to all OS users. Consider choosing a different directory.
#cbioportal-database-container  | 2024-02-21T14:02:40.084253Z 0 [Note] Event Scheduler: Loaded 0 events
#cbioportal-database-container  | 2024-02-21T14:02:40.084660Z 0 [Note] mysqld: ready for connections.
#cbioportal-database-container  | Version: '5.7.44'  socket: '/var/run/mysqld/mysqld.sock'  port: 3306  MySQL Community Server (GPL)
```

Stop the container by pressing Ctrl+C when it's finished initializing, then proceed with the next steps.


### Explicitly Specifying Seed Data

```bash
docker run -d --name cbioportal-container -v /home/usr/cbioportal-docker-compose/data/cgds.sql:/docker-entrypoint-initdb.d/cgds.sql \
-v /home/usr/cbioportal-docker-compose/data/seed.sql.gz:/docker-entrypoint-initdb.d/seed.sql.gz \
-v /home/usr/cbioportal-docker-compose/images/cctg_logo.png:/cbioportal-webapp/images/cctg_logo.png \
-p 8080:8080 -p 27017:27017 -p 8000:8000 -p 8001:8001 -p 8081:8081 -p 8082:8082 -p 8083:8083 -p 8084:8084 cbioportal/cbioportal


docker stop $(docker ps -aq)
# 7d2a032a2615
# 132da4692bd1
# 0eb5ee498374
# 001ce521f267
```

### Running the containers
```bash
docker compose up

# [+] Running 2/2
#  ✔ Container cbioportal-database-container  Created                                                                                                                                                         0.0s
#  ✔ Container cbioportal-container           Created                                                                                                                                                         0.1s
# Attaching to cbioportal-container, cbioportal-database-container, cbioportal-session-container, cbioportal-session-database-container
# cbioportal-database-container          | 2024-02-21 14:18:19+00:00 [Note] [Entrypoint]: Entrypoint script for MySQL Server 5.7.44-1.el7 started.
# cbioportal-session-database-container  | 2024-02-21T14:18:19.385+0000 I  CONTROL  [main] Automatically disabling TLS 1.0, to force-enable TLS 1.0 specify --sslDisabledProtocols 'none'
# cbioportal-database-container          | 2024-02-21 14:18:19+00:00 [Note] [Entrypoint]: Switching to dedicated user 'mysql'
# cbioportal-session-database-container  | 2024-02-21T14:18:19.391+0000 W  ASIO     [main] No TransportLayer configured during NetworkInterface startup
# cbioportal-session-database-container  | 2024-02-21T14:18:19.392+0000 I  CONTROL  [initandlisten] MongoDB starting : pid=1 port=27017 dbpath=/data/db 64-bit host=132da4692bd1
# cbioportal-session-database-container  | 2024-02-21T14:18:19.392+0000 I  CONTROL  [initandlisten] db version v4.2.24
# cbioportal-session-database-container  | 2024-02-21T14:18:19.392+0000 I  CONTROL  [initandlisten] git version: 5e4ec1d24431fcdd28b579a024c5c801b8cde4e2
# cbioportal-session-database-container  | 2024-02-21T14:18:19.392+0000 I  CONTROL  [initandlisten] OpenSSL version: OpenSSL 1.1.1  11 Sep 2018
#.
#.
#.
# cbioportal-container exited with code 1
# cbioportal-container                   | db_connection_string=jdbc:mysql://cbioportal-database:3306/cbioportal
# cbioportal-container                   | mysqladmin: [Warning] Using a password on the command line interface can be insecure.
# '@'172.18.0.5' (using password: YES)   | 2024-02-21T14:31:13.825585Z 44 [Note] Access denied for user 'cbio_user
# cbioportal-container                   | Database connection success
# cbioportal-container                   | Migrating database if necessary...
# cbioportal-container                   | (1146, "Table 'cbioportal.patient_list' doesn't exist")
# cbioportal-container                   | Running statements for version: 1.1.0
# cbioportal-container                   |        Executing statement: CREATE TABLE sample_list LIKE patient_list;
```
If you encounter the error `Error response from daemon: Conflict. The container name "/cbioportal-container" is already in use by container "0eb5ee4983742eaf232fcd9fe84465c17750230bee31a3e478eff309c7addaf8". You have to remove (or rename) that container to be able to reuse that name.`, consider removing the conflicting container by `docker rm -f cbioportal-container` and then re-run `docker-compose up`. If the error persists, use Ctrl+C to stop the container, then `run docker-compose down -v` to stop and remove all the containers defined in the Docker compose file, along with any associated volumes.
```bash
docker-compose down -v

# [+] Running 7/7
#  ✔ Container cbioportal-container                          Removed                                                                                                                                          0.0s
#  ✔ Container cbioportal-session-container                  Removed                                                                                                                                          0.0s
#  ✔ Container cbioportal-database-container                 Removed                                                                                                                                          0.0s
#  ✔ Container cbioportal-session-database-container         Removed                                                                                                                                          0.0s
#  ✔ Volume cbioportal-docker-compose_cbioportal_mysql_data  Removed                                                                                                                                          0.0s
#  ✔ Volume cbioportal-docker-compose_cbioportal_mongo_data  Removed                                                                                                                                          0.0s
#  ✔ Network cbioportal-docker-compose_cbio-net              Removed 
```
Then start all the services defined in the Docker compose file in detached mode (-d: indicates it starts the containers in the background).
```bash
docker-compose up -d

# [+] Running 7/7
#  ✔ Network cbioportal-docker-compose_cbio-net                Created                                                                                                                                        0.0s
#  ✔ Volume "cbioportal-docker-compose_cbioportal_mongo_data"  Created                                                                                                                                        0.0s
#  ✔ Volume "cbioportal-docker-compose_cbioportal_mysql_data"  Created                                                                                                                                        0.0s
#  ✔ Container cbioportal-session-database-container           Started                                                                                                                                        0.1s
#  ✔ Container cbioportal-database-container                   Started                                                                                                                                        0.1s
#  ✔ Container cbioportal-session-container                    Started                                                                                                                                        0.1s
#  ✔ Container cbioportal-container                            Started
```

### Checking the log

```bash
docker-compose logs cbioportal

# cbioportal-container  | Using database config:
# cbioportal-container  | db_user=cbio_user
# cbioportal-container  | db_password=somepassword
# cbioportal-container  | db_connection_string=jdbc:mysql://cbioportal-database:3306/cbioportal
# cbioportal-container  | mysqladmin: [Warning] Using a password on the command line interface can be insecure.
#  -psomepasswordainer  | mysqladmin ping -s -hcbioportal-database -P3306 -ucbio_user
# cbioportal-container  | Database not available yet (first time can take a few minutes to load seed database)... Attempting reconnect...
# cbioportal-container  | mysqladmin: [Warning] Using a password on the command line interface can be insecure.
#  -psomepasswordainer  | mysqladmin ping -s -hcbioportal-database -P3306 -ucbio_user
# cbioportal-container  | Database not available yet (first time can take a few minutes to load seed database)... Attempting reconnect...
# cbioportal-container  | mysqladmin: [Warning] Using a password on the command line interface can be insecure.
#  -psomepasswordainer  | mysqladmin ping -s -hcbioportal-database -P3306 -ucbio_user
# cbioportal-container  | Database not available yet (first time can take a few minutes to load seed database)... Attempting reconnect...
# cbioportal-container  | mysqladmin: [Warning] Using a password on the command line interface can be insecure.
#  -psomepasswordainer  | mysqladmin ping -s -hcbioportal-database -P3306 -ucbio_user
# cbioportal-container  | Database not available yet (first time can take a few minutes to load seed database)... Attempting reconnect...
# cbioportal-container  | mysqladmin: [Warning] Using a password on the command line interface can be insecure.
#  -psomepasswordainer  | mysqladmin ping -s -hcbioportal-database -P3306 -ucbio_user
# cbioportal-container  | Database not available yet (first time can take a few minutes to load seed database)... Attempting reconnect...
# cbioportal-container  | mysqladmin: [Warning] Using a password on the command line interface can be insecure.
#  -psomepasswordainer  | mysqladmin ping -s -hcbioportal-database -P3306 -ucbio_user
# cbioportal-container  | Database not available yet (first time can take a few minutes to load seed database)... Attempting reconnect...
# cbioportal-container  | mysqladmin: [Warning] Using a password on the command line interface can be insecure.
#  -psomepasswordainer  | mysqladmin ping -s -hcbioportal-database -P3306 -ucbio_user
# cbioportal-container  | Database not available yet (first time can take a few minutes to load seed database)... Attempting reconnect...
# cbioportal-container  | mysqladmin: [Warning] Using a password on the command line interface can be insecure.
#  -psomepasswordainer  | mysqladmin ping -s -hcbioportal-database -P3306 -ucbio_user
# cbioportal-container  | Database not available yet (first time can take a few minutes to load seed database)... Attempting reconnect...
# cbioportal-container  | mysqladmin: [Warning] Using a password on the command line interface can be insecure.
#  -psomepasswordainer  | mysqladmin ping -s -hcbioportal-database -P3306 -ucbio_user
# cbioportal-container  | Database not available yet (first time can take a few minutes to load seed database)... Attempting reconnect...
# cbioportal-container  | mysqladmin: [Warning] Using a password on the command line interface can be insecure.
#  -psomepasswordainer  | mysqladmin ping -s -hcbioportal-database -P3306 -ucbio_user
# cbioportal-container  | Database not available yet (first time can take a few minutes to load seed database)... Attempting reconnect...
# cbioportal-container  | mysqladmin: [Warning] Using a password on the command line interface can be insecure.
#  -psomepasswordainer  | mysqladmin ping -s -hcbioportal-database -P3306 -ucbio_user
# cbioportal-container  | Database not available yet (first time can take a few minutes to load seed database)... Attempting reconnect...
# cbioportal-container  | mysqladmin: [Warning] Using a password on the command line interface can be insecure.
#  -psomepasswordainer  | mysqladmin ping -s -hcbioportal-database -P3306 -ucbio_user
# cbioportal-container  | Database not available yet (first time can take a few minutes to load seed database)... Attempting reconnect...
# cbioportal-container  | mysqladmin: [Warning] Using a password on the command line interface can be insecure.
#  -psomepasswordainer  | mysqladmin ping -s -hcbioportal-database -P3306 -ucbio_user
# cbioportal-container  | Database not available yet (first time can take a few minutes to load seed database)... Attempting reconnect...
# cbioportal-container  | mysqladmin: [Warning] Using a password on the command line interface can be insecure.
#  -psomepasswordainer  | mysqladmin ping -s -hcbioportal-database -P3306 -ucbio_user
# cbioportal-container  | Database not available yet (first time can take a few minutes to load seed database)... Attempting reconnect...
# cbioportal-container  | mysqladmin: [Warning] Using a password on the command line interface can be insecure.
#  -psomepasswordainer  | mysqladmin ping -s -hcbioportal-database -P3306 -ucbio_user
# cbioportal-container  | Database not available yet (first time can take a few minutes to load seed database)... Attempting reconnect...
# cbioportal-container  | mysqladmin: [Warning] Using a password on the command line interface can be insecure.
#  -psomepasswordainer  | mysqladmin ping -s -hcbioportal-database -P3306 -ucbio_user
# cbioportal-container  | Database not available yet (first time can take a few minutes to load seed database)... Attempting reconnect...
# cbioportal-container  | mysqladmin: [Warning] Using a password on the command line interface can be insecure.
#  -psomepasswordainer  | mysqladmin ping -s -hcbioportal-database -P3306 -ucbio_user
# cbioportal-container  | Database not available yet (first time can take a few minutes to load seed database)... Attempting reconnect...
# cbioportal-container  | mysqladmin: [Warning] Using a password on the command line interface can be insecure.
#  -psomepasswordainer  | mysqladmin ping -s -hcbioportal-database -P3306 -ucbio_user
# cbioportal-container  | Database not available yet (first time can take a few minutes to load seed database)... Attempting reconnect...
# cbioportal-container  | mysqladmin: [Warning] Using a password on the command line interface can be insecure.
#  -psomepasswordainer  | mysqladmin ping -s -hcbioportal-database -P3306 -ucbio_user
# cbioportal-container  | Database not available yet (first time can take a few minutes to load seed database)... Attempting reconnect...
# cbioportal-container  | mysqladmin: [Warning] Using a password on the command line interface can be insecure.
#  -psomepasswordainer  | mysqladmin ping -s -hcbioportal-database -P3306 -ucbio_user
# cbioportal-container  | Database not available yet (first time can take a few minutes to load seed database)... Attempting reconnect...
# cbioportal-container  | mysqladmin: [Warning] Using a password on the command line interface can be insecure.
#  -psomepasswordainer  | mysqladmin ping -s -hcbioportal-database -P3306 -ucbio_user
# cbioportal-container  | Database not available yet (first time can take a few minutes to load seed database)... Attempting reconnect...
# cbioportal-container  | mysqladmin: [Warning] Using a password on the command line interface can be insecure.
#  -psomepasswordainer  | mysqladmin ping -s -hcbioportal-database -P3306 -ucbio_user
# cbioportal-container  | Database not available yet (first time can take a few minutes to load seed database)... Attempting reconnect...
# cbioportal-container  | mysqladmin: [Warning] Using a password on the command line interface can be insecure.
#  -psomepasswordainer  | mysqladmin ping -s -hcbioportal-database -P3306 -ucbio_user
# cbioportal-container  | Database not available yet (first time can take a few minutes to load seed database)... Attempting reconnect...
# cbioportal-container  | mysqladmin: [Warning] Using a password on the command line interface can be insecure.
#  -psomepasswordainer  | mysqladmin ping -s -hcbioportal-database -P3306 -ucbio_user
# cbioportal-container  | Database not available yet (first time can take a few minutes to load seed database)... Attempting reconnect...
# cbioportal-container  | mysqladmin: [Warning] Using a password on the command line interface can be insecure.
#  -psomepasswordainer  | mysqladmin ping -s -hcbioportal-database -P3306 -ucbio_user
# cbioportal-container  | Database not available yet (first time can take a few minutes to load seed database)... Attempting reconnect...
# cbioportal-container  | mysqladmin: [Warning] Using a password on the command line interface can be insecure.
#  -psomepasswordainer  | mysqladmin ping -s -hcbioportal-database -P3306 -ucbio_user
# cbioportal-container  | Database not available yet (first time can take a few minutes to load seed database)... Attempting reconnect...
# cbioportal-container  | mysqladmin: [Warning] Using a password on the command line interface can be insecure.
#  -psomepasswordainer  | mysqladmin ping -s -hcbioportal-database -P3306 -ucbio_user
# cbioportal-container  | Database not available yet (first time can take a few minutes to load seed database)... Attempting reconnect...
# cbioportal-container  | mysqladmin: [Warning] Using a password on the command line interface can be insecure.
#  -psomepasswordainer  | mysqladmin ping -s -hcbioportal-database -P3306 -ucbio_user
# cbioportal-container  | Database not available yet (first time can take a few minutes to load seed database)... Attempting reconnect...
# cbioportal-container  | mysqladmin: [Warning] Using a password on the command line interface can be insecure.
#  -psomepasswordainer  | mysqladmin ping -s -hcbioportal-database -P3306 -ucbio_user
# cbioportal-container  | Database not available yet (first time can take a few minutes to load seed database)... Attempting reconnect...
# cbioportal-container  | mysqladmin: [Warning] Using a password on the command line interface can be insecure.
#  -psomepasswordainer  | mysqladmin ping -s -hcbioportal-database -P3306 -ucbio_user
# cbioportal-container  | Database not available yet (first time can take a few minutes to load seed database)... Attempting reconnect...
# cbioportal-container  | mysqladmin: [Warning] Using a password on the command line interface can be insecure.
#  -psomepasswordainer  | mysqladmin ping -s -hcbioportal-database -P3306 -ucbio_user
# cbioportal-container  | Database not available yet (first time can take a few minutes to load seed database)... Attempting reconnect...
# cbioportal-container  | mysqladmin: [Warning] Using a password on the command line interface can be insecure.
#  -psomepasswordainer  | mysqladmin ping -s -hcbioportal-database -P3306 -ucbio_user
# cbioportal-container  | Database not available yet (first time can take a few minutes to load seed database)... Attempting reconnect...
# cbioportal-container  | mysqladmin: [Warning] Using a password on the command line interface can be insecure.
#  -psomepasswordainer  | mysqladmin ping -s -hcbioportal-database -P3306 -ucbio_user
# cbioportal-container  | Database not available yet (first time can take a few minutes to load seed database)... Attempting reconnect...
# cbioportal-container  | mysqladmin: [Warning] Using a password on the command line interface can be insecure.
#  -psomepasswordainer  | mysqladmin ping -s -hcbioportal-database -P3306 -ucbio_user
# cbioportal-container  | Database not available yet (first time can take a few minutes to load seed database)... Attempting reconnect...
# cbioportal-container  | mysqladmin: [Warning] Using a password on the command line interface can be insecure.
#  -psomepasswordainer  | mysqladmin ping -s -hcbioportal-database -P3306 -ucbio_user
# cbioportal-container  | Database not available yet (first time can take a few minutes to load seed database)... Attempting reconnect...
# cbioportal-container  | mysqladmin: [Warning] Using a password on the command line interface can be insecure.
#  -psomepasswordainer  | mysqladmin ping -s -hcbioportal-database -P3306 -ucbio_user
# cbioportal-container  | Database not available yet (first time can take a few minutes to load seed database)... Attempting reconnect...
# cbioportal-container  | mysqladmin: [Warning] Using a password on the command line interface can be insecure.
#  -psomepasswordainer  | mysqladmin ping -s -hcbioportal-database -P3306 -ucbio_user
# cbioportal-container  | Database not available yet (first time can take a few minutes to load seed database)... Attempting reconnect...
# cbioportal-container  | mysqladmin: [Warning] Using a password on the command line interface can be insecure.
#  -psomepasswordainer  | mysqladmin ping -s -hcbioportal-database -P3306 -ucbio_user
# cbioportal-container  | Database not available yet (first time can take a few minutes to load seed database)... Attempting reconnect...
# cbioportal-container  | mysqladmin: [Warning] Using a password on the command line interface can be insecure.
#  -psomepasswordainer  | mysqladmin ping -s -hcbioportal-database -P3306 -ucbio_user
# cbioportal-container  | Database not available yet (first time can take a few minutes to load seed database)... Attempting reconnect...
# cbioportal-container  | mysqladmin: [Warning] Using a password on the command line interface can be insecure.
# cbioportal-container  | Database connection success
# cbioportal-container  | Migrating database if necessary...
# cbioportal-container  | Running statements for version: 2.13.1
# cbioportal-container  |         Executing statement: ALTER TABLE `clinical_event_data` MODIFY COLUMN `VALUE` varchar(3000) NOT NULL;
# cbioportal-container  |         Executing statement: CREATE INDEX idx_clinical_event_key ON clinical_event_data (`KEY`);
# cbioportal-container  |         Executing statement: CREATE INDEX idx_clinical_event_value ON clinical_event_data (`VALUE`);
# cbioportal-container  |         Executing statement: CREATE INDEX idx_sample_stable_id ON sample (`STABLE_ID`);
# cbioportal-container  |         Executing statement: UPDATE `info` SET `DB_SCHEMA_VERSION`="2.13.1";
# cbioportal-container  | Finished.
# cbioportal-container  | Running: /bin/sh -c java -Xms2g -Xmx4g -Dauthenticate=noauthsessionservice -Dsession.service.url=http://cbioportal-session:5000/api/sessions/my_portal/ -jar webapp-runner.jar -AmaxHttpHeaderSize=16384 -AconnectionTimeout=20000 --enable-compression /cbioportal-webapp
# cbioportal-container  | Connector attributes
# cbioportal-container  | property: maxHttpHeaderSize - 16384(OK)
# cbioportal-container  | property: connectionTimeout - 20000(OK)
# cbioportal-container  | Adding Context  for /cbioportal-webapp
# cbioportal-container  | Feb 21, 2024 2:35:17 PM org.apache.coyote.AbstractProtocol init
# cbioportal-container  | INFO: Initializing ProtocolHandler ["http-nio-8080"]
# cbioportal-container  | Feb 21, 2024 2:35:17 PM org.apache.tomcat.util.net.NioSelectorPool getSharedSelector
# cbioportal-container  | INFO: Using a shared selector for servlet write/read
# cbioportal-container  | Feb 21, 2024 2:35:17 PM org.apache.catalina.core.StandardService startInternal
# cbioportal-container  | INFO: Starting service [Tomcat]
# cbioportal-container  | Feb 21, 2024 2:35:17 PM org.apache.catalina.core.StandardEngine startInternal
# cbioportal-container  | INFO: Starting Servlet Engine: Apache Tomcat/8.5.61
# cbioportal-container  | Feb 21, 2024 2:35:18 PM org.apache.catalina.startup.SetContextPropertiesRule begin
# cbioportal-container  | WARNING: [SetContextPropertiesRule]{Context} Setting property 'antiJARLocking' to 'true' did not find a matching property.
# cbioportal-container  | Feb 21, 2024 2:35:18 PM org.apache.catalina.startup.ContextConfig getDefaultWebXmlFragment
# cbioportal-container  | INFO: No global web.xml found
# cbioportal-container  | Feb 21, 2024 2:35:24 PM org.apache.jasper.servlet.TldScanner scanJars
# cbioportal-container  | INFO: At least one JAR was scanned for TLDs yet contained no TLDs. Enable debug logging for this logger for a complete list of JARs that were scanned but no TLDs were found in them. Skipping unneeded JARs during scanning can improve startup time and JSP compilation time.
# cbioportal-container  | Feb 21, 2024 2:35:24 PM org.apache.catalina.core.ApplicationContext log
# cbioportal-container  | INFO: 1 Spring WebApplicationInitializers detected on classpath
# cbioportal-container  | 14:35:24,261 |-INFO in ch.qos.logback.classic.LoggerContext[default] - Could NOT find resource [logback-test.xml]
# cbioportal-container  | 14:35:24,261 |-INFO in ch.qos.logback.classic.LoggerContext[default] - Found resource [logback.xml] at [file:/cbioportal-webapp/WEB-INF/classes/logback.xml]
# cbioportal-container  | 14:35:24,262 |-WARN in ch.qos.logback.classic.LoggerContext[default] - Resource [logback.xml] occurs multiple times on the classpath.
# cbioportal-container  | 14:35:24,262 |-WARN in ch.qos.logback.classic.LoggerContext[default] - Resource [logback.xml] occurs at [jar:file:/cbioportal-webapp/WEB-INF/lib/core-5.4.7-dirty-SNAPSHOT.jar!/logback.xml]
# cbioportal-container  | 14:35:24,262 |-WARN in ch.qos.logback.classic.LoggerContext[default] - Resource [logback.xml] occurs at [file:/cbioportal-webapp/WEB-INF/classes/logback.xml]
# cbioportal-container  | 14:35:24,316 |-INFO in ch.qos.logback.classic.joran.action.ConfigurationAction - debug attribute not set
# cbioportal-container  | 14:35:24,317 |-INFO in ch.qos.logback.core.joran.action.AppenderAction - About to instantiate appender of type [ch.qos.logback.core.ConsoleAppender]
# cbioportal-container  | 14:35:24,325 |-INFO in ch.qos.logback.core.joran.action.AppenderAction - Naming appender as [STDOUT]
# cbioportal-container  | 14:35:24,331 |-INFO in ch.qos.logback.core.joran.action.NestedComplexPropertyIA - Assuming default type [ch.qos.logback.classic.encoder.PatternLayoutEncoder] for [encoder] property
# cbioportal-container  | 14:35:24,354 |-INFO in ch.qos.logback.core.joran.action.AppenderAction - About to instantiate appender of type [ch.qos.logback.core.FileAppender]
# cbioportal-container  | 14:35:24,357 |-INFO in ch.qos.logback.core.joran.action.AppenderAction - Naming appender as [FILE]
# cbioportal-container  | 14:35:24,357 |-INFO in ch.qos.logback.core.joran.action.NestedComplexPropertyIA - Assuming default type [ch.qos.logback.classic.encoder.PatternLayoutEncoder] for [encoder] property
# cbioportal-container  | 14:35:24,358 |-INFO in ch.qos.logback.core.FileAppender[FILE] - File property is set to [/tmp/cbioportal.log]
# cbioportal-container  | 14:35:24,359 |-INFO in ch.qos.logback.classic.joran.action.RootLoggerAction - Setting level of ROOT logger to INFO
# cbioportal-container  | 14:35:24,359 |-INFO in ch.qos.logback.core.joran.action.AppenderRefAction - Attaching appender named [STDOUT] to Logger[ROOT]
# cbioportal-container  | 14:35:24,360 |-INFO in ch.qos.logback.core.joran.action.AppenderRefAction - Attaching appender named [FILE] to Logger[ROOT]
# cbioportal-container  | 14:35:24,360 |-INFO in ch.qos.logback.classic.joran.action.ConfigurationAction - End of configuration.
# cbioportal-container  | 14:35:24,360 |-INFO in ch.qos.logback.classic.joran.JoranConfigurator@1c0da97f - Registering current configuration as safe fallback point
# cbioportal-container  |
# cbioportal-container  | Feb 21, 2024 2:35:24 PM org.apache.catalina.core.ApplicationContext log
# cbioportal-container  | INFO: Initializing Spring root WebApplicationContext
# cbioportal-container  | 14:35:24.383 [localhost-startStop-1] INFO  o.s.web.context.ContextLoader - Root WebApplicationContext: initialization started
# cbioportal-container  | 14:35:24.948 [localhost-startStop-1] INFO  o.s.s.core.SpringSecurityCoreVersion - You are running with Spring Security Core 5.3.1.RELEASE
# cbioportal-container  | 14:35:24.952 [localhost-startStop-1] INFO  o.s.s.c.SecurityNamespaceHandler - Spring Security 'config' module version is 5.3.1.RELEASE
# cbioportal-container  | 14:35:24.978 [localhost-startStop-1] INFO  o.s.s.c.h.FilterInvocationSecurityMetadataSourceParser - Creating access control expression attribute '@sessionSecurity.checkWrite(request)' for /api/session-service/**
# cbioportal-container  | 14:35:24.979 [localhost-startStop-1] INFO  o.s.s.c.h.FilterInvocationSecurityMetadataSourceParser - Creating access control expression attribute 'permitAll' for /**
# cbioportal-container  | 14:35:24.989 [localhost-startStop-1] INFO  o.s.s.c.h.HttpSecurityBeanDefinitionParser - Checking sorted filter chain: [Root bean: class [org.springframework.security.web.context.SecurityContextPersistenceFilter]; scope=; abstract=false; lazyInit=null; autowireMode=0; dependencyCheck=0; autowireCandidate=true; primary=false; factoryBeanName=null; factoryMethodName=null; initMethodName=null; destroyMethodName=null, order = 200, Root bean: class [org.springframework.security.web.context.request.async.WebAsyncManagerIntegrationFilter]; scope=; abstract=false; lazyInit=null; autowireMode=0; dependencyCheck=0; autowireCandidate=true; primary=false; factoryBeanName=null; factoryMethodName=null; initMethodName=null; destroyMethodName=null, order = 400, Root bean: class [org.springframework.security.web.header.HeaderWriterFilter]; scope=; abstract=false; lazyInit=null; autowireMode=0; dependencyCheck=0; autowireCandidate=true; primary=false; factoryBeanName=null; factoryMethodName=null; initMethodName=null; destroyMethodName=null, order = 500, Root bean: class [org.springframework.security.web.savedrequest.RequestCacheAwareFilter]; scope=; abstract=false; lazyInit=null; autowireMode=0; dependencyCheck=0; autowireCandidate=true; primary=false; factoryBeanName=null; factoryMethodName=null; initMethodName=null; destroyMethodName=null, order = 2100, Root bean: class [null]; scope=; abstract=false; lazyInit=null; autowireMode=0; dependencyCheck=0; autowireCandidate=true; primary=false; factoryBeanName=org.springframework.security.config.http.HttpConfigurationBuilder$SecurityContextHolderAwareRequestFilterBeanFactory#0; factoryMethodName=getBean; initMethodName=null; destroyMethodName=null, order = 2200, Root bean: class [org.springframework.security.web.authentication.AnonymousAuthenticationFilter]; scope=; abstract=false; lazyInit=null; autowireMode=0; dependencyCheck=0; autowireCandidate=true; primary=false; factoryBeanName=null; factoryMethodName=null; initMethodName=null; destroyMethodName=null, order = 2500, Root bean: class [org.springframework.security.web.session.SessionManagementFilter]; scope=; abstract=false; lazyInit=null; autowireMode=0; dependencyCheck=0; autowireCandidate=true; primary=false; factoryBeanName=null; factoryMethodName=null; initMethodName=null; destroyMethodName=null, order = 2700, Root bean: class [org.springframework.security.web.access.ExceptionTranslationFilter]; scope=; abstract=false; lazyInit=null; autowireMode=0; dependencyCheck=0; autowireCandidate=true; primary=false; factoryBeanName=null; factoryMethodName=null; initMethodName=null; destroyMethodName=null, order = 2800, <org.springframework.security.web.access.intercept.FilterSecurityInterceptor#0>, order = 2900]
# cbioportal-container  | 14:35:24.991 [localhost-startStop-1] WARN  o.s.b.f.p.FailFastProblemReporter - Configuration problem: Overriding globally registered AuthenticationManager
# cbioportal-container  | Offending resource: class path resource [applicationContext-security.xml]
# cbioportal-container  | 14:35:25.487 [localhost-startStop-1] INFO  o.m.c.portal.util.GlobalProperties - Attempting to read properties file: /cbioportal/portal.properties
# cbioportal-container  | 14:35:25.487 [localhost-startStop-1] INFO  o.m.c.portal.util.GlobalProperties - Successfully read properties file
# cbioportal-container  | 14:35:25.488 [localhost-startStop-1] INFO  o.m.c.portal.util.GlobalProperties - Attempting to read properties file: /cbioportal/maven.properties
# cbioportal-container  | 14:35:25.488 [localhost-startStop-1] INFO  o.m.c.portal.util.GlobalProperties - Failed to read properties file: /cbioportal/maven.properties
# cbioportal-container  | 14:35:25.489 [localhost-startStop-1] INFO  o.m.c.portal.util.GlobalProperties - Attempting to read properties file from classpath
# cbioportal-container  | 14:35:25.489 [localhost-startStop-1] INFO  o.m.c.portal.util.GlobalProperties - Successfully read properties file
# cbioportal-container  | 14:35:25.496 [localhost-startStop-1] INFO  o.s.c.s.PostProcessorRegistrationDelegate$BeanPostProcessorChecker - Bean 'businessDataSource' of type [org.mskcc.cbio.portal.dao.JdbcDataSource] is not eligible for getting processed by all BeanPostProcessors (for example: not eligible for auto-proxying)
# cbioportal-container  | 14:35:25.500 [localhost-startStop-1] INFO  o.s.c.s.PostProcessorRegistrationDelegate$BeanPostProcessorChecker - Bean 'org.springframework.beans.factory.config.MethodInvokingFactoryBean#0' of type [org.springframework.beans.factory.config.MethodInvokingFactoryBean] is not eligible for getting processed by all BeanPostProcessors (for example: not eligible for auto-proxying)
# cbioportal-container  | 14:35:25.533 [localhost-startStop-1] INFO  o.s.c.s.PostProcessorRegistrationDelegate$BeanPostProcessorChecker - Bean 'appConfig' of type [org.cbioportal.service.util.AppConfig$$EnhancerBySpringCGLIB$$b21e0203] is not eligible for getting processed by all BeanPostProcessors (for example: not eligible for auto-proxying)
# cbioportal-container  | 14:35:26.711 [localhost-startStop-1] INFO  o.c.persistence.CacheEnabledConfig - Cache is enabled: false
# cbioportal-container  | 14:35:28.244 [localhost-startStop-1] INFO  o.s.s.web.DefaultSecurityFilterChain - Creating filter chain: any request, [org.springframework.security.web.context.SecurityContextPersistenceFilter@23fa810a, org.springframework.security.web.context.request.async.WebAsyncManagerIntegrationFilter@150b6db1, org.springframework.security.web.header.HeaderWriterFilter@4c75655d, org.springframework.security.web.savedrequest.RequestCacheAwareFilter@3e6d5bbe, org.springframework.security.web.servletapi.SecurityContextHolderAwareRequestFilter@49e86fa8, org.springframework.security.web.authentication.AnonymousAuthenticationFilter@740f5c1, org.springframework.security.web.session.SessionManagementFilter@607827f9, org.springframework.security.web.access.ExceptionTranslationFilter@341c30e3, org.springframework.security.web.access.intercept.FilterSecurityInterceptor@3e039ce7]
# cbioportal-container  | 14:35:28.298 [localhost-startStop-1] INFO  o.s.web.context.ContextLoader - Root WebApplicationContext initialized in 3912 ms
# cbioportal-container  |
# cbioportal-container  | ----------------------------------------------------------------------------------------------------------------
# cbioportal-container  | -- You are connecting to the OncoKB public instance which does not include any therapeutic information.
# cbioportal-container  | -- Please consider obtaining a license to support future OncoKB development by following https://docs.cbioportal.org/2.4-integration-with-other-webservices/oncokb-data-access.
# cbioportal-container  | -- Thank you.
# cbioportal-container  | ----------------------------------------------------------------------------------------------------------------
# cbioportal-container  |
# cbioportal-container  | 14:35:28.471 [localhost-startStop-1] INFO  o.s.web.servlet.DispatcherServlet - Initializing Servlet 'api'
# cbioportal-container  | Feb 21, 2024 2:35:28 PM org.apache.catalina.core.ApplicationContext log
# cbioportal-container  | INFO: Initializing Spring DispatcherServlet 'api'
# cbioportal-container  | 14:35:29.134 [localhost-startStop-1] INFO  o.h.validator.internal.util.Version - HV000001: Hibernate Validator 5.3.4.Final
# cbioportal-container  | 14:35:29.232 [localhost-startStop-1] INFO  o.s.c.s.PostProcessorRegistrationDelegate$BeanPostProcessorChecker - Bean 'org.springframework.validation.beanvalidation.LocalValidatorFactoryBean#6f14c7ab' of type [org.springframework.validation.beanvalidation.LocalValidatorFactoryBean] is not eligible for getting processed by all BeanPostProcessors (for example: not eligible for auto-proxying)
# cbioportal-container  | 14:35:29.248 [localhost-startStop-1] INFO  o.s.c.s.PostProcessorRegistrationDelegate$BeanPostProcessorChecker - Bean 'appConfig' of type [org.cbioportal.service.util.AppConfig$$EnhancerBySpringCGLIB$$b21e0203] is not eligible for getting processed by all BeanPostProcessors (for example: not eligible for auto-proxying)
# cbioportal-container  | 14:35:30.750 [localhost-startStop-1] INFO  o.c.persistence.CacheEnabledConfig - Cache is enabled: false
# cbioportal-container  | 14:35:31.084 [localhost-startStop-1] INFO  o.s.b.f.a.AutowiredAnnotationBeanPostProcessor - Autowired annotation should only be used on methods with parameters: private void org.cbioportal.web.DataAccessTokenController.initializeUsersWhoCannotUseTokenSet()
# cbioportal-container  | 14:35:33.307 [localhost-startStop-1] INFO  o.s.web.servlet.DispatcherServlet - Completed initialization in 4835 ms
# cbioportal-container  | Feb 21, 2024 2:35:33 PM org.apache.catalina.core.ApplicationContext log
# cbioportal-container  | INFO: Initializing Spring DispatcherServlet 'url_shortener'
# cbioportal-container  | 14:35:33.330 [localhost-startStop-1] INFO  o.s.web.servlet.DispatcherServlet - Initializing Servlet 'url_shortener'
# cbioportal-container  | 14:35:33.448 [localhost-startStop-1] INFO  o.s.web.servlet.DispatcherServlet - Completed initialization in 118 ms
# cbioportal-container  | Feb 21, 2024 2:35:33 PM org.apache.catalina.core.ApplicationContext log
# cbioportal-container  | INFO: Initializing Spring DispatcherServlet 'proxy'
# cbioportal-container  | 14:35:33.449 [localhost-startStop-1] INFO  o.s.web.servlet.DispatcherServlet - Initializing Servlet 'proxy'
# cbioportal-container  | 14:35:33.530 [localhost-startStop-1] INFO  o.s.web.servlet.DispatcherServlet - Completed initialization in 81 ms
# cbioportal-container  | Feb 21, 2024 2:35:37 PM org.apache.coyote.AbstractProtocol start
# cbioportal-container  | INFO: Starting ProtocolHandler ["http-nio-8080"]
```

This log indicates that the deployment is successful, the containers are running, and the portal is available at http://localhost:8080/.

## Configuration

### Importing Studies

#### From datahub

After having followed the above steps, an example study, Low-Grade Gliomas (UCSF, Science 2014) loaded locally. Now let's try to import another study from cBioPortal [datahub](https://github.com/cBioPortal/datahub/tree/master/public/), for example, we import TCGA-BLCA . It is available under the 'blca_tcga' name in the datahub. Navigating to that project there is a `meta_study.txt` file that needs to be checked to make sure that the name of the project  is identical to the study ID defined, there. The details are below

```bash
type_of_cancer: blca
cancer_study_identifier: blca_tcga
name: Bladder Urothelial Carcinoma (TCGA, Firehose Legacy)
short_name: Bladder (TCGA)
description: TCGA Bladder Urothelial Carcinoma. Source data from <A HREF="http://gdac.broadinstitute.org/runs/stddata__2016_01_28/data/BLCA/20160128/">GDAC Firehose</A>. Previously known as TCGA Provisional.
groups: PUBLIC
```
Then:

1) Downloading the study: From the root of the `cbioportal-docker-compose` folder run `DATAHUB_STUDIES=blca_tcga ./study/init.sh`. The study should now be downloaded in ./study/, if not check wd.
   
2) Importing the study: To import the study by running `docker-compose exec cbioportal metaImport.py -u http://cbioportal:8080 -s study/blca_tcga/ -o`.

3) Restarting the cbioportal instance:  `docker-compose restart cbioportal`  and see if the new study shows up on http://localhost:8080


```bash
docker compose run cbioportal metaImport.py -u http://cbioportal:8080 -s study/lgg_ucsf_2014/ -o
```

#### Importing local study
To load a study into cBioPortal, it requires a directory containing data files, each accompanied by a corresponding meta file. 
File Naming Rules:
**Meta files**: Can be named anything as long as they start or end with the word "meta" (e.g., meta_test.txt, test.meta.txt, meta.test.txt).
**Data files**: Can be named anything and are referenced in the corresponding meta file using a property called `data_filename`. 

Mandatory Files:

`meta_study.txt`: Meta information about the study.
`meta_clinical.txt`: Meta file for clinical data.
Respective clinical data file (e.g., data_clinical.txt).

Optional Files:
`meta_cancer_type.txt`: Meta file for cancer type information, which becomes mandatory if the study refers to a cancer type not already existing in the database.
Additional meta files (e.g., for expression, mutations, CNA, etc.).



To import local studies, we use [cbpManager](https://github.com/arsenij-ust/cbpManager?tab=readme-ov-file). To get this installed and configured, `remotes::install_github("arsenij-ust/cbpManager")`  and `cbpManager::setupConda_cbpManager()` for setting the Python environment. Then it needs to be provided with a path to the study folder where cBioPortal looks for studies to import them. To get the path to the study directly in WSL, running `explorer.exe .` in the WSL terminal will open a folder whose address is what the cbpManager needs to be supplied with. 

So to launch and work with cbpManager in R :

```R
# set the path to study dir
cbpManager::cbpManager(
  studyDir="\\\\wsl.localhost\\Ubuntu-18.04\\home\\usr\\cbioportal-docker-compose\\study", 
  logDir="\\\\wsl.localhost\\Ubuntu-18.04\\home\\usr\\cbioportal-docker-compose\\study"
)
```
The above launches the Shiny application and we can start working on our study's files and prepare them for import. The process of importing patient and sample data is not straightforward and require manual processing. As an alternative we can work using a text editor to create the needed files. 

**data_clinical_patient.txt**:
The first four rows of the clinical data file contain tab-delimited metadata about the clinical attributes. These rows have to start with a '#' symbol. Each of these four rows contain different type of information regarding each of the attributes that are defined in the fifth row:

Row 1: The attribute Display Names: The display name for each clinical attribute

Row 2: The attribute Descriptions: Long(er) description of each clinical attribute

Row 3: The attribute Datatype: The datatype of each clinical attribute (must be one of: STRING, NUMBER, BOOLEAN)

Row 4: The attribute Priority: A number which indicates the importance of each attribute. In the future, higher priority attributes will appear in more prominent places than lower-priority ones on relevant pages (such as the Study View). A higher number indicates a higher priority.


The file MUST contain a patient identifier column (PATIENT_ID), which serves as a unique patient ID.
Required columns for survival plots:

OS_STATUS: Overall patient survival status, with values 1:DECEASED or 0:LIVING.

OS_MONTHS: Overall survival in months since the initial diagnosis.

DFS_STATUS: Disease-free status since initial treatment, with values 0:DiseaseFree or 1:Recurred/Progressed.

DFS_MONTHS: Disease-free months since initial treatment.



| #Patient Identifier                        	| Study Identifier 	| Sub-study Identifier 	| Best Change                                          	| Best Response             	| OS Event                  	| OS Time              	| PFS Event                 	| PFS Time                  	|
|-------------------------------------------	|------------------	|----------------------	|------------------------------------------------------	|---------------------------	|---------------------------	|----------------------	|---------------------------	|---------------------------	|
| #Identifier to uniquely specify a patient. 	| Trial Identifier 	| Sub-study identifier 	| Specifying  best change in tumor   size (percentage) 	| Specifying  best response 	| Indicating death or alive 	| Overal survival time 	| Specifying type of sample 	| Specifying type of sample 	|
| #STRING                                    	| STRING           	| STRING               	| NUMBER                                               	| STRING                    	| STRING                    	| NUMBER               	| STRING                    	| NUMBER                    	|
|# 1                                         	| 0                	| 1                    	| 1                                                    	| 1                         	| 1                         	| 1                    	| 1                         	| 1                         	|
| PATIENT_ID                                	| STUDY_ID         	| SUBSTUDY_ID          	| BESTCHANGE                                           	| BESTRESP                  	| OS_STATUS                 	| OS_MONTH             	| DFS_STATUS                	| DFS_MONTH                 	|
| XXX                                 	| XXX             	| XXX                	| XXX                                                 	| PD                        	| 0:LIVING                  	| 3.712526             	| 1:Recurred/Progressed     	| 1.87269                   	|
|                                           	|                  	|                      	|                                                      	|                           	|                           	|                      	|                           	|                           	

Custom attributes can be added as well, following formatting guidelines.


#### Importing 

This needs a python script called, "" and it is available in cBioPortal Core repo:

```bash
git clone https://github.com/cBioPortal/cbioportal-core.git
# to locate the script
find . -name "metaImport*"
#./cbioportal-core/src/main/resources/scripts/importer/metaImport.py
# to run the script
pip3 install -r cbioportal-core/requirements.txt
# see help
./cbioportal-core/src/main/resources/scripts/importer/metaImport.py -h

# set portal home variable to where portal.properties is located :
export PORTAL_HOME=/home/usr/cbioportal-docker-compose/config
# Now lets import the created study
cd /home/usr/cbioportal-docker-compose
docker-compose exec cbioportal metaImport.py -u http://cbioportal:8080 -s ./study/i240 -o
```
The above prints a long message with warnings, after that restart docker to see the study 
