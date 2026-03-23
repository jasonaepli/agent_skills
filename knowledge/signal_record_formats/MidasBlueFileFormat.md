# Midas BLUE File Format

**CAM Controlled Document CCD-2006002**
Version 1.1.0 2013-11-18 11:26:22
Revision `$Id: blue.txt 316 2013-11-18 18:20:08Z sms $`
Released (November 18, 2013)

---

## 1 Preparatory Material

### 1.1 Introduction

The Midas BLUE file is a community standard file format that has evolved over the history of Midas processors to become the de facto Ground Systems Office (GSO) standard format for saving data to disk. This volume describes version 1.1 of the Midas BLUE file format and its use in standard Midas processors as controlled by the Core Asset Manager. The Midas frameworks used for these processing systems include X-Midas (with XMPy) and NeXtMidas.

Version 1.0 of the BLUE file format (**BLUE 1.0** see 1.4) was intended to document the existing file format as implemented in Core Asset Manager (CAM) Midas frameworks as of early 2006. At that time, part of the GSO community moved to use **Platinum** (see 1.4) as the preferred format for file-based interchange. While Platinum is based on BLUE 1.0, and is known as "BLUE 2.0," the Midas processing community still relies on additional formats, capabilities, and interpretations that have not been adopted by Platinum; as a result, the two formats are not fully interchangeable. This document describes an updated version of the BLUE 1.0 lineage that includes some minor usage updates (including updates to facilitate better compatibility with Platinum) and enhancements. The vast majority of files produced in accordance with this standard will be compatible with BLUE 1.0 and nearly all BLUE 1.0 files are compatible with this standard.

Since 2009, both X-Midas and NeXtMidas have produced files compatible with this document, even though the formal document publication was delayed until 2013.

### 1.2 Document Conventions

Structure field names are represented in `this font`.
Keywords are represented in **THIS FONT**.
Literal text character sequences are represented in `this font`.

This document contains tables that define the structure of various portions of the file. The names, types, and short descriptions of the fields are taken from the X-Midas implementation of BLUE files, and may differ slightly in other Midas systems.

Where binary data types are referenced in structures, the type includes a cardinal number that represents the number of bytes used for that data type; for example, the type `int_4` denotes a four-byte (32-bit) signed twos-complement integer, and the type `real_8` denotes an eight-byte (64-bit) floating point number.

### 1.3 Acknowledgements

This document is maintained by the Core Asset Manager. Parts of the document are based on material provided by IMPACT Science and Technology, Inc.

### 1.4 Applicable Documents

[BLUE 1.0] *Midas BLUE File Format* (`CCD-2006002`), Version 1.0.2, 1 August 2010.

[Platinum] *Platinum BLUE Data Exchange Format Standard*, Revision 4 "Volume 1: Platinum BLUE File Format" (`STD-DEV-PLBU/01`), and "Volume 2: Standard Platinum Keywords" (`STD-DEV-PLBU/02`), 16 June 2009.

### 1.5 Time Values

Unless otherwise specified, all time values stored within the BLUE 1.1 file in numeric form are stored in terms of UTC days since 1 January 1950, multiplied by 86,400 seconds/day, plus seconds since UTC midnight of the current day, excepting leap-seconds (which are not counted).

All measurements in a BLUE 1.1 file given in terms of seconds use the SI second.

*Although slightly obtuse, the above definitions are necessary since the epoch used (1 January 1950) predates the definition of UTC time and because UTC times prior to 1972 were reckoned using a definition of "one UTC second" that was not equal to "one SI second."*

Since leap-seconds are not counted, it is not possible to produce a valid BLUE 1.1 file with a time value that corresponds to the interval during which a leap-second was inserted.

Extended Header Keywords that convey time values and have an accompanying `.UNIT` or `.UNITS` specified as per the Platinum standard may optionally be interpreted in accordance with the Platinum standard; however, any systems providing this option are encouraged to issue a warning in situations where the interpretation using the Platinum standard differs from the interpretation implied by this standard (floating-point rounding errors excepted).

### 1.6 Deprecated Features

In the interest of maximal compatibility with BLUE 1.0 and existing legacy systems, this standard documents a number of "Deprecated" features that may appear under BLUE 1.1, but their use is discouraged on account of limited support within the various Midas baselines.

Systems compatible with this standard are **not** required to support writing of BLUE 1.1 files utilizing Deprecated features and may issue a warning or error upon the reading of a file that utilizes a Deprecated feature.

---

## 2 History of Midas File Formats

The Midas file format is a community standard format that was introduced nearly 30 years ago as part of the Midas Program. As the years have passed the format has undergone several changes and has been adapted for use by other programs. The original format was Midas **GOLD**, but this format has been universally superseded by the current Midas **BLUE** file format.

There are two major families of processing system that use the Midas **BLUE** format. The X-Midas system, along with its offshoots Midas 2k (in C++), NeXtMidas (in Java), and XMPy (in Python), is the direct descendent of previous Midas systems, and the **BLUE** format was developed within it.

MARTES is a spin-off from C-Midas, which itself was a conversion of X-Midas' ancestor DCL-MIDAS. MARTES adopted the **BLUE** file format early on because of its improved portability; however, this early adoption resulted in a divergence of features. Over time, most of the differences have been reconciled, but the MARTES system remains outside the purview of the Core Asset Manager. Material in this document that describes MARTES-specific features or behavior may be particularly inaccurate.

### 2.1 Differences in BLUE File Implementations

#### 2.1.1 Extended Header Keywords

Prior to 2005, MARTES used "MARTES ASCII" keywords in the Extended Header. These were phased out between 2002 and 2005 and are only supported under the **BLUE 1.0** standard.

#### 2.1.2 Additional File Types

Midas **BLUE** files support several file types to represent a variety of data structures, including one- and two-dimensional data as well as non-homogeneous records. Refer to section 3.1.2 of this volume for a description of **BLUE** file format types.

X-Midas added support for type 4000 and type 5000 files. A type 4000 file is a series of keyword blocks in the same format as the extended header, and is well-suited to streaming metadata. Type 5000 files are an enhancement to type 3000 files, adding units and a frame of reference to the subrecords; they were designed for generic system modeling.

MARTES added a type 6000 file format that has many uses but is most commonly associated with and used for descriptor word files, such as pulse descriptor words (PDWs). Type 6000 files were subsequently adopted into the X-Midas family of systems.

Not all Midas processing systems currently support all file types. In particular, MARTES does not support type 4000, and Midas 2k does not support type 6000.

#### 2.1.3 Platinum (BLUE 2.0)

The Platinum file format is closely related to Midas **BLUE**, but is not coupled to the historical implementations used within Midas systems. Some of the Midas **BLUE** features and requirements were not adopted by Platinum, and Platinum imposes semantics on **BLUE** keyword interpretation that are not natively recognized by Midas systems. Some of the major differences between Platinum and **BLUE** include:

- **BLUE** format types 4000 and 5000 are not part of Platinum. Specific subtypes of the other major file types are also not common to both systems.
- **BLUE** does not interpret file contents as a sequence of events. Type 4000 files serve a similar function in **BLUE 1.0**.
- **BLUE** uses a strict linear interpolation mechanism to associate time with sample data, while Platinum uses its event structure to tie precise times to specific samples.

A good overview to the differences between **BLUE 1.0** and Platinum, and advice on minimizing incompatibilities between the formats, are provided in the *Guide to Using the Platinum Format*, which can be found in the Platinum documentation on GForge.

---

## 3 Midas BLUE Format

A Midas **BLUE** file is divided into three regions:

