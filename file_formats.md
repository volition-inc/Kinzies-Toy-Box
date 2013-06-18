File Formats
============

## TOC
- [VPP Files](#vppfiles)
- [STR2 Files](#strfiles)



<a name="vppfiles"></a>
## VPP Files

The vpp_pc file format is just an archive file, similar to a zip file. It is used as a general format to group files needed by type. We generate a packfile for things like items, characters, vehicles and sounds.

File format is the following:

```c
// 2048 bytes : header info (v_packfile).
// - descriptor (4 bytes)
// - version (4 bytes)
// - short name (65 null terminated bytes)
// - pathname (256 null terminated bytes)
// – flags (4 bytes)
// - sector (4 bytes) // packfile starts at this sector
// - dir_size (4 bytes) // number of bytes in directory block
// – filename_size (4 bytes) // number of bytes in filenames block
// – data_size (4 bytes) // number of uncompressed bytes in the data files
// – compressed_data_size (4 bytes) // number of compressed bytes in the data section
// – dir (4 bytes) // offset/pointer to directory section
// – filenames (4 bytes) // offset/pointer to filenames
// – data (4 bytes) // offset/pointer to data section
// – open count (4 bytes) // number of files that have open handles into this packfile(runtime data)

// 2048 bytes : directory block (v_packfile_entry).
// – name char * (4 bytes)
// – sector (4 bytes) sector RELATIVE to start of packfile of start of file
// – start (4 bytes) byte offset RELATIVE to start of packfile of start of file
// – size (4 bytes) uncompressed file size
// – compressed_size (4 bytes) compressed file size
// – parent (4 bytes) packfile containing this file

// FILE DATA LAYOUT:
//
// RAW: (old skool)
// [file + SECTOR_alignment_pad] x N
//
// CONDENSED:
// [file + alignment_pad] x N
//
// COMPRESSED:
// [compress(file) + SECTOR_alignment_pad] x N
//
// CONDENSED + COMPRESSED:
// compress([file + alignment_pad] x N)
//
// NOTE: SECTOR_alignment_pad is padding up to PACKFILE_SECTOR_SIZE boundary
// NOTE: alignment_pad is padding up to PACKFILE_ALIGNMENT_VALUE
//
// NOTE: for computing DATA_SIZE in RAW and COMPRESSED formats, do not count the
// the SECTOR_alignment_pad of the terminal file.
// NOTE: for computing DATA_SIZE in CONDENSED formats, do not count the the
// alignment_pad of the terminanl file.
```
<a name="str2files"></a>
## STR2 Files 

Str2_pc files are just like packfiles used in SR3, but they are specifically used by the streaming system and have a required file inside of them(the .asm_pc file). The str2_pc file is a collection of the files needed for a specific streaming container such as a single weapon, character, or vehicle.

## v\_file_header

Used at the top of certain files to describe data referenced by this file so we can build streaming containers. Most container building was still done by table files on SR3, but some things like the zone file had a v_file_header with files referenced by the zone.

```c 

struct v_file_header
{
	et_uint16		m_signature;			// binary file signature
	et_uint16		m_version;			// file version

	et_uint32		m_reference_data_size;		// size of reference data
	et_uint32		m_reference_data_start;    // offset to reference data
	et_uint32		m_reference_count;		// number of references in header

	uint8			m_initialized;			// whether this header has been initialized.
	uint8			m_pad[15];			// extra pad data, zero'd before write
}; 

```

# czn/gzn files

The czn/gzn zone files correspond to the cpu data and gpu data required to load a piece of the world. These files contain everything from the physics to the objects needed for that piece.

The file consists of a v_file_header followed by the following world header

```c
// Header for zones
struct world_zone_header {
	// 8 bytes 
	et_uint32		m_signature;
	et_uint32		m_version;

	// 4/8 bytes
	const struct v_file_header *m_v_file_header;		// Filled in at load time

	// position offset to apply to all file refs
	et_fl_vector		m_file_reference_offset;

	// number of file references
	wz_file_reference	*m_file_references;		// Filled in at load time
	et_uint16		m_num_file_references;

	// type of zone
	uint8			m_zone_type;
	uint8			m_unused;			// pad previous to two bytes

	wz_interior_trigger	*m_interior_triggers;		// filled in at load time
	et_uint16		m_num_interior_triggers;	// filled in at load time

	uint16			m_extra_objects;		// only in test levels

	uint8			m_pad[24];			// Pad for future use, guaranteed to be zero'd
};
```

The file data itself is a chunked format that has a header with type and size followed by the data for that chunk. The chunk header looks like this:

```c
struct zone_section {
	et_uint32		m_id;
	et_uint32		m_cpu_size;
	et_uint32		m_gpu_size;
}
```
An important note is that the gpu size parameter is missing and not padded if the section header does not have a the high bit set.

Zone section identifiers:

```c
"Crunched ref geom",		0x2233,	"References to level mesh files (not the lmeshes themselves"
"Objects",			0x2234,	"Game objects, as placed in the WE"
"Navmesh",			0x2235,	"The navmesh data for the zone"
"Traffic",			0x2236,	"Traffic data for the zone"
"WE geom",			0x2237,	"Editor created geometry (patches, roads, path deform meshes"
"Sidewalks",			0x2238,	"Sidewalk data for the zone"
"Trailer",			0x2239,	"???"
"Light clip meshes",		0x2240, "Light clip mesh data"
"Traffic signals",		0x2241,	"Traffic signal data"
"Mover constraints",		0x2242 , "Constraints for movers in the zone"
"Interior triggers",		0x2243,	"Interior trigger volumes"
"Heightmap",			0x2244,	"Heightmap data for the zone (stitched into the larger world)"
"c_object rbb tree",		0x2245,	"rbb tree for collecting c_objects at runtime"
"Undergrowth",			0x2246,	"Undergrowth data"
"Water volumes",		0x2247,	"Water volume data"
"Wave killers",			0x2248,	"Wave killer data"
"Water surfaces",		0x2249,	"Water surface data"
"Parking",			0x2250,	"Parking data"
"Rain killer meshes",		0x2251,	"Rain killer mesh data"
"Supplemental LOD",		0x2252,	"Autogenerated 4th LOD data"
"Fading grid data",		0x2253,	"City fading grid data. Also used for mip streamer calculations"
"Audio Emitter rbb tree",	0x2254,	"rbb tree for collecting audio emitters at runtime"
"Havok Pathfinding data",	0x2255,	"Havok pathfinding data for the zone"
```