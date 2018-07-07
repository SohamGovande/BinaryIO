# Binary-IO
C++ library for binary file input and output that handles the problem of system endianness!

Often, the C++ programming language is used to create efficient programs, and it's necessary to rapidly read and write data from a filesystem. Traditional ASCII-based formats such as XML, JSON, or Properties, albeit trouble-free, are verbose - thereby occupying more disk space - and take vastly longer time to parse into structures within a program. Binary files, on the other hand, are compact, fast, and are easily convertible into program structures.

Binary-IO is a fast, efficient, and simple library you can use to do all this.

---

**Endianness**

Endianness is an OS property that determines whether the first byte has the highest order or the last one does. For instance, the 4-byte set 01 00 00 00 could mean 16777216, like on big-endian systems, or just 1, on little-endian systems, depending on whether the 1 has the place value 0 or 6. When dealing with binary files, it's important to deal with this correctly or bytes could be read in reversed order, thereby representing a different value and corrupting data.

Binary-IO handles this by efficiently reversing input the order of read bytes if on a big-endian systemm, or if the system is little-endian, writing bytes in their raw order.

**Block IO**

In topics such as 3d model storage and loading, it's important to minimize overhead time, so Binary-IO implements a functionality called Block IO which lets entire blocks of data to be written at once.

---

**Examples**

Writing data:
```cpp
BinaryWriter writer("ImageData.bin");
//Sign the binary with a tag and a version so that it'll be easy to detect if we're reading an old version
writer.writeHeader("img", Version(1, 0, 0));
//Write the width and height
writer.write<unsigned int>(width);
writer.write<unsigned int>(height);
//Send the image pixel data to be written
//Previously defined: float* pixelData = ...;
writer.writeBlock<float, unsigned int>(
	pixelData,
	width * height * 3 //Every pixel has 3 color channels
);
writer.close(); //Optional
```
Reading data:
```cpp
BinaryReader reader("ImageData.bin");
//Ensure that the file is the right type and of the right version, or else discard it
reader.ensureHeader("img", Version(1, 0, 0));
//Load dimensions, then once we know the size, read the pixel data
unsigned int width = reader.read<unsigned int>();
unsigned int height = reader.read<unsigned int>();
float* pixelData = reader.readBlock<float, unsigned int>(width * height * 3);
reader.close(); //Optional
//Do stuff with width, height, and pixelData! :D
```
