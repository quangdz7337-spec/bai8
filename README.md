# bai8
STM32F103C8T6 – Đọc ADC bằng Polling và gửi UART
🔹 Mục tiêu

Đọc giá trị biến trở từ chân PA0 (ADC1_IN0).

Sử dụng phương pháp Polling (chờ cờ EOC – End Of Conversion).

Lấy 1000 mẫu liên tiếp, tính trung bình.

Quy đổi sang điện áp (mV).

Gửi giá trị điện áp qua UART1 để hiển thị trên PC (Hercules, TeraTerm…).

🔹 Phần cứng

STM32F103C8T6 (Bluepill).

Biến trở 10kΩ.

USB–UART converter (PL2303, CP2102, CH340…).

Dây nối.

Kết nối

Biến trở:

Một đầu ngoài → 3.3V

Đầu ngoài còn lại → GND

Chân giữa → PA0

UART:

PA9 (TX) → RX của USB–UART

PA10 (RX) → TX của USB–UART (nếu cần nhận)

GND STM32 ↔ GND USB–UART

🔹 Phần mềm

IDE: Keil uVision5

Thư viện: STM32F10x Standard Peripheral Library

Cấu hình UART1

Baudrate: 9600

Data bits: 8

Stop bit: 1

Parity: None

Cấu hình ADC1 (Polling)

Kênh: ADC_Channel_0 (PA0)

Chế độ: Continuous conversion

Lấy mẫu: ADC_SampleTime_55Cycles5

Sau khi enable, code kiểm tra cờ EOC (End Of Conversion) để đọc giá trị từ ADC1->DR.

🔹 Code chính
Lấy mẫu và tính trung bình
sum = 0;
for (i = 0; i < ADC_BUF_LEN; i++) {
    while (ADC_GetFlagStatus(ADC1, ADC_FLAG_EOC) == RESET);
    sample = ADC_GetConversionValue(ADC1);
    sum += sample;
}
avg = sum / ADC_BUF_LEN;
voltage = (avg * 3300) / 4095; // mV

Gửi qua UART
sprintf(msg, "Voltage: %lu mV\r\n", voltage);
USART1_SendString(msg);

🔹 Kết quả

Khi mở Hercules (COMx, Baud 9600), màn hình sẽ in ra điện áp đo được từ biến trở:

Voltage: 123 mV
Voltage: 1635 mV
Voltage: 2890 mV


Khi vặn biến trở, giá trị sẽ thay đổi từ 0 mV → 3300 mV.
