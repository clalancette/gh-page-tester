# Standards
The original ISO9660 standard is fairly old, having first been ratified in 1988.  This standard has many limitations (such as a maximum of 8 directory levels, a maximum of 31 characters for filenames, etc.), and thus a number of extensions have made the original standard a lot more palatable on modern systems.  The most relevant standards that are used today include:

- [ISO 9660:1988](https://en.wikipedia.org/wiki/ISO_9660). Information processing - Volume and file structure of CD-ROM for information interchange.  Also known as [Ecma-119](https://www.ecma-international.org/publications/standards/Ecma-119.htm).
- [ISO/EIC 9660:1999](http://pismotec.com/cfs/iso9660-1999.html).  Information technology -- Volume and file structure of CD-ROM for information interchange.
- [El Torito](http://wiki.osdev.org/El-Torito) Bootable CD-ROM Format Specification Version 1.0.
- [Joliet](https://en.wikipedia.org/wiki/Joliet_(file_system)) Specification.
- [System Use Sharing Protocol (SUSP)](https://en.wikipedia.org/wiki/Rock_Ridge), Version 1.09 and Version 1.12.
- [Rock Ridge Interchange Protocol (RRIP)](https://en.wikipedia.org/wiki/Rock_Ridge), Version 1.09 and Version 1.12.
- [Ecma-167](https://www.ecma-international.org/publications/standards/Ecma-167.htm). Volume and File Structure for Write-Once and Rewritable Media using Non-Sequential Recording for Information Interchange.
- [UDF](http://www.osta.org/specs/), Version 1.02 through 2.60.
- [Ecma TR-071](https://www.ecma-international.org/publications/techreports/E-TR-071.htm).  Ecma Technical Report for "DVD Read-Only Disk - File System Specifications".

Unfortunately, accessing most of these standards requires a license, so the links above are not primary sources (with the exception of the Ecma standards, which are available free of charge from [https://www.ecma-international.org](https://www.ecma-international.org)).  Nevertheless, they give a good overview of the state of the ISO ecosystem as it exists today.

While PyCdlib aims to be compliant with these standards, there are a number of complicating factors.  One such factor is that there are places in the standards that are ambiguous, and different implementations have taken different approaches to solving the same problem.  Another complicating factor is the fact that there are several "standard" parts of ISOs that have no relevant standard backing them up; they are just generally agreed to by the various implementations.  PyCdlib takes a middle road here, and tries to be pretty forgiving with the type of ISOs that it can open, but fairly strict with what it can produce.  When there are ambiguities in the standards, PyCdlib generally takes the approach of being compliant with whatever [cdrkit](https://launchpad.net/cdrkit) does.  However, there are several bugs in the cdrkit implementation, so in those cases, PyCdlib falls back to being standards compliant.

## Interchange levels
The original ISO9660 standard defines "interchange" levels 1, 2, and 3.  The differences between the three interchange levels is mostly irrelevant now, as most modern ISOs use interchange level 3 (and supplement it with one of the extensions).  A newer version of the ISO9660 standard was put out in 1999 that lifts some of the restrictions of the original ISO9660 standard.  PyCdlib follows the lead of genisoimage here and defines this as interchange level 4, although that is not an "official" designation.  For almost all use cases, interchange level 3 should be used with the [Rock Ridge](#rock-ridge) and [Joliet](#joliet) extensions.

## Rock Ridge and Joliet
The two most common extensions to the original ISO9660 standard are Rock Ridge and Joliet, both of which allow ISOs to contain deeper directory structures, longer filenames, and other features usually used by modern filesystems.  While both standards aim to accomplish the same goal, they do it in entirely different ways, and some of those details leak through into the PyCdlib API.  Thus, a brief discussion of each of them is in order.

### Rock Ridge
The standard commonly referred to as "Rock Ridge" is actually two standards, SUSP and Rock Ridge proper.  SUSP stands for "System Use and Sharing Protocol", and defines a few generic, operating system-independent fields to be placed at the end of file and directory metadata on the ISO.  Rock Ridge proper then defines a number of Unix-specific fields to be placed after the SUSP fields.  The combination of the two allows ISOs to contain Unix-like semantics for each file and directory, including permission bits, longer filenames, timestamps, symlinks, character and block devices, and a few other minor features.  One important thing to realize about Rock Ridge is that it is an extension to the original ISO9660, and thus shares the file/directory structure with the original ISO.  This structure can actually be virtually extended for deeper directory structures, but that is an implementation detail and will be glossed over here.  For more information, read the Rock Ridge specification.

### Joliet
The Joliet standard came out of Microsoft, and is primarily intended to provide extensions to ISO for Windows compatibility.  However, the data stored in Joliet is mostly generic, so can easily be used by all operating systems.  In large contrast to Rock Ridge, Joliet uses an entirely different namespace to store the file and directory structure of the extended names.  The file *data* is shared between ISO9660 and Joliet, but the essential metadata is not.  The consequence of this is that there can be files on the ISO that are only visible to ISO9660/Rock Ridge, files that are only visible to Joliet, or files that are visible to both.  That being said, the most common arrangement is for the file and directory structure to be replicated between ISO9660/Rock Ridge and Joliet.

## El Torito
El Torito is the name of the standard used to make an ISO bootable.  Without going into the details too much, El Torito works by defining a "boot catalog" that has a list of one or more entries for booting.  Each entry consists of a pointer to a file on the ISO, and a booting method.  The available booting methods are:

1.  Floppy emulation booting - Emulate the boot that would have been done by a floppy disk.  The file to be used must be one of the sizes of a floppy, and must have a particular structure.  This method was developed to support very old BIOS's that didn't know how to boot from a CD, but is rarely used anymore.
1.  HD emulation - Emulate the boot that would have happened from a hard drive.  The file to be used must be 512 bytes long, and be a valid MBR.  Again, this method was developed to support old BIOS's, but is rarely used in modern ISOs.
1.  No emulation - Don't do any emulation for booting.  This is the method that is used for BIOS's that know how to boot from ISO, and is the one most commonly used today.  There are few restrictions on what the contents of the file must be, other than it should be valid code for the machine it will be used to boot.

## UDF
The "Universal Disk Format" was proposed in the late 1990's to supplant the aging ISO9660 as the filesystem of choice on optical media.  It was adopted as the standard DVD filesystem format, and thus started gaining widespread popularity and use.  Due to backwards-compatibility concerns, however, it mostly didn't replace ISO9660 as much as augment it as another way to encode the data on a volume.  This is often known as the "bridge" format.

The UDF filesystem format consists of two or three separate specifications, depending on how you count them.  Ecma-167 is the base specification that defines the filesystem structure, types, etc.  The UDF specification starts with Ecma-167 and adds specific rules and restrictions that, when followed, make a filesystem layout UDF compliant.  Further, Ecma TR-071 starts with the UDF specification and adds even more rules and restrictions that, when followed, make a filesystem layout DVD Read-only media compliant.

***

<div style="text-align: center"><a href="https://clalancette.github.io/gh-page-tester/">Top</a></div><div style="text-align: right"><a href="python-compatibility.html">Python Compatibility --></a></div>
