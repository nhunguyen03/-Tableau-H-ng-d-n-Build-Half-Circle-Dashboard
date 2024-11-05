# -Tableau-Build-Half-Circle-Dashboard

## Yêu cầu: Build 1 dashboard Half-Circle với 3 màu khác nhau. 3 threshold tương ứng 3 vòng cung chồng lên nhau tạo thành Half-Circle Dashboard
![half-circle](https://github.com/user-attachments/assets/45c34035-92d2-42d4-8515-6bead6b21118)

Bài sau sẽ hướng dẫn từ A - Z cách xây dựng Half-Circle Dashboard và giải thích chi tiết
### Bước 1: Insert data bên dưới vào dataset

- Level 1, 2, 3 tương ứng là các threshold để phân cách 2 màu trên Half-Circle. Còn được hiểu là số lượng vòng cung muốn chồng lên nhau. Tùy yêu cầu, có đề bài có lên đến 5-6 threshold
- 1 - 362 tương ứng là 2 nửa vòng tròn 180 độ (1-181 và 182-362) - điều này sẽ được minh họa bởi hình ảnh ở bước kế tiếp
- Sau đây, gọi nửa vòng tròn lớn bên ngoài là **vòng 1**; nửa vòng tròn nhỏ bên trong là **vòng 2**

![image](https://github.com/user-attachments/assets/e5ba71fc-9a51-480e-82fa-568234098f18)

- Ở Physical Layer, insert data trên vào dataset hiện tại. Setting: 1-1. Điều này sẽ làm số lượng records trong dataset hiện tại *6 lần (tương ứng 6 cột)

![image](https://github.com/user-attachments/assets/fa49c423-aa67-45e0-af57-d8482c8fc251)

- Tạo Parameter Level 1, Level 2, Level 3 tương ứng các threshold. Các threshold này sẽ được dùng để điều chỉnh độ sâu của vòng cung (depth), khi thay đổi giá trị này ở parameter, độ sâu vòng cung sẽ thay đổi tự động

![image](https://github.com/user-attachments/assets/6b6441e3-db1b-4fc8-8955-de9e6b4462d8)

![image](https://github.com/user-attachments/assets/7a9d9335-0597-496c-a30e-0c5efe02c7c7)

![image](https://github.com/user-attachments/assets/72be19d4-01da-47da-852d-b5265288255e)


### Bước 2: Tạo Path(bin), index --> tận dụng _DATA DENSIFICATION_ (có thể tìm hiểu thuật ngữ này)
- Tạo Path(bin): với size of bin là 1 <=> tạo 362 bins với mỗi bin chứa 1 giá trị từ 1 đến 362

![image](https://github.com/user-attachments/assets/70370386-bafd-4c7c-9c37-22385ac55851)
![image](https://github.com/user-attachments/assets/b84bdf66-3082-49d6-afc5-672b6ee640f4)

- Tạo index: index() bắt đầu từ 1; nên minus 1 để bắt đầu từ 0 - 180 độ và 181 - 361 độ

![image](https://github.com/user-attachments/assets/2feb71ef-9c6b-4e90-a5fa-703c78057722)

**? QUESTION: Vì sao đã tạo Path(bin) rồi, lại còn tạo index làm gì khi cả 2 đều hiển thị KQ từ 1 - 362**
- Câu trả lời như ảnh bên dưới. Path(bin) chỉ show các bin khi show all missing value, bản chất nó chỉ có 2 giá trị thực là 1 và 362. Do đó, khi vẽ line - line này sẽ nối point 1 đến point 362 trực tiếp, không thể curve tạo circle
- Ngược lại index show các giá trị 1 - 362 và khi vẽ line - line này sẽ nối point 1 đến point 2 đến point 3... đến point 362, có thể curve tạo circle

=> Vì điểm khác biệt đó, index được dùng để tính Dial Radius và Dial Degree bên dưới

![image](https://github.com/user-attachments/assets/29436fcf-a143-4918-8e11-c35722138a5a)


### Bước 3: Tạo Dial Radius (bán kính), Dial Degree (độ), Depth (độ sâu của các vòng cung)
**- Dial Radius**: bán kính của vòng 1 và vòng 2
- Bán kính vòng 2 sẽ nhỏ hơn (1-_size_). _size_ là parameter có min là 0, max là 1 và step là 0.1. Vì _size_ là parameter nên sẽ được tùy chỉnh tùy ý

_@ NOTE: index luôn nhỏ hơn Path(bin) 1 đơn vị do đã minus 1 ở công thức_

![image](https://github.com/user-attachments/assets/5b940281-e294-4250-80dc-7bc104d44f00)
![image](https://github.com/user-attachments/assets/387d4b4f-ab8d-435a-936d-a61304421b52)

**- Dial Degree**: vòng 1 hiển thị như bình thường. Riêng vòng 2 ta muốn hiển thị ngược lên trên thay vì vòng xuống dưới nên dùng (361-index)/180
- Các hàm SIN, COS trong Tableau mặc định dùng pi làm góc thay vì độ. Do đó, KQ sau khi tính xong phải *pi()

_@ NOTE: index luôn nhỏ hơn Path(bin) 1 đơn vị do đã minus 1 ở công thức_

![image](https://github.com/user-attachments/assets/bac582d0-edb0-40d7-915e-2b4304d6dfa0)

**? QUESTION: Tại sao không MAKEPOINT Dial mà lại dùng tọa độ X, Y để vẽ vòng cung**
- Không thể dùng MAKEPOINT Dial vì Tableau không cho phép áp dụng Table Calculation, Field từ nhiều data source

![image](https://github.com/user-attachments/assets/32810d0e-25dc-4728-be80-c4048373f59a)

**- Depth (độ sâu vòng cung)**: ở đây có 3 hướng làm, tuy nhiên chỉ có hướng cuối cùng thực hiện được, 2 hướng còn lại sai
- Hướng 1: Tạo Parameter Level với range 1 - 3. Sau đó dùng Depth để tạo độ sâu vòng cùng => SAI vì không thể cùng lúc chồng 3 vòng cung lên nhau như yêu cầu. Nó chỉ show từng vòng cung khi thay đổi Parameter Level

![image](https://github.com/user-attachments/assets/0c98a77b-a191-47b0-aafd-550ca3f97f57)

![image](https://github.com/user-attachments/assets/d505868d-479d-4017-933a-95128a8caa79)

![image](https://github.com/user-attachments/assets/231051f9-e14e-49ac-a501-cb443d0ac348)

![image](https://github.com/user-attachments/assets/e8898a62-2073-4c6a-8174-858b3974d1fb)

![image](https://github.com/user-attachments/assets/aadc238d-0a12-4c5d-9678-48e3cdc260a3)

- Hướng 2: dùng Level trong dữ liệu insert ban đầu vào dataset => SAI vì khi *Depth vào X, Y thì Tableau báo lỗi _"Cannot mix aggregate and non-aggregate arguments with this function"_

![image](https://github.com/user-attachments/assets/64c8cebb-8026-4ec7-8689-e7d0b61f165c)

![image](https://github.com/user-attachments/assets/b5f98b32-2afb-4af6-9325-45e7db2039c3)

- Hướng 3: dùng WINDOW FUNCTION trong dữ liệu insert ban đầu vào dataset

![image](https://github.com/user-attachments/assets/1363f1bc-d276-423a-835c-bfaa7cf99058)

![image](https://github.com/user-attachments/assets/45846ffe-dcd6-402f-b4e9-7819f35989c9)

![image](https://github.com/user-attachments/assets/9773bfc2-4ba8-4e5c-a9e6-4efd07909475)

### Bước 4: Tạo Half-Circle Dashboard
- Reverse trục X, Edit Color và Show Filter

![image](https://github.com/user-attachments/assets/1fb8e4ce-8e47-4072-bd4d-1a782ae26a31)



























