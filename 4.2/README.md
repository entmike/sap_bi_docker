# Disclaimer

This is not supported by SAP.  Refer to SAP Note [2496083](https://apps.support.sap.com/sap/support/knowledge/preview/en/2496083) for SAP's statement on the matter.  I take no responsibility if you get this working or not, nor would I do this in production but chances are if you are actually considering this, you're probably a developer and don't care. 👀

# Prerequisites

- BusinessObjects 4.2 Linux Server installation files in your Docker build context folder.  Move folder `BusinessObjectsServer_lnx` to the base of your build folder.
- Modify `BusinessObjectsServer_lnx/dunit/product.businessobjects64-4.0-core-32/actions/patchlevel.sh` similar to below:

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

# Other Comments

- This will build a ridiculously huge 36GB image.  If somebody better at Docker builds than me can think of a way to reduce the footprint, reach out to me!
