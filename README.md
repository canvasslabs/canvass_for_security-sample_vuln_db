# Sample Vulnerability Database

This repository contains instructions on how to create a vulnberability database that works with our dependency checker. We provided a sample data set that contains some CVE and our vulnerability data in this repository. By following instructions below, you can set up your own vulnerability database. All the instructions below assume that you are on *nix system.


## How to get started

The vulnerability database uses Apache Solr. If you have no prior experience in using Solr, this guide may be helpful.

http://lucene.apache.org/solr/guide/8_1/solr-tutorial.html


### Overview

1. Clone sample_vuln_db repository
2. Download Apache Solr
3. Enable Basic Authentication on Solr
4. Run Solr
5. Create two collections (cores) on Apache Solr
6. Insert sample data set into Solr


#### 1. Clone sample_vuln_db repository

```
git clone https://github.com/canvasslabs/sample_vuln_db.git
```

#### 2. Download Apache Solr

Apache Solr official releases are available here:
https://lucene.apache.org/solr/downloads.html

Download Solr and decompress it.

```
cd sample_vuln_db
wget "https://archive.apache.org/dist/lucene/solr/8.1.1/solr-8.1.1.zip"
unzip solr-8.1.1.zip -d solr
```

#### 3. Enable Basic Authentication on Solr
Dependency checker expects a Solr instance running as a vulnerability database to have basic authentication. Following steps below will enable basic authentication and set username to "solr" and password to "SolrRocks". Please do change the username and password to your own.

Detailed information on Solr's basic authentication is available here.
https://lucene.apache.org/solr/guide/8_1/basic-authentication-plugin.html#enable-basic-authentication

Change directory to where Solr is unzipped.
```
cd solr/solr-8.1.1
```

Uncomment 'SOLR_AUTH_TYPE="basic"' line in solr.in.sh file
```
sed -i 's/#SOLR_AUTH_TYPE=\"basic\"/SOLR_AUTH_TYPE=\"basic\"/' ./bin/solr.in.sh
```

Uncomment 'SOLR_AUTHENTICATION_OPTS=-Dbasicauth=solr:SolrRocks"' line in solr.in.sh file
```
sed -i 's/#SOLR_AUTHENTICATION_OPTS=\"-Dbasicauth=solr:SolrRocks\"/SOLR_AUTHENTICATION_OPTS=\"-Dbasicauth=solr:SolrRocks\"/' ./bin/solr.in.sh``
```

Copy security.json file in "sample_vuln_db/solr_configs" folder to "solr-8.1.1/server/solr/"

```
cp ../../solr_configs/security.json server/solr
```


#### 4. Run Solr
Change directory to sample_vuln_db/solr/solr-8.1.1 and start Solr
```
./bin/solr start
```


#### 5. Create two collections (cores) on Apache Solr

We will create two collections (cores) on running Solr instance. One core will contain vulnerability data and the other core will contain CVE data. You can use Solr core configuration files that we provided. They are located at "sample_vuln_db/solr_configs/cve" and "sample_vuln_db?solr_configs/vuln".

```
./bin/solr create -c pkgname_vrange_cve_merged -d ../../solr_configs/vuln
./bin/solr create -c cves -d ../../solr_configs/cve
```

#### 6. Insert sample data set into Solr

We are going to insert sample CVE and vulnerability data into the cores that we just created.

```
// Enter your own username and password after -user flag.
./bin/post -user solr:SolrRocks -c pkgname_vrange_cve_merged ../../sample_data/vuln_sample_data.json
./bin/post -user solr:SolrRocks -c cves ../../sample_data/cve_sample_data.json
```


#### Making sure the vulnerability database is up and running

Open your brower and go to "http://localhost:8983". You should be able to access the running Solr instance. Enter your username and password to login. Under "core selector" pull down menu, you should see "CVE" and "pkgname_vrange_cve_merged" collections and under each collection, you should see inserted CVE and vulnerability data.
