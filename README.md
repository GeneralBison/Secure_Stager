# Secure Stager

This project demonstrates an x64 position-independent stager that verifies the stage it downloads prior to executing it. This offers a safeguard against man-in-the-middle attacks for those who are concerned about such things. Final stager size ~4100 bytes.

## Technical Implementation

The stager generated by this tool was built using the [Stardust](https://github.com/Cracked5pider/Stardust) framework. Using user input, a header file (Config.h) is produced and compiled into the stager by secure_stager.py. 

The validity of the retrieved stage is verified using its MD5 checksum. During the generation process the hash of the payload stage is determined and then used to XOR encrypt it. This hash is then compiled into the stager. At runtime the stager downloads the stage from the target URL (provided during generation), XOR decrypts it using the original MD5 hash, and then retrieves the MD5 hash of the decrypted stage in order to compare it against the original. If they match, the stage is executed.

## Cobalt Strike Integration

This tool can be integrated into Cobalt Strike through the use of the secure_stager.cna Aggressor script. After loading it in the script manager, the `Secure Stager` menu item can be found under `Payloads`. After selecting a listener and specifying the URL that the payload will be available at, the Aggressor script will generate a raw x64 stageless beacon and save it to disk before calling secure_stager.py to generate the stager. 

Secure stager functionality within Cobalt Strike is particularly attractive because Cobalt Strike's built-in stager functionality neither verifies the retrieved stage nor fetches a stage that reflects user modifications to the sleepmask or UDRL. This toolkit both ensures the validity of the stage and that the fetched stage will contain user-modified sleepmask/UDRL/etc.

## Usage

Command Line Syntax: `./secure_stager.py </path/to/raw/file> <HTTPS url that stage will be hosted at>`.

Example: `./secure_stager.py /home/kali/beacon_x64.bin https://www.myhostingdomain.com/aboutus`.

After the python script completes, host the produced encrypted stage at the URL that you provided to the tool. Next include the generated stager in your favorite dropper/shellcode runner.

## Notes

There are no AV/EDR evasion methods built into the stager; that is the job of your shellcode runner. After the stage has been downloaded and verified it will be executed in the same thread as the stager via function pointer.

## Further work
1. Make stager proxy-aware.  
2. Add customization options for request headers.

## Credits
1. [@C5pider](https://x.com/C5pider) for Stardust
2. Various StackOverflow posts