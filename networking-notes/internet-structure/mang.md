# Mạng biên, mạng truy cập, mạng lõi

Internet được chia thành **3 phần** để dễ học và dễ thiết kế. Mỗi phần có vai trò riêng.

## 1. Khái niệm

### Mạng biên (Network Edge)

- Nằm ở **rìa Internet**, nơi có thiết bị và ứng dụng.
- Gồm các **thiết bị đầu cuối** (host / end system):
  - Máy tính, laptop, điện thoại, smart TV, camera, thiết bị IoT.
  - **Server** (máy chủ) chứa dữ liệu: web, video, mail...
- Là nơi dữ liệu **được tạo ra** và **được dùng đến**.
- Hai mô hình giao tiếp:
  - **Client-Server**: client gửi yêu cầu, server luôn bật và phản hồi (web, email).
  - **Peer-to-Peer (P2P)**: các máy nói chuyện trực tiếp, vừa là client vừa là server (BitTorrent).

### Mạng truy cập (Access Network)

- Là **đường nối** từ thiết bị ở mạng biên ra tới mạng lõi (điểm kết nối đầu tiên của nhà mạng).
- Chỉ lo **đoạn ngắn**: đưa thiết bị vào mạng, không lo tìm đường đi xa.
- Các loại phổ biến:

| Loại | Ghi chú |
|---|---|
| FTTH (cáp quang) | Nhanh, phổ biến ở Việt Nam |
| Wi-Fi | Không dây, trong nhà, quán cà phê |
| 4G/5G | Không dây qua sóng điện thoại |
| Ethernet | Cắm dây mạng, công ty, trường học |
| DSL | Dùng đường dây điện thoại (cũ) |
| Cáp đồng trục (HFC) | Dùng cáp truyền hình (cũ) |

### Mạng lõi (Network Core)

- Là **mạng lưới các đường truyền và thiết bị chuyển tiếp** nối các mạng truy cập với nhau, do nhiều nhà mạng quản lý.
- **Không chứa dữ liệu**, chỉ **chuyển dữ liệu đi** đúng hướng, đúng nơi nhận.
- Dữ liệu được cắt thành các **gói tin (packet)**, mỗi gói có địa chỉ đích.
- Được thiết kế **dạng lưới, dư đường**: một chỗ hỏng thì gói tin đi đường khác.

## 2. Hình dung bằng đường bộ

| Phần | Hình dung |
|---|---|
| Mạng biên | Ngôi nhà, cửa hàng (nơi người ta sống và làm việc) |
| Mạng truy cập | Con hẻm, đường nhỏ nối nhà ra đường lớn |
| Mạng lõi | Đường cao tốc và các nút giao |

Mẹo nhớ: **Biên = rìa**. Rìa Internet = biên + truy cập. Giữa Internet = lõi.

## 3. Sơ đồ tổng quát

```
 Bạn                                                        Nơi chứa video
  |                                                               |
[Mạng biên]  <-->  [Mạng truy cập]  <-->  [Mạng lõi]  <-->  [Mạng truy cập]  <-->  [Mạng biên]
 Điện thoại        Wi-Fi, cáp quang       Chuyển gói tin      Đường nối nhà mạng      Server YouTube
```

Mũi tên hai chiều: yêu cầu đi lên, dữ liệu trả về đi xuống.

## 4. Demo: mở YouTube trên điện thoại

| Bước | Chuyện gì xảy ra | Thuộc mạng nào |
|---|---|---|
| 1 | Bạn bấm xem video. Điện thoại tạo yêu cầu "cho tôi video này" | **Mạng biên** |
| 2 | Yêu cầu đi qua Wi-Fi rồi cáp quang để ra khỏi nhà | **Mạng truy cập** |
| 3 | Yêu cầu được cắt thành các gói tin, có ghi địa chỉ đích (server YouTube) | **Mạng biên** (điện thoại chuẩn bị gửi) |
| 4 | Các gói tin đi qua nhiều chặng, mỗi chặng chuyển tiếp tới chặng kế, tới gần server | **Mạng lõi** |
| 5 | Gói tin đi qua đường nối vào phía server | **Mạng truy cập** (đầu bên kia) |
| 6 | Server YouTube nhận yêu cầu, tìm video, gửi dữ liệu video về | **Mạng biên** (đầu bên kia) |
| 7 | Dữ liệu video đi ngược lại theo con đường trên | Lõi, rồi truy cập |
| 8 | Điện thoại ghép các gói tin lại và phát video | **Mạng biên** |

Điểm cần nhớ từ demo:
- **Server nằm ở mạng biên**, vì nó là thiết bị đầu cuối chứa dữ liệu.
- **Mạng lõi chỉ vận chuyển**, không chứa video.
- Dữ liệu đi **cả hai chiều**.

## 5. Khi nào không cần qua mạng lõi?

Hai thiết bị **ở cùng một mạng truy cập** nói chuyện trực tiếp được.

Ví dụ: điện thoại và laptop cùng Wi-Fi nhà gửi file cho nhau, không cần ra mạng lõi. Chỉ khi đi **ra ngoài** (YouTube, Facebook, người ở xa) mới bắt buộc qua lõi.

## 6. Tại sao phải qua mạng lõi?

- Không thể kéo dây riêng từ mỗi thiết bị tới mọi thiết bị khác (quá tốn kém).
- Mạng lõi cho mọi người **dùng chung đường dài**; mỗi nhà chỉ cần một đường ngắn ra lõi.
- Mạng truy cập chỉ lo đoạn gần, không lo tìm đường đi xa.

## 7. Nếu có sự cố thì sao?

| Sự cố | Hậu quả |
|---|---|
| Hỏng một phần mạng lõi | Gói tin đi đường khác, gần như không ảnh hưởng |
| Hỏng mạng truy cập nhà bạn | Chỉ nhà bạn mất mạng |
| Đứt nhiều tuyến lõi (ví dụ cáp quang biển) | Một khu vực bị chậm hoặc khó vào web quốc tế |

## 8. Từ vựng tiếng Anh

| Tiếng Anh | Tiếng Việt |
|---|---|
| Network edge | Mạng biên |
| Access network | Mạng truy cập |
| Network core | Mạng lõi |
| Host / End system | Thiết bị đầu cuối |
| Client / Server | Máy khách / Máy chủ |
| Peer-to-Peer (P2P) | Mạng ngang hàng |
| Packet | Gói tin |
| Bandwidth | Băng thông |

## 9. Tóm tắt 3 câu

1. **Mạng biên**: nơi có thiết bị và server.
2. **Mạng truy cập**: đường ngắn nối thiết bị ra mạng lõi.
3. **Mạng lõi**: đường trục chung, chuyển gói tin giữa các nơi.

