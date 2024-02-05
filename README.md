# rhel-kickstart

## Kickstarts for RHEL 8
- **kickstart_8_minimal.txt**
	- Tested on the **RHEL 8.9 Offline DVD ISO image** (`rhel-8.9-x86_64-dvd.iso`)
	- Hardware Allocations:
		- 2 CPU cores
		- 2 GB RAM
		- 10 GB disk
	- Default Password (`DEFAULTDEFAULTDEFAULT`) is generated for:
		- 'admin' user account
			- Change with:
			```
			passwd
			```
		- LUKS disk encryption password
			- Change with:
			```
			# Find the Partition (sda<?>) that has FSTYPE=crypto_LUKS.  (default: /dev/sda3)
			lsblk -f
			
			# Find which Keyslot is currently being used.  (default: Keyslot #0)
			sudo cryptsetup luksDump /dev/sda3
			
			# Test to ensure that this Keyslot is unlocked with the Default Password.
			# It will display "Key slot <?> unlocked"
			sudo cryptsetup --verbose open --test-passphrase /dev/sda3
			
			# Change the password for Keyslot #0
			sudo cryptsetup luksChangeKey /dev/sda3 -S 0
			```
		- Grub bootloader password (default Grub username: root)
			- Change with:
			```
			sudo grub2-setpassword
			```


## USAGE
- When you boot the RHEL Offline DVD ISO image, use your arrow keys to stop at the Grub Bootloader
- Use your arrow keys to select the `Red Hat Enterprise Linux (...) 8.9 (Oopta)` entry
	- **NOT** the `(0-rescue-...)` entry
- Press "e" on your keyboard
- Use your arrow keys to move to where "quiet" is, and delete the word "quiet"
- To use the Kickstart file hosted on an HTTP server, append the text to the same line where "quiet" was
	- (if network is configured for DHCP):
	```
	fips=1 inst.ks=http://IP:PORT/ks_8_minimal.txt"
	```
	- (if network if configured for STATIC IP):
	```
	# Static IP is in this format: ip=<STATIC_IP>::<GATEWAY>:<SUBNET_MASK>
	fips=1 inst.ks=http://IP:PORT/ks_8_minimal.txt  ip=192.168.1.5::192.168.1.1:255.255.255.0
	```
	
	
## IMPORTANT
- Kickstart files must have Unix Line Endings (LF), **NOT** Windows Line Endings (CRLF)
	- On Windows, if using Notepad++, go to `Edit > EOL Conversion > Unix (LF)`
	- On Linux, run `dos2unix <ks.txt>`