1. The **Header Control Block (HCB)**, which contains the file header and adjunct (variable) header data, and occupies the first 512 bytes or more of the file.
2. The **Data Block**, which contains the primary data and begins on a 512-byte boundary after the HCB.
3. The **Extended Header**, which contains keyword-based metadata and begins on a 512-byte boundary after the Data Block.

The HCB is always present. The Data Block and Extended Header are optional, but the Extended Header, if present, must follow the Data Block. The Data Block may be zero bytes in length; however, the Extended Header still begins on a 512-byte boundary after the start of the Data Block.

### 3.1 Header Control Block (HCB)

The Header Control Block (HCB) occupies the beginning of the file. It is composed of two parts: the Fixed Header (also called the Main Header) and the Variable (Adjunct) Header.

The HCB always begins at byte offset 0 and occupies at minimum 512 bytes. When the adjunct header requires more than the default amount of space, the HCB will be larger than 512 bytes; it is always a multiple of 512 bytes.

### 3.1.1 Fixed Header

The Fixed Header (also known as the Main Header) occupies the first 256 bytes of the file. It contains fields describing the overall structure, type, and parameters of the file.

**Table 2 — BLUE Header Fields**

| Offset | Name | Size | Type | Description |
|--------|------|------|------|-------------|
| 0 | `version` | 4 | `char[4]` | Header version |
| 4 | `head_rep` | 4 | `char[4]` | Header representation |
| 8 | `data_rep` | 4 | `char[4]` | Data representation |
| 12 | `detached` | 4 | `int_4` | Detached header |
| 16 | `protected` | 4 | `int_4` | Protected from overwrite |
| 20 | `pipe` | 4 | `int_4` | Pipe mode (N/A) |
| 24 | `ext_start` | 4 | `int_4` | Extended header start, in 512-byte blocks |
| 28 | `ext_size` | 4 | `int_4` | Extended header size in bytes |
| 32 | `data_start` | 8 | `real_8` | Data start in bytes |
| 40 | `data_size` | 8 | `real_8` | Data size in bytes |
| 48 | `type` | 4 | `int_4` | File type code |
| 52 | `format` | 2 | `char[2]` | Data format code |
| 54 | `flagmask` | 2 | `int_2` | 16-bit flagmask (1=flagbit) |
| 56 | `timecode` | 8 | `real_8` | Time code field |
| 64 | `inlet` | 2 | `int_2` | Inlet owner |
| 66 | `outlets` | 2 | `int_2` | Number of outlets |
| 68 | `outmask` | 4 | `int_4` | Outlet async mask |
| 72 | `pipeloc` | 4 | `int_4` | Pipe location |
| 76 | `pipesize` | 4 | `int_4` | Pipe size in bytes |
| 80 | `in_byte` | 8 | `real_8` | Next input byte |
| 88 | `out_byte` | 8 | `real_8` | Next out byte (cumulative) |
| 96 | `outbytes` | 64 | `real_8[8]` | Next out byte (each outlet) |
| 160 | `keylength` | 4 | `int_4` | Length of keyword string |
| 164 | `keywords` | 92 | `char[92]` | User defined keyword string |
| 256 | `adjunct` | 256 | `char[256]` | Type-specific adjunct union |

#### 3.1.1.1 `version`

The `version` field identifies the file as a Midas **BLUE** file. Valid values are `BLUE` (standard byte order) and `EEEI` (byte-swapped). The string `EEEI` is `IEEE` backwards, indicating that the bytes of multi-byte fields are stored in the reverse order from the standard **IEEE** representation.

Note: All **BLUE** files have `BLUE` in the `version` field. The `head_rep` field (see 3.1.1.2) determines the actual byte ordering of header fields. The `EEEI` value in `version` is a legacy indicator and is synonymous with having `EEEI` in `head_rep`.

#### 3.1.1.2 `head_rep`

The `head_rep` field specifies the byte ordering used for all multi-byte numeric fields in the Fixed Header and Adjunct Header. Valid values are listed in Table 3. The default is `IEEE` (big-endian byte order on most modern systems; actually denotes the native byte order of the host that wrote the file for legacy formats).

*Note: Legacy floating point representations (VAX and CRAY) are rarely encountered in modern systems and have limited support.*

#### 3.1.1.3 `data_rep`

The `data_rep` field specifies the byte ordering and floating-point representation used for data elements in the Data Block. Valid values are listed in Table 3. The default is `IEEE`.

**Table 3 — Valid Values for Header and Data Representation Fields**

| Code | Description |
|------|-------------|
| `IEEE` | **IEEE** 754 floating-point format, big-endian byte order (most-significant byte first) |
| `EEEI` | **IEEE** 754 floating-point format, little-endian byte order (least-significant byte first); `EEEI` is `IEEE` spelled backwards |
| `VAX` | VAX floating-point format, little-endian byte order (Deprecated) |
| `CRAY` | CRAY floating-point format, big-endian byte order (Deprecated) |

#### 3.1.1.4 `detached`

The `detached` field indicates whether this file is a detached header. A value of `0` indicates a standard attached header (the normal case). A non-zero value indicates that this file is a detached header file and the actual data is stored in a separate file.

When `detached` is non-zero, the value indicates the type of detachment:
- `1`: The detached data file has the same name as the header file but without the header file extension.
- `2`: The detached data file name is stored in the **BLUE** Extended Header under the keyword **DETACHED**.

#### 3.1.1.5 `protected`

The `protected` field is a write-protect flag. A value of `0` indicates the file is not protected. A non-zero value indicates the file should be treated as read-only. This is a software-enforced protection and does not replace file system permissions.

#### 3.1.1.6 `pipe`

The `pipe` field indicates whether the file is being used as a pipe (streaming data). A value of `0` indicates normal file mode. Non-zero values indicate pipe mode, in which the `data_size` field may not accurately reflect the total data written until the pipe is closed.

#### 3.1.1.7 `ext_start`

The `ext_start` field contains the starting location of the Extended Header, expressed as the number of 512-byte blocks from the beginning of the file. A value of `0` indicates that no Extended Header is present.

#### 3.1.1.8 `ext_size`

The `ext_size` field contains the size of the Extended Header in bytes. If `ext_start` is `0`, this field is `0` as well.

#### 3.1.1.9 `data_start`

The `data_start` field contains the starting location of the Data Block, expressed as the number of 512-byte blocks from the beginning of the file. This value is always at least `1` (since the HCB occupies at least the first 512 bytes). For the default 512-byte HCB, `data_start` is `1.0`.

#### 3.1.1.10 `data_size`

The `data_size` field contains the total size of the Data Block in bytes. For pipe-mode files, this field may be updated during writing and should be read from the file when the pipe is closed.

#### 3.1.1.11 `type`

The `type` field specifies the logical structure of the data stored in the file. The file type determines the format of the Adjunct Header. Valid values are listed in Table 4.

**Table 4 — Midas File Types**

