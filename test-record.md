Archivematica Transfers Test Record
===================================

Flat Directory Tests
--------------------

### 01: [Single empty file, no metadata](flat/simple-tests/empty-file)
Ingested OK and [METS pointer record](results/flat/simple-tests/empty-file/pointer-mets.xml) OK. [Response error](results/flat/simple-tests/empty-file/response-error.xml) when attempt to download the SIP.

### 02: [Single text file, no metadata](flat/simple-tests/single-text-file)
Ingested OK and [METS pointer record](results/flat/simple-tests/single-text-file/pointer-mets.xml) OK.
Similar response error to test 01 so now testing by sending SIP straight to backlog.

### 03: [Single text file with objects directory](flat/simple-tests/with-objects)
Now examing SIP structure in backlog with checkum fields See deep hash alg bug. Same SIP structure as flat file.

### 03a: [Single text file with objects, bad checksum](flat/simple-tests/with-objects-bad-checksum)
Confirmed suspicions that checksum validation isn't working, most likely due to failure to install the deep hash algorithms. This seems to only affect flat, e.g. non-bagged transfers

### 04: Single text file, production workflow automation file
First reset defaults and tested without any `processingMCP.xml` file to confirm all decision steps had returned. Then added the `processingMCP.xml` file extracted from my straight to backlog automated flow. Test successful, making it possible to provide custom automtion files to shortcut the transfer and ingest steps some.

### 05: Single text file with Dublin Core identifier and title
Added simple `metadata.csv` file to metadata folder, e.g.:
```
"filename","dc.identifier","dc.title","dc.description","dc.date","dc.format"
"objects/test-file.txt","1","Test description simple ASCII only.","11/25/2018","text/plain"
```
This was unsucccessful so removed quote chars and retried, e.g. :
```
filename,dc.identifier,dc.title,dc.description,dc.date,dc.format
objects/test-file.txt,1,Test description simple ASCII only.,11/25/2018,text/plain
```
Bugs?
-----

### Lack of deep hash algorithms
This means that hash validation is broken for transfers, bad (wrong), and even non-legal MD5s don't cause checksum validation to fail. e.g.
```
d8e8fca2dc0f894fd7cb4cb0031ba249  objects/text-file.txt
e8r8fca2dc0f894fdedb4eb0031baf78  objects/text-file.txt
```

When examining output of verify checksums step.
e.g. `STDERR`
```
/usr/lib/archivematica/MCPClient/clientScripts/archivematicaCheckMD5NoGUI.sh: line 39: sha1deep: command not found
/usr/lib/archivematica/MCPClient/clientScripts/archivematicaCheckMD5NoGUI.sh: line 41: sha1deep: command not found
/usr/lib/archivematica/MCPClient/clientScripts/verifyMD5.sh: line 54: sha1deep: command not found
```
or
```
/usr/lib/archivematica/MCPClient/clientScripts/archivematicaCheckMD5NoGUI.sh: line 39: sha256deep: command not found
/usr/lib/archivematica/MCPClient/clientScripts/archivematicaCheckMD5NoGUI.sh: line 41: sha256deep: command not found
/usr/lib/archivematica/MCPClient/clientScripts/verifyMD5.sh: line 54: sha1deep: command not found
```
or
```
/usr/lib/archivematica/MCPClient/clientScripts/archivematicaCheckMD5NoGUI.sh: line 39: md5deep: command not found
/usr/lib/archivematica/MCPClient/clientScripts/archivematicaCheckMD5NoGUI.sh: line 41: md5deep: command not found
/usr/lib/archivematica/MCPClient/clientScripts/verifyMD5.sh: line 45: md5deep: command not found
```
