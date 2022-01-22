# Plutus Playground Statup Steps

## Notes
- Every week, git clone the following repository for class work in terminal
	- `git clone https://github.com/input-output-hk/plutus-pioneer-program`
- Check the git commit by going to the cabal.project file of the week.
	- look for location: https://github.com/input-output-hk/plutus-apps.git
	- copy git commit tag, e.g. 41149926c108c71831cfe8d244c83b0ee4bf5c8a
- Cd to ~/plutus-apps directory
- In terminal, `git checkout <paste-tag-here>`
	- e.g. git checkout 41149926c108c71831cfe8d244c83b0ee4bf5c8a
- In ~/plutus-apps directory
	- `GC_DONT_GC=1 nix-build -A plutus-playground.client -A plutus-playground.server`
- NOTE: (removed nix.conf file sandbox and extra-sandbox-paths)
- reran `GC_DONT_GC=1 nix-build -A plutus-playground.client -A plutus-playground.server`
	- gave delta of two instances, ran 1 second, hopefully delta changes made.
- starting nix shell
	-

## Week 1 Example
in terminal, cd to
/Users/christophegarant/code/haskell/plutus-pioneer-program/code/week01
check the 


### Nix helpful commands
nix doctor --verbose						//precheck
nix --version									//to check it is installed correctly
nix-channel --update --verbose	//seems to not work
nix-env -iA nixpkgs.nix-update		//works, command for non NixOS
nix-build --show-trace					//to get details of fails
nix-collect-garbage						//cleans up garbage old files https://www.mankier.com/1/nix-collect-garbage
--extra-experimental-features nix-command


### Helpful resources 