| Type | Name | Description |
|------|------|-------------|
| 1000 | Type 1000 | One-dimensional data (a series of scalars or vectors along a single axis) |
| 1999 | Type 1999 | Deprecated type 1000 variant |
| 2000 | Type 2000 | Two-dimensional data (a raster or matrix of scalars or vectors) |
| 2200 | Type 2200 | Two-dimensional data, column-major storage (transposed relative to type 2000) |
| 3000 | Type 3000 | Record-based data (non-homogeneous records, each having a fixed format described by the adjunct) |
| 3999 | Type 3999 | Deprecated type 3000 variant |
| 4000 | Type 4000 | Keyword-data file (a series of Extended Header-format keyword blocks) |
| 4999 | Type 4999 | Deprecated type 4000 variant |
| 5000 | Type 5000 | Enhanced record-based data (similar to type 3000 but with units and frame of reference) |
| 5001 | Type 5001 | Type 5000 with time-domain data |
| 5010 | Type 5010 | Type 5000 variant |
| 6000 | Type 6000 | Descriptor word file (e.g., pulse descriptor words) |
| 6001 | Type 6001 | Type 6000 variant |
| 6002 | Type 6002 | Type 6000 variant |
| 6003 | Type 6003 | Type 6000 variant |
| 6010 | Type 6010 | Type 6000 variant |
| 6020 | Type 6020 | Type 6000 variant |
| 6030 | Type 6030 | Type 6000 variant |
| 6040 | Type 6040 | Type 6000 variant |
| 6050 | Type 6050 | Type 6000 variant |
| 6060 | Type 6060 | Type 6000 variant |
| 6070 | Type 6070 | Type 6000 variant |
| 6080 | Type 6080 | Type 6000 variant |

#### 3.1.1.12 `format`

The `format` field is a two-character code that specifies the data format of each element in the Data Block. The first character is the **size designator** and the second character is the **type designator**. Together they identify the type and size of each data element.

**Table 5 — Data Format Size Designator (first character of `format`)**

| Code | Size (bits) | Description |
|------|-------------|-------------|
| `P` | variable | Packed data (special encoding; see 3.1.1.12.1) |
| `A` | 8 | ASCII character (1 byte) |
| `O` | 8 | Octet (unsigned 8-bit integer) |
| `B` | 8 | Byte (signed 8-bit integer) |
| `I` | 16 | Integer (signed 16-bit integer) |
| `L` | 32 | Long integer (signed 32-bit integer) |
| `X` | 64 | Extended integer (signed 64-bit integer) |
| `F` | 32 | Float (32-bit **IEEE** single-precision floating-point) |
| `D` | 64 | Double (64-bit **IEEE** double-precision floating-point) |
| `G` | 128 | Quad (128-bit extended-precision floating-point, where supported) |
| `U` | 1 | Unsigned bit (packed single bits) |
| `V` | variable | Variable-length ASCII string (see 3.1.1.12.3) |
| `N` | variable | Nibble (4-bit packed value; see 3.1.1.12.1) |

**Table 6 — Data Format Type Designator (second character of `format`)**

| Code | Description |
|------|-------------|
| `S` | Scalar (single real value per element) |
| `C` | Complex (pair of real values per element: real part followed by imaginary part) |
| `V` | Vector (multiple scalars per element; element count in adjunct) |
| `M` | Matrix (2D array per element; dimensions in adjunct) |
| `U` | Unsigned scalar |
| `1` | (Alternative scalar designation, used in some legacy contexts) |

**Table 7 — Additional Format Designators**

| Code | Description |
|------|-------------|
| `SP` | Packed single bit |
| `NP` | Packed nibble (4-bit) |
| `NH` | Nibble, high-order first |
| `NL` | Nibble, low-order first |
| `VS` | Variable-length scalar string |
| `CF` | Complex float (32-bit real + 32-bit imaginary) |
| `CD` | Complex double (64-bit real + 64-bit imaginary) |

##### 3.1.1.12.1 Packed Bits and Nibbles

The `SP` format stores data as packed single bits. Each byte contains 8 bits of data. Bits are stored most-significant-bit first within each byte. The total number of bits is given by the `data_size` field multiplied by 8.

The `NP` (or `N`) format stores data as packed 4-bit nibbles. Two nibbles are stored per byte. The order of nibbles (high-nibble-first or low-nibble-first) may be indicated by `NH` or `NL` respectively.

**Note:** The `SP` format (`SB` nibble fields) has different handling in NeXtMidas versus other Midas implementations. In NeXtMidas, `SP` data elements are returned as `byte` values (0 or 1), not packed bits. Writers of NeXtMidas-produced files should be aware of this distinction.

##### 3.1.1.12.2 Unsigned Integer Types

Unsigned integer formats (e.g., `OB`, `IU`) are supported in some Midas implementations but may not be universally available. When writing files intended for broad use, prefer signed integer types.

For variable-length ASCII strings (`VS` format), each string element is padded to the element size with null bytes (`\x00`). Readers should strip trailing null bytes when interpreting the string content.

##### 3.1.1.12.3 Modified UTF-8 Specification

For `VS` format (variable-length string) fields that carry Unicode text, the encoding used is a Modified UTF-8 scheme compatible with Java's `DataInputStream.readUTF()` / `DataOutputStream.writeUTF()` convention:

```
Modified UTF-8 Encoding:
- Characters in the range U+0001 to U+007F are encoded as a single byte.
- The null character (U+0000) and characters in the range U+0080 to U+07FF
  are encoded as two bytes.
- Characters in the range U+0800 to U+FFFF are encoded as three bytes.
- Characters outside the Basic Multilingual Plane (U+10000 and above) are
  encoded as a surrogate pair, each half encoded as three bytes (6 bytes total).
- The two-byte length prefix (big-endian unsigned short) indicates the number
  of encoded bytes that follow, not the number of characters.
```

#### 3.1.1.13 `flagmask`

The `flagmask` field is reserved and should be set to `0`.

#### 3.1.1.14 `timecode`

The `timecode` field stores the time of the first data sample as a double-precision floating-point value. The time is measured in seconds since midnight, 1 January 1950 UTC (not counting leap seconds), as described in section 1.5.

For type 1000 files, `timecode` gives the time corresponding to the first sample. When **TC_PREC** (see 3.4.1.5) is present in the Main Header keywords, it provides an additional precision offset to be added to `timecode`. Specifically, the actual precise time is:

    precise_time = timecode + TC_PREC

The `xstart` field (see 3.1.1.21) provides the X-axis starting value, which may duplicate the time information or represent a different quantity (e.g., frequency), depending on the application.

#### 3.1.1.15 `inlet`

The `inlet` field is a legacy field that records the inlet number used during acquisition. It may be set to `0` if not applicable.

#### 3.1.1.16 `outlets`

The `outlets` field is a legacy field that records the number of outlets used during acquisition. It may be set to `0` if not applicable.

#### 3.1.1.17 `outmask`

The `outmask` field is a legacy bitmask field indicating which outlets are active. It may be set to `0` if not applicable.

#### 3.1.1.18 `pipeloc`

The `pipeloc` field is a legacy field tracking the current position in a pipe. It may be set to `0` if not applicable.

#### 3.1.1.19 `pipesize`

The `pipesize` field is a legacy field indicating the pipe buffer size. It may be set to `0` if not applicable.

#### 3.1.1.20 `in_byte`, `out_byte`, `outbytes`

These are legacy fields tracking byte counts for inlet and outlet connections. They may be set to `0` if not applicable.

#### 3.1.1.21 `xstart`

The `xstart` field specifies the value of the independent variable (X-axis) at the first data sample. For time-series data, this is typically the same as `timecode`. For frequency-domain data, it represents the starting frequency.

#### 3.1.1.22 `xdelta`

The `xdelta` field specifies the uniform increment of the independent variable (X-axis) between successive data samples. For uniformly-sampled time-series data, this is the sample period (reciprocal of sample rate). For frequency-domain data, it represents the frequency bin spacing.

#### 3.1.1.23 `bhversion`

The `bhversion` field indicates the version of the **BLUE** header format. Valid values are `1.0` (for **BLUE 1.0** compatible files) and `1.1` (for files utilizing **BLUE 1.1** features). Files written under this standard should use `1.1`.

#### 3.1.1.24 `bversion`

The `bversion` field is reserved for future use and should be set to spaces (ASCII 0x20).

#### 3.1.1.25 `keywords` (Main Header Keyword Area)

