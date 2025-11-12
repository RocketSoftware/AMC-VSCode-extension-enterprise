# Verifying the Digital Signature of a Rocket Software Inc. Extension for Visual Studio Code


# Digital Signatures
The Rocket Software, Inc. extensions for Visual Studio Code listed below use [PGP](https://en.wikipedia.org/wiki/Pretty_Good_Privacy) digital signatures to guarantee that the packages you are installing were created by Rocket Software, Inc. This readme guides you through the process of verifying these extensions on Windows and on Linux.

* [**Rocket Analyzer extension**](https://marketplace.visualstudio.com/items?itemName=RocketSoftware.rocket-analyzer)
* [**Rocket Enterprise extension**](https://marketplace.visualstudio.com/items?itemName=RocketSoftware.rocket-enterprise)
* [**Rocket COBOL extension**](https://marketplace.visualstudio.com/items?itemName=RocketSoftware.rocket-cobol)
* [**Rocket Learn COBOL extension**](https://marketplace.visualstudio.com/items?itemName=RocketSoftware.rocket-learncobol)
* [**Rocket JVM COBOL**](https://marketplace.visualstudio.com/items?itemName=RocketSoftware.rocket-jvmcobol)



## Software Requirements
There are a number of open-source tools available for both Windows and Linux platforms which you can use to verify the marketplace extensions before you install them. This readmes uses [Gpg4win for Windows](https://www.gpg4win.org/) and [gnupgp for Linux](https://gnupg.org/).

# Verifying the Signature
This readme includes example instructions for both Windows (using Gpg4win) and Red Hat Linux (using gnupgp).

1. Install the PGP tools:
* On Windows - download and install [Gpg4win for Windows](https://www.gpg4win.org/)
* On Linux, install gnupg using yum from a terminal: 
  ```
    yum install –y gnupg
  ```
  This adds the PGP tools to the PATH environment variable on your machine.
  On Windows, however, you might need to add the Gpg4win installation folder manually to the PATH as follows:
  ```
    set PATH=%PATH%;C:\Program Files (x86)\Gpg4win\bin
  ```

2. Download the required extension file (```.vsix```) from the Visual Studio Marketplace:
* [**Rocket Enterprise extension**](https://marketplace.visualstudio.com/items?itemName=RocketSoftware.rocket-enterprise&ssr=false#version-history)
* [**Rocket COBOL extension**](https://marketplace.visualstudio.com/items?itemName=RocketSoftware.rocket-cobol&ssr=false#version-history)
* [**Rocket Learn COBOL extension**](https://marketplace.visualstudio.com/items?itemName=RocketSoftware.rocket-learncobol&ssr=false#version-history)
* [**Rocket JVM COBOL**](https://marketplace.visualstudio.com/items?itemName=RocketSoftware.rocket-jvmcobol&ssr=false#version-history)

  **Note:** We recommend that you always download and install the latest version of the extensions.

3. Select the release from this GitHub repository which matches the version of the extension(s) you have downloaded. Download the signature (```.asc```) file for the selected release. 

4. Extract the public key from the ```.vsix``` package. The ```.vsix``` package can be treated as a ZIP archive so you can use any archive utility such az 7-zip on Windows:

* On Windows:
  ```
    "C:\Program Files\7-Zip\7z.exe" e RocketSoftware.rocket-cobol-2.0.0.vsix extension\Rocket.package-sign.pub
  ```
* On Linux:
  ```
    unzip RocketSoftware.rocket-cobol-2.0.0.vsix extension/Rocket.package-sign.pub
  ```

5. To view the fingerprint of the exported public key, run the following command:
* On Windows:
  ```
    gpg --import --import-options show-only --dry-run Rocket.package-sign.pub
  ```
* On Linux:
  ```
    gpg2 --import --import-options show-only --dry-run extension/Rocket.package-sign.pub
  ```
  The expected output is in a format similar to the following where the asterisks indicate the fingerprint value:
  ```
    pub   rsa4096 2024-05-14 [SC]
      4BCACD85F7618C855216DD8C56A5D1B5263069BE
    uid                      Rocket-package-sign (Rocket Software, Inc.  Package signing - 20240514) <Rocket-package-sign@rocketsoftware.com>
  ```
  **Important:** To verify that the public key is correct, the fingerprint must match the following value:

   **Key fingerprint: 4BCA CD85 F761 8C85 5216 DD8C 56A5 D1B5 2630 69BE**
   
   **You should only proceed with importing the public key into the local keystore if the key fingerprint matches this value.**

6. To import the public key into the local keystore:
* On Windows:
  ```
    gpg --import Rocket.package-sign.pub
  ```
* Linux:
  ```
    gpg2 --import extension/Rocket.package-sign.pub
  ```

7. Once imported, you need to sign the key in your keystore with a private key. 

    This is required so that you can provide information to your local system that the signing certificate imported in this step has passed all the checks performed in the previous steps. If you do not provide this information, the system reports that the imported certificate is not trusted when you try to verify the ```.vsix``` package.

    You need an existing private key to sign the key. Follow the prompts to sign the public key:
* On Windows:
  ```
    gpg --lsign-key "4BCA CD85 F761 8C85 5216 DD8C 56A5 D1B5 2630 69BE"
  ```
* On Linux:
  ```
    gpg2 --lsign-key "4BCA CD85 F761 8C85 5216 DD8C 56A5 D1B5 2630 69BE"
  ```

  **Important:** If you receive the following output, it indicates that no private key exists. In this case you need to create a new private key and retry this step:

  ```
    gpg: no default secret key: No secret key 
    Key has not been changed, so no save is needed. 
  ```

8. To verify the ```.vsix``` package using the signature file:
* On Windows:
  ```
    gpg --verify rsvsanalyzer-2.0.8.vsix.asc rsvsanalyzer-2.0.8.vsix
    gpg --verify rsvscode-2.0.12.vsix.asc rsvscode-2.0.12.vsix
    gpg --verify rsvsenterprise-2.0.12.vsix.asc rsvsenterprise-2.0.12.vsix
    gpg --verify rslearnCOBOL-2.0.10.vsix.asc rslearnCOBOL-2.0.10.vsix
  ```
* On Linux:
  ```
    gpg2 --verify rsvsanalyzer-2.0.8.vsix.asc rsvsanalyzer-2.0.8.vsix
    gpg2 --verify rsvscode-2.0.12.vsix.asc rsvscode-2.0.12.vsix
    gpg2 --verify rsvsenterprise-2.0.12.vsix.asc rsvsenterprise-2.0.12.vsix
    gpg2 --verify rslearnCOBOL-2.0.10.vsix.asc rslearnCOBOL-2.0.10.vsix

9. If the verification process reports an error, check the **Common Issues** below.

## Common Issues
**Issue:**
> Can't check signature: No public key

**Resolution:**
 Double-check that the public key has been imported into the local system.

**Issue:**
> Invalid signature

**Resolution:**
- Double-check that the correct signature version is being used.
- Double-check the checksum of the downloaded .vsix package.

If the problem persists, visit the [Community Forum](https://community.rocketsoftware.com/rocket-visual-cobol-28) or, if you have a maintenance contract for Rocket&#174; Visual COBOL or Rocket&#174;Enterprise Developer, contact [Rocket Software Support](https://www.rocketsoftware.com/en-us/support).
