# Secret Document
## Description
"shell is the key if you did'nt get it xorry"
## File
[Secret-Document.dat]()
## Solution
Kiểm tra metadata của file thì ta thấy đây là một file không xác định được loại file
```
┌──(kali㉿kali)-[~/Documents/shellCTF2022/Secret_Document]
└─$ exiftool Secret-Document.dat 
ExifTool Version Number         : 12.44
File Name                       : Secret-Document.dat
Directory                       : .
File Size                       : 352 kB
File Modification Date/Time     : 2022:08:12 05:57:49-04:00
File Access Date/Time           : 2022:08:15 11:29:33-04:00
File Inode Change Date/Time     : 2022:08:12 22:17:45-04:00
File Permissions                : -rw-r--r--
Error                           : Unknown file type
```
Sử dụng hexedit để kiểm tra các header byte đầu tiên của file            
![image](https://user-images.githubusercontent.com/62021009/184665867-56662f76-bd19-4928-946c-03cf38162fa3.png)
ta thấy các byte đầu tiên của file này khá là lạ và gần như không có loại file nào bắt đầu bằng những file này. Lúc này, ta để ý đến description của bài, `shell` chính là key nếu như chưa đem đi xor. Đọc tới đây, có vẻ như toàn bộ các byte của file này đã được đem đi xor với `shell`. Như vậy để tìm lại được file gốc, ta sẽ lấy file này xor lại với `shell` vì tính chất của phép xor ` a xor b = c <=> b = c xor a`. Ta thử đem xor 5 bytes đầu tiên của file với xor xem thử:          
![image](https://user-images.githubusercontent.com/62021009/184666743-a035925a-e8b0-4558-bcc0-0618e66444cf.png)          
Ta thấy các byte `89 50 4e 47 0d` là các bytes đầu tiên của file png. Như vậy, ta viết đoạn script sau để xor toàn bộ file với `shell`
```python
cipher = open("Secret-Document.dat", "rb").read()
key = "shell"
result = open("solve.png", "wb")
for i in range(0, len(cipher),1):
        result.write((cipher[i] ^ ord(key[i % 5])).to_bytes(1, byteorder="big"))
```
Sau khi chạy đoạn chương trình trên, ta được một file [solve.png]() chứa flag
## Flag `shell{y0u_c4n_s33_th3_h1dd3n}
