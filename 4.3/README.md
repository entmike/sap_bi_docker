# Disclaimer

This is not supported by SAP.  Refer to SAP Note [2496083](https://apps.support.sap.com/sap/support/knowledge/preview/en/2496083) for SAP's statement on the matter.  I take no responsibility if you get this working or not, nor would I do this in production but chances are if you are actually considering this, you're probably a developer and don't care. 👀

# Prerequisites

- BusinessObjects 4.3 Linux Server installation files in your Docker build context folder.  Move folder `install` to the base of your build folder.
- Modify `install/dunit/product.businessobjects64-4.0-core-32/actions/patchlevel.sh` similar to below:

  - Before:
    ```
    ...
    if [ -f "$cddir/loadStrings.sh" ]; then
            . "$cddir/loadStrings.sh"
    fi
    # make sure we're running on Solaris or AIX
    ...
    ```
  - After:
    ```
    ...
    if [ -f "$cddir/loadStrings.sh" ]; then
            . "$cddir/loadStrings.sh"
    fi
    # Change here:  Exit with a code of 0
    exit 0
    # make sure we're running on Solaris or AIX
    ...
  ```

# Usage

- `license-key` is your BusinessObjects License Key
- `password` is your Cluster Key, DB, and Administrator Password

# Example Build Syntax

`docker build --build-arg license_key=0000-0000000-0000000-0000000-00 --build-arg password=YourPassword -t yourrepo/imagename .`

# Example Run Syntax

`docker run --name bi43 -p 8080:8080 -p 6405:6405 -p 6400:6400 yourrepo/imagename`

Console Log:
```
SQL Anywhere Start Server In Background Utility Version 17.0.9.4935
Starting all servers...

Starting localhost...
```

You should now be able to browse to your docker daemon's host on port 8080 (Example http://dockerhost:8080/BOE/CMC)

## Initial Config tweaks:

- Log into CMC
- Go to Applications
- Change your RESTful Web Service to the docker hostname.  It will be something like `http://35c99b486bc4:8080/biprws` initially.  Change it to `http://dockerhost:8080/biprws`
- Save and Close
- While still under Applications, alse change your Web Service to the the docker hostname.  It will be something like `http://35c99b486bc4:8080/dswsbobje` initially.  Change it to `http://dockerhost:8080/dswsbobje`.

## Logging in:

- Navigate to `http://dockerhost:8080/BOE/BI` and log in as `Administrator` and the password you used during your Docker build.
- You should see the BI LaunchPad come up.  You are done!

# Other Comments

- This will build a ridiculously huge 36GB image.  If somebody better at Docker builds than me can think of a way to reduce the footprint, reach out to me!
