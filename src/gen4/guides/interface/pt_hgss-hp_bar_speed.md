# How to change the HP Bar speed<sup>*(Platinum/HGSS)*</sup>
> This guide was written by AdAstra, formatted for this wiki by SpagoAsparago.
> Research: AdAstra, Mikelan98 and Nitram

This is a tutorial on how to change the HP Bar speed in gen 4 games.

--- 
## Table of Contents
* [Cy's Hex Macros](#section)
* [Hex Editing](#section-2)
  * [Extracting the files](#subsection)
  * [Changing the Speed](#subsection-1)
  * [Reinserting the files](#subsection-2)

## Cy's Hex Macros

For Platinum, if 2x speed is enough for your needs, you can use [Cy's Hex Macros](https://github.com/dev-cyw/Cy-s-Hex-Macros/releases/tag/v1.2).

Run the program and select the arm9.bin in your DSPRE's extracted folder, then click the appropriate button. 
When you load your project in DSPRE in order to export the ROM, remember to **not** re-extract it.


## Hex Editing
If you're using HGSS or want a different speed for Platinum, you need to hex edit the appropriate files. All offsets provided are based on the US ROM(s) and might be different on another version.

### Extracting the files

* Platinum:
Since Platinum overlays aren't compressed, you can just use the *overlay_0016.bin* file in the *YourROMNAme_DSPRE_contents/overlay* folder generated by DSPRE. Alternatively you can extract it with Tinke by searching *verlay_9_16* and clicking *Extract*.

* HGSS:
HGSS overlays are compressed instead, so you can use CrystalTile2 to extract them already decompressed.
After you've loaded your ROM, click the NDS icon in the top bar, it should open a window called *NDS filesystem information*. From there, select *overlay_0022.bin*, right click and select *Extract (U)*

### Changing the Speed

Open the overlay in a hex editor, then go to the following offset:

* HGSS: `0x2E17A`
* Platinum: `0x2D046`

Then, replace the bytes at this offset with the following sequence, depending on the speed you want to have:

<details> 
 <summary>HGSS</summary>
 
| Byte Sequence  | Speed |
| ------------- | ------------- |  
| 80 01 | x0.25 |
| C0 01 | x0.5  | 
| 40 02 | x2  | 
| 80 02 | x4  | 
| C0 02 | x8  | 

If you want a speed that is not listed above:
<details> 
 <summary>HGSS - Custom Speed</summary>
 
 1) You'll need to use either [Shell-storm online assembler and disassembler](https://shell-storm.org/online/Online-Assembler-and-Disassembler/) or an assembler like [Armips](https://github.com/Kingcom/armips)
 2) Assemble the instruction `LSLS R0, R0, #yourNumber`, with the default `#yourNumber` being 8, which means the base speed is `2^8 = 256`.
 3) Paste the assembled bytes in the overlay

</details>
 
</details>

<details> 
 <summary>Platinum</summary>
 
| Byte Sequence  | Speed |
| ------------- | ------------- |  
| 88 1E | x2 |
| C8 1E | x3  | 
| 08 1F | x4  | 
| 48 1F | x5  | 
| 88 1F | x6  | 
| C8 1F | x7  | 

</details>

If you want to fine-tune the speed Animation:

<details><summary>How to fine-tune the speed Animation (HGSS only):</summary>

1) If you haven't already, you need to expand the arm9 using DSPRE's toolbox
 
2) Paste this at 0x14FF0 of your synthetic overlay file:
```
2D 2D 48 50 20 42 41 52 20 53 50 45 45 44 2D 2D 
80 21 49 00 48 43 11 1C 70 47 C0 46 FF FF FF FF
```
* The first byte of the second row (default is `0x80`) represents the speed "fine control".
 
* `80 21 49 00` means the speed is 128\*2 = 256. [x1]
* `A0 21 49 00` means the speed is 160\*2 = 320. [x1.25]
* `C0 21 49 00` means the speed is 192\*2 = 384. [x1.5]
* ...up to `FF 21 49 00`, which means the speed is 255\*2 = 510. [x1.9921]
 
* The third byte of the second row (default is `0x49`) acts as a cumulative multiplier, or "coarse control". 

* Increasing that value will allow you to set the speed even higher.
 
* `60 21 89 00` means the speed is 96\*4 = 384. [x1.5]
* `80 21 89 00` means the speed is 128\*4 = 512. [x2]
* `EC 21 89 00` means the speed is 236\*4 = 944. [x3.6875]
* ...up to `FF 21 89 00`, which means the speed is 255\*4 = 1020. [x3.9843]
 
3) Paste `77 F1 D1 FA` at `0x2E17A` offset of uncompressed overlay12.

</details>

### Reinserting the files

* For Platinum, if you edited the overlay in DSPRE's content folder, just load your ROM in DSPRE and export it. Remember to **not** re-extract the contents. If you extracted the overlay with Tinke, just do the same steps you did for extracting it, but this time use the *Change File* button and select your extracted overlay you modified.
* For HGSS, in CrystalTile's NDS filesystem information window, you can select the overlay you previously extracted, right click and this time click *Compression*, then select your own hex edited overlay. After you're done you can use `File>Build ROM...` to save your ROM.