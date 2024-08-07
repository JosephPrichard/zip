# Zip
Compress and archive files into a custom zip format. 
Zip utilizes huffman coding to compress files, generally achieving a 60% compression ratio for the average text file. 
The compression works on any file type, but it works on text file types the best. 

The archival format is inspired by TAR but is custom designed for simplicity. 
Project involved implementing the huffman coding algorithm, memory safe binary trees, and bit-layered reader/writers. 

The project contains only rayon as a dependency and was primarily created to learn Rust's standard library.

## Compression Format
Each compressed file is broken into two segments: the tree segment and the compressed data segment. 
The tree segment is laid out using depth first traversal. An internal node is represented with a 0 bit, and a leaf node with a 1 bit. 

A leaf node is followed by the byte the bit code decompresses into.
The compressed data segment simply contains a bit sequence of each original byte compressed using the aforementioned tree.

## Archival Format
The archive file is broken up into two segments: the file header segment and the file data segment. 
The file header segment contains a block for each file in the archive. 

Each block contains a null-terminated relative path, the bit sizes of the tree and compressed data, the pre compression byte size, and the file offset which acts as a pointer to the actual compressed data stored in the file data segment. 

The file data segment contains each compressed file stored as a bit stream. 
The archive two segments are separated by control code GS, and each file header is separated by control code RS.

## Usage

### Compress
Compresses each file into an archive using the compression strategy described above. Recursively adds sub-directories to archive.

```shell
$ ./zip.exe -c ../path/to/directory ../path/to/file.txt
```

### Decompress
Decompresses the archive into the stored directory structure using the decompression strategy described above.

```shell
$ ./zip.exe -d ../path/to/archive.zipr
```

### List
Lists the sizes, compression ratios, and relative file name of any files in the archive. 

```shell
$ ./zip.exe -l ../path/to/archive.zipr
```

### Multithreading
Adds multithreaded parallelism using Rayon's threadpool. Uses the max number of cores as the thread pool count, although this isn't guaranteed to actually use all cores in the system (unless you have no other processes being executed).

```shell
$ ./zip.exe -c -mt ../path/to/directory
```

## Example

```shell
$ ./zip.exe -c -mt ./test/files
```
![image](https://github.com/user-attachments/assets/57e9778d-5c6d-4be6-9bc9-1faabc4b5fca)

