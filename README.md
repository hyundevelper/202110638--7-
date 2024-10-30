# DHT11 - 라즈베리파이 4 + Node-RED

1. 개요
DHT11은 온도와 습도를 측정할 수 있는 저렴한 센서입니다. 라즈베리 파이와 함께 사용하여 환경 모니터링 시스템을 구축할 수 있으며, Python 프로그래밍 언어를 사용하여 센서에서 직접 데이터를 읽어오는 방법을 배우고, 이를 Node-RED를 통해 시각화할 수 있습니다.

2. 필요 장비
라즈베리 파이: Raspberry Pi 3 이상
DHT11 온습도 센서: 온도와 습도를 측정하는 센서
브레드보드: 회로 연결을 위한 도구
점퍼 와이어: 연결용
Python 라이브러리:
adafruit_dht: DHT11 센서와의 통신을 위한 라이브러리
board: GPIO 핀 제어를 위한 라이브러리
3. 회로 연결
DHT11 센서를 브레드보드에 다음과 같이 연결합니다:

VCC 핀: DHT11의 VCC 핀을 라즈베리 파이의 3.3V 핀에 연결합니다.
GND 핀: DHT11의 GND 핀을 라즈베리 파이의 GND 핀에 연결합니다.
DATA 핀: DHT11의 DATA 핀을 라즈베리 파이의 **GPIO 4번 핀 (D4)**에 연결합니다.


회로도 예시

   DHT11
   +---------+
   |         |
   |  VCC    |------> 3.3V (Raspberry Pi)
   |         |
   |  GND    |------> GND (Raspberry Pi)
   |         |
   |  DATA   |------> GPIO 4 (D4) (Raspberry Pi)
   +---------+
4. 코드 작성
아래의 Python 코드를 사용하여 DHT11 센서로부터 온도와 습도 데이터를 읽어올 수 있습니다.

python
코드 복사
import time
import board
import adafruit_dht

# DHT11 센서 초기화 (GPIO 4번 핀 사용)
dhtDevice = adafruit_dht.DHT11(board.D4)

try:
    while True:
        # 온도 및 습도 값 읽기
        temperature_c = dhtDevice.temperature  # 섭씨 온도
        temperature_f = temperature_c * (9 / 5) + 32  # 화씨 온도로 변환
        humidity = dhtDevice.humidity  # 습도

        # 값 출력
        print(f"Temp: {temperature_f:.1f}°F / {temperature_c:.1f}°C    Humidity: {humidity}%")

        # 2초 대기
        time.sleep(2.0)

except Exception as e:
    print(f"Error: {e}")

finally:
    dhtDevice.exit()
5. Node-RED 연동
Node-RED를 설치하고 실행합니다.
DHT11 센서에서 읽은 온도와 습도 데이터를 Node-RED로 전송하기 위해 MQTT나 HTTP 요청을 사용할 수 있습니다.
Node-RED 대시보드에서 데이터를 시각화하기 위한 차트 또는 그래프 노드를 추가합니다.
예시: MQTT를 통한 데이터 전송
먼저 MQTT 브로커를 설정하고, paho-mqtt 라이브러리를 설치합니다:
bash
코드 복사
pip install paho-mqtt
다음은 데이터를 MQTT 브로커로 전송하는 방법입니다:
python
코드 복사
import paho.mqtt.client as mqtt

# MQTT 설정
broker = "mqtt.eclipse.org"  # MQTT 브로커 주소
topic = "sensor/dht11"

# MQTT 클라이언트 초기화
mqtt_client = mqtt.Client()
mqtt_client.connect(broker)

try:
    while True:
        temperature_c = dhtDevice.temperature
        humidity = dhtDevice.humidity

        # MQTT로 데이터 전송
        mqtt_client.publish(topic, f"Temp: {temperature_c:.1f}°C, Humidity: {humidity}%")
        print(f"Published: Temp: {temperature_c:.1f}°C, Humidity: {humidity}%")
        time.sleep(2.0)

except Exception as e:
    print(f"Error: {e}")

finally:
    mqtt_client.disconnect()
    dhtDevice.exit()
    
    
    이 과정을 통해 DHT11 센서로부터 데이터를 읽고 Node-RED를 통해 시각화할 수 있습니다. 추가적으로 Node-RED 대시보드에서 데이터의 그래프를 설정하면 실시간 모니터링이 가능합니다.

# Node-RED

DHT11 센서를 통해 온도와 습도 데이터를 수집하고, Node-RED를 사용하여 이를 실시간으로 시각화합니다. 이 과정에서 온도 및 습도 값을 시각적으로 표현하여 환경 상태를 모니터링할 수 있습니다.

2. 필요 장비
Raspberry Pi: 모든 모델 지원
DHT11 센서: 온도와 습도 측정을 위한 센서
브레드보드 및 점퍼 와이어: 회로 연결용
Node-RED: Raspberry Pi에 사전 설치되어 있어야 함
3. Node-RED 환경 설정
Node-RED의 흐름을 설정하기 위해 다음 단계를 따릅니다:

Node-RED 실행: Raspberry Pi에서 Node-RED를 실행합니다. 웹 브라우저에서 http://<Raspberry_Pi_IP>:1880에 접속합니다.

플로우 생성: 아래의 노드들을 사용하여 플로우를 구성합니다.

Inject 노드: 데이터를 주기적으로 전송하는 역할
Function 노드: 온도 및 습도 데이터를 포맷팅
Debug 노드: 데이터 확인용
UI Chart 노드: 시각화를 위한 차트 노드
예시로 아래와 같은 흐름을 생성합니다:


[Inject] --> [Function] --> [Debug]
                              |
                              v
                           [UI Chart]
노드 설정:

Inject 노드: 주기를 설정하여 정기적으로 데이터를 수집하도록 합니다 (예: 2초마다).
Function 노드: DHT11 센서로부터 수신한 데이터를 JSON 형식으로 변환하여 UI 차트에 전달합니다.

msg.payload = {
    temperature: msg.temperature,
    humidity: msg.humidity
};
return msg;


UI Chart 노드: 수신한 데이터를 차트로 시각화하도록 설정합니다.
4. 온도 및 습도 데이터 수집
DHT11 센서에서 온도와 습도 데이터를 수집하는 코드를 사용하여 Node-RED로 데이터를 전송합니다. 이를 위해 앞서 작성한 MQTT 예제 코드나 HTTP 요청을 사용할 수 있습니다.

예시: MQTT로 데이터 전송


import paho.mqtt.client as mqtt

broker = "mqtt.eclipse.org"  # MQTT 브로커 주소
topic = "sensor/dht11"

mqtt_client = mqtt.Client()
mqtt_client.connect(broker)

try:
    while True:
        temperature_c = dhtDevice.temperature
        humidity = dhtDevice.humidity

        mqtt_client.publish(topic, f"{temperature_c},{humidity}")
        print(f"Published: Temp: {temperature_c:.1f}°C, Humidity: {humidity}%")
        time.sleep(2.0)

except Exception as e:
    print(f"Error: {e}")

finally:
    mqtt_client.disconnect()
    dhtDevice.exit()
