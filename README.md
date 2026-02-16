# steam-deck-replica
Phần 1: Đấu nối phần cứng
Cắm nút bấm vào rãnh giữa của breadboard 
Chọn 1 chân nối vào GND 
Chân còn lại ( là chân đối diện vs chân đã chọn ) của nút -> Pin 2 ( tùy ý) 

Nạp code cho arduino 
```
void setup() {
  Serial.begin(9600);
  pinMode(2, INPUT_PULLUP); 
}

void loop() {
  // Đọc trạng thái nút (bấm là LOW, nhả là HIGH vì dùng PULLUP)
  int trangThai = digitalRead(2);

  if (trangThai == LOW) { 
    Serial.println("MO_GAME_DI");
    delay(1000); // 
  }
}
```

Cài thư viện pyserial 
vào Terminal gõ 
``` bash 
pip install pyserial
```

Code python 
```
import serial
import os
import time

# --- CẤU HÌNH ---
# 1. Tên cổng COM (Xem trong Arduino IDE > Tools > Port)
# Windows thường là 'COM3', 'COM4'... Mac/Linux là '/dev/tty...'
arduino_port = 'COM3' 
baud_rate = 9600

# 2. Đường dẫn game 
# Ví dụ: Đường dẫn đến game League of Legends hoặc bất cứ cái gì 
game_path = r"C:\Riot Games\League of Legends\LeagueClient.exe" 

try:
    # Kết nối với Arduino
    ser = serial.Serial(arduino_port, baud_rate)
    print(f"Đã kết nối với Arduino qua cổng {arduino_port}")
    print("Đang chờ bấm nút...")

    while True:
        # Đọc dữ liệu từ Arduino gửi lên
        if ser.in_waiting > 0:
            # Đọc 1 dòng, giải mã từ bytes sang string, xóa khoảng trắng thừa
            tin_hieu = ser.readline().decode('utf-8').strip()
            
            if tin_hieu == "MO_GAME_DI":
                print("Nhận lệnh mở game! Đang khởi động...")
                # Lệnh mở file của Windows
                os.startfile(game_path)
                
        time.sleep(0.1) 

except serial.SerialException:

    print("Lỗi: Không tìm thấy cổng COM hoặc Arduino chưa cắm.")
except Exception as e:
    print(f"Có lỗi xảy ra: {e}")
```
Tự sửa game path 