For macOS i7
- [MacOS Setup for Plutus Pioneer Program](https://github.com/Til-D/cardano-plutus)
	- macOS Monterey, v12.0.1
	- processor: 2.3 GHz Quad-Core Intel Core i7
	- memory: 16 GB 3733 MHz LPDDR4X
	
- [Plutus developer environment setup on MacOS Monterey](https://www.punkbit.com/hacking/plutus-developer-environment-setup-on-macos-monterey/)
	- the `yarn install` did not work for me, the npm did 
	
For macOS M1 (newer, but good reference)
- [cardano-plutus-apps-install-m1](https://github.com/renzwo/cardano-plutus-apps-install-m1/blob/main/README.md)

-[Cardano Stack Exchange - Plutus Pioneers Program](https://cardano.stackexchange.com/questions/tagged/plutus-pioneer-program)

## Attempt 1

### Steps
//install
sh <(curl -L https://nixos.org/nix/install) --daemon

//edit the config file
build-users-group = nixbld
substituters        = https://hydra.iohk.io https://iohk.cachix.org https://cache.nixos.org/
trusted-public-keys = hydra.iohk.io:f/Ea+s+dFdN+3Y/G+FDgSq+a5NEWhJGzdjvKNGv0/EQ= iohk.cachix.org-1:DpRUyj7h7V830dp/i6Nti+NEO2/nhblbov/8MW7Rqoo= cache.nixos.org-1:6NCHdD59X431o0gWypbMrAURkbJ16ZPMQFGspcDShjY=volume

//build it
GC_DONT_GC=1 nix-build -A plutus-playground.client -A plutus-playground.server

//run the shell
CG_DONT_GC=1 nix-shell

### Issues
1. nix doctor --verbose
		(base) christophegarant@Christophes-MBP / % nix doctor --verbose
		Running checks against store uri: daemon
		[FAIL] Multiple versions of nix found in PATH:
  			/nix/store/iq3ra93h9kxmnrw3zlxmqn8ng5w62dra-nix-2.5.1/bin
  			/nix/store/j6fqvmsfhl4frzqn2f3bzhn8hr16j5q5-nix-2.5.1/bin
  			
  			
 2. Ran nix-build all night, timed out or froze.  
 Read comment here that https://cardano.stackexchange.com/questions/3878/sandbox-error-when-running-nix-build
 Remove the sanbox and extra-sandbox-paths from nix/conf file
 
 3. nix-build worked, but then nix-shell had the following error
 error: getting status of /nix/var/nix/daemon-socket/socket: Operation not permitted


## Attemp 2
Per advice from:
- [](https://github.com/Til-D/cardano-plutus)

**Iteration 2:** uninstalling nix, restarting, installing with nix macOS recommended install, and recommended nix.conf file before build.
- recommended nix uninstall [](https://github.com/NixOS/nix/issues/1402)

	To uninstall nix you will need to do the following things:

	from: https://github.com/NixOS/nix/issues/1402
	remove the . "$HOME/.nix-profile/etc/profile.d/nix.sh" line in your ~/.profile or ~/.bash_profile
	rm -rf $HOME/{.nix-channels,.nix-defexpr,.nix-profile,.config/nixpkgs}
	sudo rm -rf /nix
	On a multi-user install, also remove all the nixbld[0-9] users

_note:_ also from the bottom of [](https://github.com/renzwo/cardano-plutus-apps-install-m1/blob/main/README.md)
	- https://gist.github.com/kintsugi/47e5e9f9d7c3a3a2f6004b6271365f8c
	- downloand (`git close <https-copy-paste-here`> and run from terminal by `sudo sh <drag-and-drop-file-in>`

- recommended nix install macOS [](https://nixos.org/download.html#nix-install-macos)

### Steps 

1. Install per https://nixos.org/download.html#nix-install-macos

sh <(curl -L https://nixos.org/nix/install)

Ran into issue I guess from previous install, instructions said to run this.  I did and it worked

sudo mv /etc/bash.bashrc.backup-before-nix /etc/bash.bashrc

2. Restart

3. Edit `/etc/nix/nix.conf` file per [](https://github.com/renzwo/cardano-plutus-apps-install-m1/blob/main/README.md)
_minus the system and extra-platforms intended for M1._

sudo nano /etc/nix/nix.conf

		build-users-group = nixbld
		
		substituters        = https://hydra.iohk.io https://iohk.cachix.org https://cache.nixos.org/
		trusted-public-keys = hydra.iohk.io:f/Ea+s+dFdN+3Y/G+FDgSq+a5NEWhJGzdjvKNGv0/EQ= iohk.cachix.org-1:DpRUyj7h7V830dp/i6Nti+NEO2/nhblbov/8MW7Rqoo= cache.nixos.org-1:6NCHdD59X431o0gWypbMrAURkbJ16ZPMQFGspcDShjY=volume
		
		sandbox = false
		extra-sandbox-paths = /System/Library/Frameworks /System/Library/PrivateFrameworks /usr/lib /private/tmp /private/var/tmp /usr/bin/env
		experimental-features = nix-command
		extra-experimental-features = flakes

4. Restart computer
5. clone plutus-apps (already did)
6. cd to ~/plutus-apps
7. git status -> 41149926c (already did git checkout of latest)
8. Build playground code
		In plutus playground, will not compile just yet (see below error)
       			- #14 [HPM] Error occurred while trying to proxy request /api/contract from localhost:8009 to http://localhost:8080 (ECONNREFUSED) (https://nodejs.org/api/errors.html#errors_common_system_errors)

       	In new terminal tab, got to ~/plutus-pioneers-program/code/week01
       	Cabal update
       	Cabal build
9. Build the plutus playground
		nix-build -A plutus-playground.server -A plutus-playground.client
10. Fire up the nix-shell (first time takes a while)
		nix-shell (CG_DONT_GC=1 nix-shell ?)
11. start plutus server
		cd plutus-playground-server
		GC_DONT_GC=1 plutus-playground-server
			(note: without, seems the playground won't compile with error cannot find module)
12. start plutus client
		New tab (ctrl+t)
		from ~/plutus-apps
		nix-shell
13. update dependancies npm (java)
		sudo GC_DONT_GC=1 npm install -g npm
		(Note: Warnings did not working origonally 		
			sudo npm install -g npm
			"missing write access to /nix/store/...." WARNINGS
			https://cardano.stackexchange.com/questions/6320/unable-to-start-plutus-apps-client-application)
14. start client node server
		cd plutus-playground-client
		GC_DONT_GC=1 npm run start 
		(note, before just used npm run start, but local host did not show up)
15. Open [](http://localhost:8009/)
		(Note: edits per https://cardano.stackexchange.com/questions/404/plutus-pioneer-program-problem-with-plutus-playground-client?rq=1)
		added timeout: 1000 * 60 * 10 too
		Edit cardano/plutus/plutus-playground-client/webpack.config.js
       	
### Issues

 