The `keywords` field is a 92-byte area within the Fixed Header that can hold a small number of short keyword-value pairs. These keyword entries follow a compact binary format as described in Table 8. This area is separate from the Extended Header keyword area and is used for a handful of standard keywords (see section 3.4.1).

**Table 8 — Main Header Keyword Format**

| Offset | Name | Type | Size (bytes) | Description |
|--------|------|------|-------------|-------------|
| 0 | `tag` | `char` | 1 | Single-character keyword tag identifying the keyword type |
| 1 | `lkey` | `uint_1` | 1 | Length of the keyword name (in bytes) |
| 2 | `lval` | `uint_1` | 1 | Length of the keyword value (in bytes) |
| 3 | `keyword` | `char` | `lkey` | Keyword name string (not null-terminated) |
| 3+lkey | `value` | (varies) | `lval` | Keyword value (encoding depends on keyword type) |

Keyword entries are packed contiguously within the 92-byte area. The area is terminated by a `tag` value of `0x00`. Unused bytes after the terminator are set to `0x00`.

Valid `tag` values include:
- `T`: Deprecated. Formerly used for results parameters. Readers should skip entries with tag `T` when the keyword is not one of the standard defined main header keywords.
- Standard tags are defined in section 3.4.1.

### 3.1.2 Variable (Adjunct) Header

The Variable (Adjunct) Header occupies bytes 256 through 511 of the Fixed Header area (i.e., the second 256 bytes of the first 512-byte block). Its interpretation depends on the file `type` field. Each file type defines its own adjunct structure, described in the subsections below.

For file types requiring more adjunct space than the default 256 bytes, the HCB is extended in 512-byte increments, and `data_start` is adjusted accordingly.

#### 3.1.2.1 Type 1000 Adjunct

Type 1000 files store one-dimensional data: a sequence of uniformly-spaced samples along a single axis (typically time or frequency).

**Table 9 — Type 1000 Adjunct Structure**

| Offset | Name | Type | Size (bytes) | Description |
|--------|------|------|-------------|-------------|
| 0 | `xunits` | `int_4` | 4 | Units code for the X axis (see Table 29) |
| 4 | `yunits` | `int_4` | 4 | Units code for the Y axis (data values) |
| 8 | `subsize` | `int_4` | 4 | Number of scalars per element (1 for scalar data, 2 for complex, N for vector) |
| 12 | `ydelta` | `real_8` | 8 | Reserved; not used for type 1000 |
| 20 | `ystart` | `real_8` | 8 | Reserved; not used for type 1000 |

The total number of data elements is `data_size / (element_size * subsize)`, where `element_size` is the byte size of the base format type.

For complex (`C`) formats, `subsize` is implicitly 2 (real and imaginary parts), and the actual `subsize` field in the adjunct should be set to 1. The total number of complex samples is `data_size / (2 * element_size)`.

For vector (`V`) format, `subsize` specifies the length of each vector.

##### 3.1.2.1.1 Type 1999 (Deprecated)

Type 1999 is a deprecated variant of type 1000. Files of this type should be treated identically to type 1000 files for reading purposes. Writing type 1999 files is not permitted under this standard.

#### 3.1.2.2 Type 2000 Adjunct

Type 2000 files store two-dimensional data as a raster (rows × columns of samples). Each row is a complete record with a fixed number of columns. The X axis corresponds to columns and the Y axis corresponds to rows.

**Table 10 — Type 2000 Adjunct Structure**

| Offset | Name | Type | Size (bytes) | Description |
|--------|------|------|-------------|-------------|
| 0 | `xunits` | `int_4` | 4 | Units code for the X axis (column axis) |
| 4 | `yunits` | `int_4` | 4 | Units code for the Y axis (row axis) |
| 8 | `subsize` | `int_4` | 4 | Number of elements per row (number of columns) |
| 12 | `ydelta` | `real_8` | 8 | Y-axis increment (row spacing) |
| 20 | `ystart` | `real_8` | 8 | Y-axis start value (value at first row) |

Data is stored in row-major order: all columns of row 0, then all columns of row 1, etc.

For **type 2200** (column-major) files, data is stored in column-major order (all rows of column 0, then all rows of column 1, etc.), and the meaning of the X and Y axes is transposed relative to type 2000.

The `xstart` and `xdelta` fields in the Fixed Header describe the X axis. The `ystart` and `ydelta` fields in the adjunct describe the Y axis.

*Note: In prior versions of this document, the semantics of `xstart` and `ystart` were documented in an interchanged manner. The correct interpretation is as stated here: `xstart`/`xdelta` describe the column (X) axis and `ystart`/`ydelta` describe the row (Y) axis.*

#### 3.1.2.3 Type 3000 Adjunct

Type 3000 files store record-based (non-homogeneous) data. Each record has a fixed size and contains multiple fields of potentially different types and sizes, as described by the adjunct structure.

**Table 11 — Type 3000 Adjunct Structure**

| Offset | Name | Type | Size (bytes) | Description |
|--------|------|------|-------------|-------------|
| 0 | `xunits` | `int_4` | 4 | Units code for the X axis |
| 4 | `yunits` | `int_4` | 4 | Units code for Y axis (not actively used; structural only) |
| 8 | `subsize` | `int_4` | 4 | Size of each record in bytes |
| 12 | `ydelta` | `real_8` | 8 | Not used for type 3000 |
| 20 | `ystart` | `real_8` | 8 | Not used for type 3000 |
| 28 | `r2units` | `int_4` | 4 | Units code for secondary axis (present for structural reasons only; not used) |
| 32 | `nrecords` | `int_4` | 4 | Number of records (may be 0 if unknown at write time) |
| 36 | `subrecords` | `int_4` | 4 | Number of subrecord field definitions |
| 40 | `recapture` | `int_4` | 4 | Reserved |
| 44 | `srec` | array | 200 | Array of subrecord field descriptors (see Table 13) |

*Note: `r2units` in type 3000 files is present for structural reasons only and does not carry meaningful information.*

##### 3.1.2.3.1 Extended Type 3000

When the number of subrecord definitions exceeds what fits in the standard 256-byte adjunct area, an Extended Type 3000 header is used. This requires extending the HCB beyond 512 bytes.

**Table 12 — Extended Type 3000 Adjunct Structure**

| Offset | Name | Type | Size (bytes) | Description |
|--------|------|------|-------------|-------------|
| 0 | `xunits` | `int_4` | 4 | Units code for the X axis |
| 4 | `yunits` | `int_4` | 4 | Units code for Y axis |
| 8 | `subsize` | `int_4` | 4 | Size of each record in bytes |
| 12 | `ydelta` | `real_8` | 8 | Not used |
| 20 | `ystart` | `real_8` | 8 | Not used |
| 28 | `r2units` | `int_4` | 4 | Units code for secondary axis (structural only) |
| 32 | `nrecords` | `int_4` | 4 | Number of records |
| 36 | `subrecords` | `int_4` | 4 | Number of subrecord field definitions |
| 40 | `recapture` | `int_4` | 4 | Reserved |
| 44 | `srec` | array | (variable) | Extended array of subrecord field descriptors |

**Table 13 — SUBRECSTRUCT: Subrecord Field Descriptor**

| Offset | Name | Type | Size (bytes) | Description |
|--------|------|------|-------------|-------------|
| 0 | `name` | `char` | 4 | Field name (short, 4-character, space-padded) |
| 4 | `format` | `char` | 2 | Data format code for this field (see section 3.1.1.12) |
| 6 | `units` | `int_2` | 2 | Units code for this field (see Table 29) |
| 8 | `offset` | `int_4` | 4 | Byte offset of this field within a record |
| 12 | `subsize` | `int_4` | 4 | Number of elements of the given format in this field |

