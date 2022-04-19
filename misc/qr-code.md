# QR Code
Quick Response Code - 2D barcode.  
Consists of black squares arranged in a square grid on a white background, processed using Reed–Solomon error correction until the image can be appropriately interpreted.
## Components
- Positioning detection markers: To identify the presence of a QR Code and its orientation
- Alignment markings: To straighten out QR Codes drawn on a curved surface
- Timing pattern: To accurately configure and determine the size of the data grid
- Version information: Determine QR code version
- Format information: Determine error tolerance and data mask pattern
- Data and error correction keys: Has data and parity (error correction) information
- Quiet zone: To distinguish the code from its surroundings

![image](https://user-images.githubusercontent.com/45961072/156776188-819ce7a3-b7d2-490c-ae7d-a61dc230afca.png)

## Versions
There are 40 versions of QR codes; each increasing version is bigger and holds more data.  
Version 1 contains 21 rows and columns; each subsequent version increases rows and columns by 4.
Version 40 contains 177 rows and columns, which can store 3kb of data (`177 * 177 = 31,329 modules = 3kb`).  

## Levels of error correction
|Code|Level|Can withstand upto|
|:---:|:---:|:---:|
|L|Low|7% damage|
|M|Medium|15% damage|
|Q|Quality|25% damage|
|H|High|30% damage|

## Types of QR codes
- The normal QR code
- Micro QR code: Stores small amounts of data, designed for smaller components
- iQR code: Stores data more efficiently, supports rectangular modules, can be used on non-flat surfaces
- Frame QR code: Central area of the QR code is left blank for branding. Not backward compatible 
## References
https://www.qr-code-generator.com/qr-code-marketing/qr-codes-basics
https://qrcode.meetheed.com/question7.php