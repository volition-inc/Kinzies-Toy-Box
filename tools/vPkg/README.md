# vPkg

The vpkg tool for SR3 is designed to allow creation of packfiles(vpp_pc), compressed streaming files(sr2_pc) and asset assembler files(asm_pc). The tool supports extraction and building of the archives used in Saints Row 3.

Running the tool involves the command line so batch files can easily be created to accomplish tasks. The command line arguments are as follows:
- -working_dir <dirname>
- -build_vehicle <vehicle_package_file.str2_pc> <filename1> <filename2> …
- -build_item <item_package_file.str2_pc> <filename1> <filename2> …
- -build_preload_item <item_preload_package_file.str2_pc> <filename1> <filename2> …
- -build_character <character_package_file.str2_pc> <filename1> <filename2> …
- -build_packfile <packfile_name.vpp_pc> <-combine_asms combined_asm_name.asm_pc> <filename1> <filename2> …
- -extract_packfile <packfile_name.vpp_pc>

working dir is a global setting that will make extract_packfile and build_packfile

The str2 building commands(vehicle, item, character) will build a non-preloaded str2_pc file from the files provided along with building and including an asm file for the streaming system. You can later combine those str2 files and asm files into a single packfile with a single asm file by using -combine_asms. The build_packfile switch accepts wildcards for filenames, so things like -build_packfile my_new_packfile.vpp_pc -combine_asms my_new_packfile.asm_pc *.asm_pc *.str2_pc will work.

Extracting a packfile will extract to the working dir or to the current working directory if working dir is not set and is able to extract vpp_pc and str2_pc files.

