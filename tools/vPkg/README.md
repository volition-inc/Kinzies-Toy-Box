# vPkg

The vpkg tool for SR3 is designed to allow creation of packfiles(vpp_pc), compressed streaming files(sr2_pc) and asset assembler files(asm_pc). The tool supports extraction and building of the archives used in Saints Row 3.

Running the tool involves the command line so batch files can easily be created to accomplish tasks. The command line arguments are as follows:

- -build_vehicle <vehicle_package_file.str2_pc> <filename1> <filename2> …
- -build_item <item_package_file.str2_pc> <filename1> <filename2> …
- -build_character <character_package_file.str2_pc> <filename1> <filename2> …
- -build_packfile <packfile_name.vpp_pc> <filename1> <filename2> …
- -extract_packfile <packfile_name.vpp_pc>

The str2 building commands(vehicle, item, character) will build a non-preloaded str2_pc file from the files provided along with building and including an asm file for the streaming system.

Extracting a packfile will extract to the current working directory and is able to extract vpp_pc and str2_pc files.