##### 3.1.2.3.2 Type 3999 (Deprecated)

Type 3999 is a deprecated variant of type 3000. Files of this type should be treated identically to type 3000 files for reading purposes. Writing type 3999 files is not permitted under this standard.

#### 3.1.2.4 Type 4000 Adjunct

Type 4000 files contain a stream of keyword-format blocks, each identical in structure to an Extended Header segment. This type is well-suited to streaming metadata or event annotations.

**Table 14 — Type 4000 Adjunct Structure**

| Offset | Name | Type | Size (bytes) | Description |
|--------|------|------|-------------|-------------|
| 0 | `xunits` | `int_4` | 4 | Units code for the X axis |
| 4 | `reserved` | `char` | 252 | Reserved; set to `0` |

##### 3.1.2.4.1 Variable Record Structure

Each "record" in a type 4000 file is a variable-length keyword block. The structure of each block is identical to that used in the Extended Header (see section 3.3).

**Table 15 — Type 4000 Variable Record Structure**

| Field | Type | Description |
|-------|------|-------------|
| `lkey` | `int_4` | Total length of keyword block in bytes (including this length field) |
| `keywords` | binary | Keyword entries in Extended Header binary format |
| padding | `char` | Padding bytes (null) to align next record to a 4-byte boundary |

##### 3.1.2.4.2 T4INDEX Keyword

The **T4INDEX** keyword, when present in the Extended Header of a type 4000 file, provides an index into the keyword blocks stored in the Data Block. It contains a list of byte offsets (from the start of the Data Block) pointing to the start of each keyword block, facilitating random access.

##### 3.1.2.4.3 Known Type 4000 Extensions

Producers of type 4000 files may define application-specific keyword extensions. Known extensions include event-based data annotations used in conjunction with type 1000 data files.

#### 3.1.2.5 Type 5000 Adjunct

Type 5000 files are an enhanced version of type 3000 files, adding units and frame-of-reference information to subrecords. They were designed for generic system modeling applications.

**Table 16 — Type 5000 Adjunct Structure**

| Offset | Name | Type | Size (bytes) | Description |
|--------|------|------|-------------|-------------|
| 0 | `xunits` | `int_4` | 4 | Units code for the X axis |
| 4 | `yunits` | `int_4` | 4 | Units code for Y axis |
| 8 | `subsize` | `int_4` | 4 | Size of each record in bytes |
| 12 | `ydelta` | `real_8` | 8 | Not used |
| 20 | `ystart` | `real_8` | 8 | Not used |
| 28 | `r2units` | `int_4` | 4 | Units code for secondary axis |
| 32 | `nrecords` | `int_4` | 4 | Number of records |
| 36 | `subrecords` | `int_4` | 4 | Number of component definitions |
| 40 | `recapture` | `int_4` | 4 | Reserved |
| 44 | `comp` | array | 200 | Array of component descriptors (see Table 18) |

##### 3.1.2.5.1 Extended Type 5000

When the number of component definitions exceeds the space available in the standard adjunct, an Extended Type 5000 header is used, analogous to Extended Type 3000.

**Table 17 — Extended Type 5000 Adjunct Structure**

| Offset | Name | Type | Size (bytes) | Description |
|--------|------|------|-------------|-------------|
| 0 | `xunits` | `int_4` | 4 | Units code for the X axis |
| 4 | `yunits` | `int_4` | 4 | Units code for Y axis |
| 8 | `subsize` | `int_4` | 4 | Size of each record in bytes |
| 12 | `ydelta` | `real_8` | 8 | Not used |
| 20 | `ystart` | `real_8` | 8 | Not used |
| 28 | `r2units` | `int_4` | 4 | Units code for secondary axis |
| 32 | `nrecords` | `int_4` | 4 | Number of records |
| 36 | `subrecords` | `int_4` | 4 | Number of component definitions |
| 40 | `recapture` | `int_4` | 4 | Reserved |
| 44 | `comp` | array | (variable) | Extended array of component descriptors |

**Table 18 — COMPSTRUCT: Component Descriptor**

| Offset | Name | Type | Size (bytes) | Description |
|--------|------|------|-------------|-------------|
| 0 | `name` | `char` | 4 | Component name (4-character, space-padded) |
| 4 | `format` | `char` | 2 | Data format code for this component |
| 6 | `units` | `int_2` | 2 | Units code for this component |
| 8 | `offset` | `int_4` | 4 | Byte offset of this component within a record |
| 12 | `subsize` | `int_4` | 4 | Number of elements of the given format in this component |
| 16 | `type_code` | `int_4` | 4 | Type code (see Table 19) |
| 20 | `frame_of_ref` | `int_4` | 4 | Frame of reference code (see Table 21) |
| 24 | `category` | `int_4` | 4 | Category code (see Table 22) |
| 28 | `comp_def` | `int_4` | 4 | Component definition code (see Table 23) |

**Table 19 — Type 5000 Component Type Codes**

| Code | Description |
|------|-------------|
| 0 | Unspecified / generic |
| 1 | Position |
| 2 | Velocity |
| 3 | Acceleration |
| 4 | Attitude (orientation) |
| 5 | Angular velocity |
| 6 | Angular acceleration |
| 7 | Force |
| 8 | Torque |
| 9 | Temperature |
| 10 | Pressure |
| 11 | Power |
| 12 | Energy |
| 13 | Mass |
| 14 | Time |

**Table 20 — Type 5001/5010 Quadword Fields**

| Offset | Name | Type | Size (bytes) | Description |
|--------|------|------|-------------|-------------|
| 0 | `time` | `real_8` | 8 | Time of record (seconds from epoch) |
| 8 | `pad` | `int_4` | 4 | Padding (reserved, set to 0) |
| 12 | `flags` | `int_4` | 4 | Flag bits |

**Table 21 — `frame_of_ref` Values**

| Value | Description |
|-------|-------------|
| 0 | Unspecified / not applicable |
| 1 | Earth-Centered Inertial (ECI) |
| 2 | Earth-Centered Earth-Fixed (ECEF) |
| 3 | North-East-Down (NED) |
| 4 | Body frame |
| 5 | Vehicle frame |
| 6 | Local level frame |

##### 3.1.2.5.2 RAGW Algorithm

The RAGW (Rounding And Gain/Width) algorithm is used in type 5000 files for certain floating-point compression schemes. The algorithm operates as follows:

```
RAGW Algorithm:
  Given:
    value    - the floating-point value to encode
    gain     - scale factor
    width    - bit width of the encoded integer
    offset   - bias value

  Encode:
    encoded = round((value - offset) / gain)
    encoded = clamp(encoded, -(2^(width-1)), (2^(width-1)) - 1)

  Decode:
    decoded = (encoded * gain) + offset

  Notes:
    - 'gain' must be nonzero and positive
    - 'width' specifies the number of bits in the signed integer representation
    - The encoded value is stored as a signed integer of the specified width
    - Values outside the representable range are clamped to the extremes
```

**Table 22 — Type 5000 Component Categories**

| Code | Description |
|------|-------------|
| 0 | Unspecified |
| 1 | Scalar |
| 2 | Cartesian vector (X, Y, Z components) |
| 3 | Spherical coordinate |
| 4 | Quaternion |
| 5 | Euler angles |
| 6 | Matrix |

**Table 23 — Type 5000 Component Definitions**

| Code | Description |
|------|-------------|
| 0 | Unspecified |
| 1 | X component |
| 2 | Y component |
| 3 | Z component |
| 4 | W component (quaternion scalar) |
| 5 | Magnitude |
| 6 | Azimuth |
| 7 | Elevation |
| 8 | Range |
| 9 | Roll |
| 10 | Pitch |
| 11 | Yaw |

