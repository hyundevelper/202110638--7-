# DHT11 센서를 이용한 Raspberry Pi 4 + Node-RED 환경 구축
1. 개요
DHT11은 온도와 습도를 측정할 수 있는 저렴한 센서입니다. Raspberry Pi와 함께 사용하여 환경 모니터링 시스템을 구축할 수 있으며, Python 프로그래밍 언어를 통해 센서에서 데이터를 읽어온 후, Node-RED를 통해 시각화할 수 있습니다.

2. 필요 장비
Raspberry Pi: Raspberry Pi 3 이상 모델
DHT11 온습도 센서: 온도와 습도를 측정하는 센서
브레드보드: 회로 연결을 위한 도구
점퍼 와이어: 연결용
Python 라이브러리:
adafruit_dht: DHT11 센서와의 통신을 위한 라이브러리
board: GPIO 핀 제어를 위한 라이브러리
3. 회로 연결
DHT11 센서를 브레드보드에 다음과 같이 연결합니다:

VCC 핀: DHT11의 VCC 핀을 Raspberry Pi의 3.3V 핀에 연결합니다.
GND 핀: DHT11의 GND 핀을 Raspberry Pi의 GND 핀에 연결합니다.
DATA 핀: DHT11의 DATA 핀을 Raspberry Pi의 GPIO 4번 핀 (D4)에 연결합니다.

4. 데이터 읽기 및 전송
5. 
import time
import board
import adafruit_dht
import paho.mqtt.client as mqtt


dhtDevice = adafruit_dht.DHT11(board.D4)


broker = "mqtt.eclipse.org"  # MQTT 브로커 주소
topic = "sensor/dht11"

mqtt_client = mqtt.Client()
mqtt_client.connect(broker)

try:
    while True:
        # 온도 및 습도 값 읽기
        temperature_c = dhtDevice.temperature  # 섭씨 온도
        humidity = dhtDevice.humidity  # 습도
        
        # MQTT를 통해 데이터 전송
        mqtt_client.publish(topic, f"{temperature_c},{humidity}")
        print(f"Published: Temp: {temperature_c:.1f}°C, Humidity: {humidity}%")
        time.sleep(2.0)

except Exception as e:
    print(f"Error: {e}")

finally:
    mqtt_client.disconnect()
    dhtDevice.exit()

    

5. Node-RED 연동
7. 
Node-RED 환경 설정
Node-RED 실행: Raspberry Pi에서 Node-RED를 실행하고 웹 브라우저에서 http://<Raspberry_Pi_IP>:1880에 접속합니다.

플로우 생성: 다음 노드를 사용하여 플로우를 구성합니다.

Inject 노드: 데이터를 주기적으로 전송하는 역할
Function 노드: 온도 및 습도 데이터를 포맷팅
Debug 노드: 데이터 확인용
UI Chart 노드: 시각화를 위한 차트 노드
플로우 설정 예시
Inject 노드: 주기를 설정하여 정기적으로 데이터를 수집하도록 합니다 (예: 2초마다).
Function 노드: DHT11 센서로부터 수신한 데이터를 JSON 형식으로 변환하여 UI 차트에 전달합니다.
UI Chart 노드: 수신한 데이터를 차트로 시각화하도록 설정합니다.


6. 실시간 모니터링
위 과정을 통해 DHT11 센서로부터 온도와 습도 데이터를 읽고, Node-RED를 통해 실시간으로 시각화할 수 있습니다. Node-RED 대시보드에서 차트를 설정하면 환경 상태를 모니터링하는 데 유용합니다.



