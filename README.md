from twilio.rest import Client
import time

# Ваши данные Twilio
TWILIO_SID = 'your_account_sid'
TWILIO_TOKEN = 'your_auth_token'
TWILIO_NUMBER = '+1234567890'  # Номер Twilio
YOUR_NUMBER = '+0987654321'  # Ваш номер телефона

# Инициализация клиента Twilio
client = Client(TWILIO_SID, TWILIO_TOKEN)

def make_call():
    """Функция для совершения звонка"""
    try:
        call = client.calls.create(
            to=YOUR_NUMBER,
            from_=TWILIO_NUMBER,
            url='http://demo.twilio.com/docs/voice.xml'  # Приветственное сообщение от Twilio
        )
        print(f"Звонок инициирован. SID: {call.sid}")
    except Exception as e:
        print(f"Ошибка при совершении звонка: {e}")

def monitor_alarm_signal():
    """Мониторинг сигнала сигнализации (пример с GPIO)"""
    import RPi.GPIO as GPIO

    # Настройка GPIO (пример для Raspberry Pi)
    GPIO.setmode(GPIO.BCM)
    ALARM_PIN = 18  # Пин, к которому подключён датчик
    GPIO.setup(ALARM_PIN, GPIO.IN, pull_up_down=GPIO.PUD_DOWN)

    print("Мониторинг сигнализации запущен...")

    try:
        while True:
            # Проверяем сигнал (1 — тревога, 0 — всё нормально)
            if GPIO.input(ALARM_PIN) == 1:
                print("ТРЕВОГА! Сигнал получен.")
                make_call()
                # Ждём, пока сигнал не сбросится, чтобы не звонить повторно
                while GPIO.input(ALARM_PIN) == 1:
                    time.sleep(1)
            time.sleep(0.1)  # Задержка для снижения нагрузки на CPU
    except KeyboardInterrupt:
        print("Мониторинг остановлен.")
    finally:
        GPIO.cleanup()

if __name__ == "__main__":
    monitor_alarm_signal()