##### 3.1.2.5.29 FLAG Component

The **FLAG** component is a special component definition used in type 5000 files to store per-record status or quality flags.

**Table 24 — FLAG Component Definition**

| Field | Value | Description |
|-------|-------|-------------|
| `name` | `FLAG` | Component name |
| `format` | `LU` | Unsigned 32-bit integer |
| `units` | 0 | Dimensionless |
| `type_code` | 0 | Unspecified |
| `frame_of_ref` | 0 | Not applicable |
| `category` | 1 | Scalar |
| `comp_def` | 0 | Unspecified |

Flag bit assignments within the **FLAG** component are application-defined. Producers should document their flag bit assignments.

#### 3.1.2.6 Type 6000 Adjunct

Type 6000 files (and their variants 6001–6080) store descriptor word files, most commonly pulse descriptor words (PDWs). This type was introduced by MARTES and subsequently adopted by X-Midas.

The Type 6000 adjunct structure is defined by a compound header that includes a record descriptor and optional metadata about the subrecord fields. The column definitions for the type 6000 subrecords are provided through the **SUBREC_DEF** and **SUBREC_DESCRIP** keywords in the Extended Header.

**Table 25 — TYPE0 Subrecord Field Definitions (Standard Column Names)**

All fields in this table are stored as ASCII text in the **SUBREC_DEF** keyword unless otherwise noted.

| Column Name | Type | Description |
|-------------|------|-------------|
| `ADDRESS` | `char` | Platform or source address identifier |
| `AMP` | `real_8` | Amplitude |
| `ANGLE` | `real_8` | Angle measurement |
| `ANTENNA` | `char` | Antenna identifier |
| `AOA` | `real_8` | Angle of arrival |
| `AOA_AZ` | `real_8` | Angle of arrival, azimuth |
| `AOA_EL` | `real_8` | Angle of arrival, elevation |
| `ATID` | `char` | Attribute ID |
| `AZ` | `real_8` | Azimuth |
| `BW` | `real_8` | Bandwidth |
| `CARRIER` | `real_8` | Carrier frequency |
| `CF` | `real_8` | Center frequency |
| `CHANNEL` | `int_4` | Channel number |
| `CLASS` | `char` | Classification or class label |
| `CODE` | `char` | Code identifier |
| `CONF` | `real_8` | Confidence value |
| `CORR` | `real_8` | Correlation value |
| `CW` | `int_4` | Continuous wave flag |
| `DELAY` | `real_8` | Time delay |
| `DEPTH` | `real_8` | Depth measurement |
| `DEVCODE` | `char` | Device code |
| `DOPPLER` | `real_8` | Doppler shift |
| `DROOP` | `real_8` | Pulse droop |
| `DT` | `real_8` | Delta time (time interval) |
| `DUR` | `real_8` | Duration |
| `DUTY` | `real_8` | Duty cycle |
| `ELEV` | `real_8` | Elevation angle |
| `EL` | `real_8` | Elevation (short form) |
| `EMITTER` | `char` | Emitter identifier |
| `ENERGY` | `real_8` | Energy value |
| `ERP` | `real_8` | Effective radiated power |
| `FILTER` | `char` | Filter identifier |
| `FREQ` | `real_8` | Frequency |
| `GAIN` | `real_8` | Gain |
| `ID` | `char` | Generic identifier |
| `IFM` | `real_8` | Instantaneous frequency measurement |
| `INDEX` | `int_4` | Record index |
| `LAT` | `real_8` | Latitude |
| `LAT_SIGMA` | `real_8` | Latitude uncertainty (sigma) |
| `LON` | `real_8` | Longitude |
| `LON_SIGMA` | `real_8` | Longitude uncertainty (sigma) |
| `MODN` | `int_4` | Modulation type number |
| `NF` | `real_8` | Noise figure |
| `NOISE` | `real_8` | Noise level |
| `NUM` | `int_4` | Count or number |
| `PA` | `real_8` | Phase angle |
| `PLATFORM` | `char` | Platform identifier |
| `POL` | `char` | Polarization |
| `POWER` | `real_8` | Power |
| `PRI` | `real_8` | Pulse repetition interval |
| `PRF` | `real_8` | Pulse repetition frequency |
| `PULSE_AMP` | `real_8` | Pulse amplitude |
| `PULSE_FREQ` | `real_8` | Pulse frequency |
| `PULSE_ID` | `int_4` | Pulse identifier |
| `PULSE_W` | `real_8` | Pulse width |
| `PW` | `real_8` | Pulse width (short form) |
| `QUALITY` | `real_8` | Quality metric |
| `RANGE` | `real_8` | Range measurement |
| `RANGE_SIGMA` | `real_8` | Range uncertainty (sigma) |
| `RASTER` | `int_4` | Raster identifier |
| `RCS` | `real_8` | Radar cross section |
| `RECEIVER` | `char` | Receiver identifier |
| `RISE` | `real_8` | Rise time |
| `SCAN` | `real_8` | Scan period or angle |
| `SENSOR` | `char` | Sensor identifier |
| `SEQ` | `int_4` | Sequence number |
| `SNR` | `real_8` | Signal-to-noise ratio |
| `SOURCE` | `char` | Source identifier |
| `SPEED` | `real_8` | Speed |
| `STATUS` | `int_4` | Status word |
| `SUBPULSE` | `int_4` | Subpulse identifier |
| `SYS_TIME` | `real_8` | System time (seconds from epoch) |
| `TEMP` | `real_8` | Temperature |
| `TIME` | `real_8` | Time (seconds from epoch) |
| `TRACK` | `int_4` | Track number |
| `TYPE` | `char` | Type identifier |
| `UNIT` | `char` | Unit identifier |
| `VEL` | `real_8` | Velocity |
| `VEL_X` | `real_8` | X-component of velocity |
| `VEL_Y` | `real_8` | Y-component of velocity |
| `VEL_Z` | `real_8` | Z-component of velocity |
| `WEIGHT` | `real_8` | Weight or weighting factor |
| `WIDTH` | `real_8` | Width measurement |
| `X` | `real_8` | X coordinate |
| `XTAL_DIV` | `real_8` | Crystal divider value |
| `Y` | `real_8` | Y coordinate |
| `Z` | `real_8` | Z coordinate |

##### 3.1.2.6.1 Long Column Names

When column names exceed 4 characters, they are stored using a long-name extension mechanism. In this case, the short name field in the subrecord structure is set to a special marker, and the full name is provided in the **SUBREC_DEF** or **SUBREC_DESCRIP** keyword.

Example — Long column name encoding in **SUBREC_DEF**:

```
SUBREC_DEF = "TIME:real_8, FREQ:real_8, PULSE_WIDTH:real_8, AMPLITUDE:real_8"
```

In this example, `PULSE_WIDTH` and `AMPLITUDE` are long column names (more than 4 characters). Readers must use the **SUBREC_DEF** keyword rather than the short names embedded in the binary subrecord structure when long names are present.

---

### 3.2 Data Block

The Data Block contains the primary data of the file. It begins at the byte offset indicated by `data_start × 512` and has a size of `data_size` bytes.

The format and interpretation of the data depends on the file `type` and `format` fields:

- For **type 1000** files, the data is a flat sequence of uniformly-spaced samples. The number of samples is `data_size / (subsize × element_size)`.
- For **type 2000** files, the data is stored row-major as a two-dimensional raster. Each row has `subsize` elements.
- For **type 3000** files, the data is a sequence of fixed-size records. Each record is `subsize` bytes and contains the fields described in the adjunct.
- For **type 4000** files, the data is a sequence of variable-length keyword blocks (see section 3.1.2.4.1).
- For **type 5000** files, the data is similar to type 3000 but with component metadata from the adjunct.
- For **type 6000** files, the data is a sequence of variable-length descriptor word records.

