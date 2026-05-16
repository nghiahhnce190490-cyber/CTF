**Challenge** Information

**Level**: Medium

**Tags**: picoCTF 2026, Forensics

**Author**: NhanNghia

**Challenge** **Link**: picoCTF

**Alternative Solution (Timeline & Inode Analysis)**
**Step 1**:** Chuẩn bị môi trường và giải nén
Đầu tiên, tạo thư mục làm việc riêng và tiến hành giải nén file image đã tải xuống từ thử thách:**
Bash
┌──(kali㉿kali)-[~]
└─$ **mkdir Nghia && cd Nghia**

┌──(kali㉿kali)-[~/Nghia]
└─$ **wget https://challenge-files.picoctf.net/c_plain_mesa/aa1f8ba93409887e081435732d7037c45b30a8442853bf07c9e84fe4d0e0bc19/partition4.img.gz**

┌──(kali㉿kali)-[~/Nghia]
└─$ **gzip -d partition4.img.gz **
**
Kiểm tra định dạng của file sau khi giải nén bằng lệnh file:
Bash**

┌──(kali㉿kali)-[~/Nghia]
└─$ **file partition4.img**                       
partition4.img: Linux rev 1.0 ext4 filesystem data, UUID=7a00e9da-98f8-4f0f-b257-95edf422d902 (extents) (64bit) (large files) (huge files)


**Step 2**:** **Cài đặt công cụ phân tích cấu trúc Disk
Để phân tích sâu vào hệ thống tập tin này, chúng ta cần sử dụng bộ công cụ chuyên dụng sleuthkit. Tiến hành cài đặt trình quản lý gói nix để cấu hình môi trường nhanh chóng****:
Bash
┌──(kali㉿kali)-[~/Nghia]
└─$ **nix-shell -p sleuthkit**                                                                

**Step 3**: **Tạo Timeline hệ thống và phân tích MACb
Chúng ta tiến hành dựng lại dòng thời gian thay đổi của các file bên trong disk image bằng lệnh fls (để liệt kê danh sách file kèm metadata dạng body) và mactime (để đọc và sắp xếp lại theo thời gian thực tế)**:
Bash
┌──(kali㉿kali)-[~/Nghia]
└─$ **fls -r -m "/" partition4.img > body.txt **  

┌──(kali㉿kali)-[~/Nghia]
└─$ ****mactime -b body.txt -d > hello ** **       

**Đọc các dòng đầu tiên của tập tin timeline vừa tạo:**
Bash
┌──(kali㉿kali)-[~/Nghia]
└─$ **head hello**

Date,Size,Type,Mode,UID,GID,Meta,File Name
Tue Jan 01 1985 12:00:00,41,macb,r/rrw-r--r--,0,0,4945,"/bin/bcab"
Mon Oct 18 2021 13:54:17,451,ma..,r/rrw-r--r--,0,0,64994,"/usr/share/apk/keys/alpine-devel@lists.alpinelinux.o

**Phát hiện: Tại vị trí dòng đầu tiên, có một file rất bất thường nằm tại đường dẫn /bin/bcab. Điểm đáng chú ý là nó có Metadata/Inode ID là 4945.**

**Step 4: Trích xuất nội dung file từ Inode (icat) và giải mã Flag
Sử dụng công cụ icat từ The Sleuth Kit để đọc trực tiếp nội dung bên trong vùng dữ liệu dựa trên số Inode 4945 mà không cần phải mount toàn bộ ổ đĩa**:
Bash

┌──(kali㉿kali)-[~/Nghia]
└─$ **icat partition4.img 4945**
NzFtMzExbjNfMHU3MTEzcl9oM3JfNDNhMmU3YWYK

**Kết quả trả về một chuỗi ký tự lạ kết thúc bằng ký tự K. Đây rõ ràng là một chuỗi đã bị mã hóa dưới định dạng Base64. Tiến hành decode chuỗi này để lấy flag**:
Bash

┌──(kali㉿kali)-[~/Nghia]
└─$ echo "NzFtMzExbjNfMHU3MTEzcl9oM3JfNDNhMmU3YWYK" | base64 -d

**Flag: picoCTF{71m311n3_0u7113r_h3r_43a2e7af}**

References

**Sleuthkit** - Official Homepage

**fls** - Linux Manual Page

**icat** - Linux Manual Page

**mactime** - Sleuthkit Wiki

**Base64** Encoding/Decoding - Wikipedia