Data elements are stored in the byte order and floating-point representation indicated by the `data_rep` field (see section 3.1.1.3).

The Data Block is padded with null bytes at the end to the next 512-byte boundary (for files that are not in pipe mode). The `data_size` field gives the actual number of valid data bytes, excluding any padding.

---

### 3.3 Extended Header

The Extended Header contains keyword-based metadata. It begins at the byte offset `ext_start × 512` and has a size of `ext_size` bytes. If `ext_start` is `0`, there is no Extended Header.

The Extended Header consists of a sequence of binary keyword entries. Each keyword entry follows the format described in Table 26.

**Table 26 — Binary Keyword Structure**

| Offset | Name | Type | Size (bytes) | Description |
|--------|------|------|-------------|-------------|
| 0 | `lkey` | `uint_1` | 1 | Length of the keyword name string in bytes (1–255) |
| 1 | `lval` | `uint_4` | 4 | Length of the keyword value data in bytes |
| 5 | `type` | `uint_1` | 1 | Keyword value type code (see below) |
| 6 | `format` | `char` | 1 | Format code (same as data format; see section 3.1.1.12) |
| 7 | `keyword` | `char` | `lkey` | Keyword name string (not null-terminated) |
| 7+lkey | `value` | (varies) | `lval` | Keyword value data |

The `type` field in the keyword entry indicates the type of value stored:

| Type Code | Description |
|-----------|-------------|
| `A` (0x41) | ASCII string value |
| `B` (0x42) | Binary (numeric) value |
| `O` (0x4F) | Object (structured binary value) |
| `S` (0x53) | String (Unicode, Modified UTF-8) |
| `X` (0x58) | Extended (application-specific) |

Keyword entries are packed contiguously within the Extended Header area. There is no explicit terminator; the end of the Extended Header is determined by the `ext_size` field.

Keyword names are case-insensitive for matching purposes, but are typically stored in uppercase by convention.

#### 3.3.1 MARTES ASCII Keywords (Deprecated)

Prior to 2005, MARTES systems used an ASCII keyword format in the Extended Header area. In this format, keywords were stored as newline-delimited `NAME=VALUE` text lines within the Extended Header. This format is only recognized under **BLUE 1.0** and is not supported in **BLUE 1.1**. Systems encountering this format should attempt to parse the ASCII keyword lines but may issue a compatibility warning.

---

### 3.4 Standard BLUE Keywords

This section defines the standard keywords defined for use in **BLUE 1.1** files. Keywords may appear in the Main Header keyword area (section 3.1.1.25) or in the Extended Header (section 3.3), as indicated for each keyword.

#### 3.4.1 Main Header Keywords

The following keywords may appear in the 92-byte Main Header keyword area.

##### 3.4.1.1 CREATOR

**CREATOR** identifies the software component that created the **BLUE** file.

| Property | Value |
|----------|-------|
| Location | Main Header keyword area |
| Format | ASCII string |
| Example | `CREATOR = "NeXtMidas 3.0"` |

The value is a free-form ASCII string identifying the creating application, version, and optionally the host. The **CREATOR** keyword assists in diagnosing compatibility issues and tracking data provenance.

##### 3.4.1.2 IO

**IO** specifies the I/O configuration or connection used when the file was created.

**Table 27 — IO Keyword Values**

| Value | Description |
|-------|-------------|
| `R` | Read (input) |
| `W` | Write (output) |
| `RW` | Read/write |
| `SINK` | Data sink (no output) |
| `SOURCE` | Data source (no input) |

The **IO** keyword is primarily used for operational bookkeeping and may indicate the direction of data flow in a processing chain.

##### 3.4.1.3 PACKET

**PACKET** indicates that the data in this file is organized into packets. When present, it identifies the packet format or protocol.

| Property | Value |
|----------|-------|
| Location | Main Header keyword area |
| Format | ASCII string |
| Example | `PACKET = "CCSDS"` |

##### 3.4.1.4 PKT_BYTE_COUNT

**PKT_BYTE_COUNT** specifies the number of bytes in each packet when the file contains packetized data.

| Property | Value |
|----------|-------|
| Location | Main Header keyword area |
| Format | Integer (stored as ASCII or binary) |
| Example | `PKT_BYTE_COUNT = 1024` |

##### 3.4.1.5 TC_PREC

**TC_PREC** (timecode precision) provides a sub-`real_8` precision offset to be added to the `timecode` field in the Fixed Header. This allows for greater time precision than the `real_8` `timecode` field alone can provide.

| Property | Value |
|----------|-------|
| Location | Main Header keyword area |
| Format | `real_8` (double-precision floating-point) |
| Units | Seconds |

The precise time of the first sample is: `timecode + TC_PREC`.

##### 3.4.1.6 VER

**VER** records the version of the software or data standard associated with the file.

**Table 28 — VER Keyword Usage**

| Property | Value |
|----------|-------|
| Location | Main Header keyword area |
| Format | ASCII string |
| Example | `VER = "1.1.0"` |

The **VER** keyword is distinct from the `bhversion` field in the Fixed Header. While `bhversion` records the **BLUE** format version, **VER** records the version of the application-level data standard or software version associated with the content.

#### 3.4.2 Extended Header Keywords

The following keywords may appear in the Extended Header.

##### 3.4.2.1 ACQDATE

**ACQDATE** stores the acquisition date as a human-readable string.

| Property | Value |
|----------|-------|
| Location | Extended Header |
| Format | ASCII string |
| Example | `ACQDATE = "2013-11-18"` |

The format is ISO 8601 date (`YYYY-MM-DD`) by convention.

##### 3.4.2.2 ACQTIME

**ACQTIME** stores the acquisition time as a human-readable string.

| Property | Value |
|----------|-------|
| Location | Extended Header |
| Format | ASCII string |
| Example | `ACQTIME = "11:26:22.000"` |

The format is `HH:MM:SS.sss` (24-hour clock, UTC) by convention. Together with **ACQDATE**, this provides a human-readable timestamp of the acquisition.

##### 3.4.2.3 COMMENT

**COMMENT** is a free-form text field for arbitrary notes about the file content.

| Property | Value |
|----------|-------|
| Location | Extended Header |
| Format | ASCII string |
| Example | `COMMENT = "Test collection run 3, antenna boresight at 45 deg"` |

Multiple **COMMENT** keywords may appear in the Extended Header; each is treated as a separate annotation.

##### 3.4.2.4 SUBREC_DEF

**SUBREC_DEF** provides the column name and type definitions for type 3000 or type 6000 subrecord fields.

| Property | Value |
|----------|-------|
| Location | Extended Header |
| Format | ASCII string |
| Example | `SUBREC_DEF = "TIME:real_8, FREQ:real_8, AMP:real_4"` |

The value is a comma-separated list of `name:type` pairs, where `name` is the column name and `type` is the data format string (using Midas type notation). This keyword is especially important for type 6000 files with long column names (see section 3.1.2.6.1).

##### 3.4.2.5 SUBREC_DESCRIP

**SUBREC_DESCRIP** provides human-readable descriptions of the subrecord fields.

| Property | Value |
|----------|-------|
| Location | Extended Header |
| Format | ASCII string |
| Example | `SUBREC_DESCRIP = "TIME: Time of pulse arrival; FREQ: Center frequency; AMP: Peak amplitude"` |

##### 3.4.2.6 T4INDEX

**T4INDEX** appears in the Extended Header of type 4000 files and contains an index of byte offsets for keyword blocks in the Data Block.

| Property | Value |
|----------|-------|
| Location | Extended Header |
| Format | Binary array of `real_8` values |
| Description | Each element is a byte offset from the start of the Data Block to a keyword block |

##### 3.4.2.7 TIMELINE

**TIMELINE** stores a structured description of the timing relationship between samples and real-world time. It is used when the `timecode` field alone is insufficient to describe the time base.

| Property | Value |
|----------|-------|
| Location | Extended Header |
| Format | ASCII string or structured binary |
| Example | `TIMELINE = "START=1234567890.0, RATE=1000.0, EPOCH=J1950"` |

The **TIMELINE** keyword is particularly useful when describing data with non-uniform sampling, gap-filled sequences, or complex time bases.

---

### 3.5 Other Constants

#### 3.5.1 Unit Codes

Unit codes are stored in the `xunits`, `yunits`, and field-level `units` fields. The following table defines the standard unit code values.

**Table 29 — Unit Codes**

| Code | Symbol | Unit Name |
|------|--------|-----------|
| 0 | — | Unknown / not specified |
| 1 | `U` | User-defined |
| 2 | `s` | Seconds (time) |
| 3 | `m` | Meters (length) |
| 4 | `m/s` | Meters per second (velocity) |
| 5 | `m/s²` | Meters per second squared (acceleration) |
| 6 | `Hz` | Hertz (frequency) |
| 7 | `rad` | Radians (angle) |
| 8 | `deg` | Degrees (angle) |
| 9 | `rad/s` | Radians per second (angular velocity) |
| 10 | `deg/s` | Degrees per second (angular velocity) |
| 11 | `rad/s²` | Radians per second squared (angular acceleration) |
| 12 | `deg/s²` | Degrees per second squared (angular acceleration) |
| 13 | `K` | Kelvin (temperature) |
| 14 | `°C` | Degrees Celsius (temperature) |
| 15 | `°F` | Degrees Fahrenheit (temperature) |
| 16 | `kg` | Kilograms (mass) |
| 17 | `g` | Grams (mass) |
| 18 | `lb` | Pounds (mass) |
| 19 | `N` | Newtons (force) |
| 20 | `lbf` | Pounds-force (force) |
| 21 | `Pa` | Pascals (pressure) |
| 22 | `bar` | Bar (pressure) |
| 23 | `psi` | Pounds per square inch (pressure) |
| 24 | `J` | Joules (energy) |
| 25 | `cal` | Calories (energy) |
| 26 | `eV` | Electron-volts (energy) |
| 27 | `W` | Watts (power) |
| 28 | `dBm` | Decibels relative to 1 mW (power level) |
| 29 | `dBW` | Decibels relative to 1 W (power level) |
| 30 | `V` | Volts (voltage) |
| 31 | `A` | Amperes (current) |
| 32 | `Ω` | Ohms (resistance) |
| 33 | `F` | Farads (capacitance) |
| 34 | `H` | Henries (inductance) |
| 35 | `T` | Tesla (magnetic flux density) |
| 36 | `Wb` | Webers (magnetic flux) |
| 37 | `C` | Coulombs (electric charge) |
| 38 | `lm` | Lumens (luminous flux) |
| 39 | `lx` | Lux (illuminance) |
| 40 | `cd` | Candela (luminous intensity) |
| 41 | `mol` | Moles (amount of substance) |
| 42 | `sr` | Steradians (solid angle) |
| 43 | `Bq` | Becquerels (radioactivity) |
| 44 | `Gy` | Grays (absorbed dose) |
| 45 | `Sv` | Sieverts (dose equivalent) |
| 46 | `kat` | Katals (catalytic activity) |
| 47 | `bit` | Bits (information) |
| 48 | `B` | Bytes (information) |
| 49 | `b/s` | Bits per second (data rate) |
| 50 | `B/s` | Bytes per second (data rate) |
| 51 | `km` | Kilometers (length) |
| 52 | `nmi` | Nautical miles (length) |
| 53 | `ft` | Feet (length) |
| 54 | `mi` | Miles (length) |
| 55 | `kHz` | Kilohertz (frequency) |
| 56 | `MHz` | Megahertz (frequency) |
| 57 | `GHz` | Gigahertz (frequency) |
| 58 | `dB` | Decibels (ratio, logarithmic) |
| 59 | `dBsm` | Decibels relative to 1 m² (radar cross section) |
| 60 | `m²` | Square meters (area) |
| 61 | `m³` | Cubic meters (volume) |
| 62 | `km/h` | Kilometers per hour (speed) |
| 63 | `kn` | Knots (speed) |

---

## 4 Document Information

| Field | Value |
|-------|-------|
| Document | CCD-2006002 (Midas BLUE File Format) |
| Version | 1.1.0 2013-11-18 11:26:22 |
| Revision | `$Id: blue.txt 316 2013-11-18 18:20:08Z sms $` |
| Release Date | Released (November 18, 2013) |
| CCD Manager | Michelle Fromm, Rincon Research, 871-7310 |
| Document Owner | Michelle Fromm, Rincon Research, CAM Program Manager, 871-7310 |

### 4.1 Revision History

- **17-Mar-2006 (1.0.0):** Original release of CCD-2006002-1.0.0
- **17-Aug-2008 (1.0.1):** Converted from Microsoft Word to restructured text to simplify generation of PDF and HTML from single source. No content changes.
- **17-Aug-2008 (175):** Correct the following errors:
  - Assorted typographical errors
  - Size of `keywords` field in Table 2
  - **IEEE** floating point standard number in Table 3
  - Type of `padding` in Table 20
  - All fields in Table 25 are ASCII text; the table erroneously listed their decoded types. Also correct table name to reflect that it represents `TYPE0` subrecords
  - Semantics of `xstart` and `ystart` were interchanged in section 3.1.2.2.1
  - Refine the description of the Data and Extended Header blocks in section 3 to address order and spacing
  - Reword legacy floating point text in section 3.1.1.2
  - Note semantic extensions in section 3.1.1.4
  - Add type 5001 to Table 4
  - In section 3.1.1.12.2, mention unsupported unsigned integer types, clarify packed bit encoding, and note padding expectations for variable-length ASCII strings
  - Clarify the relationship between `timecode`, `xstart`, and **TC_PREC** in section 3.1.1.14
  - Provide details on the **FLAG** component in section 3.1.2.5.29
  - Add main header keywords **CREATOR** (3.4.1.1), **IO** (3.4.1.2), **PACKET** (3.4.1.3), **PKT_BYTE_COUNT** (3.4.1.4), and **VER** (3.4.1.6), and extended header keyword **TIMELINE** (3.4.2.7)
  - Note difference in NeXtMidas representation and processing of **SP** data in section 3.1.1.12.2
  - Internal review release as CCD-200602-1.0.1
- **12-Sep-2008 (187):**
  - Uniformly use decimal values for offsets in tables
  - Further clarifications of some updates from 175
- **10-Dec-2008 (213):**
  - Update units table to use correct SI unit symbols
  - Add references to Platinum
- **20-Jan-2010 (309):**
  - Note that `r2units` in type 3000 files is present for structural reasons only
  - Make HTML formatting prettier
  - Release as CCD-2006002-1.0.2
- **18-Nov-2013 (316):**
  - Updated info for file types 1200 and 2200
  - Various keyword information added, specifically for **IO**, **VER**, and **CREATOR**
  - Clarification of deprecated keyword code `T` with respect to results parameters stored in a **BLUE** file
  - Numerous updates from the NeXtMidas team
  - Release as CCD-2006002-1.1.0
