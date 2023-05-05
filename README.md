import requests
from bs4 import BeautifulSoup
import sqlite3
import time


def create_table():
    conn = sqlite3.connect('weather.db')
    c = conn.cursor()
    c.execute('CREATE TABLE IF NOT EXISTS weather_data(date TEXT, time TEXT, temperature REAL)')
    conn.commit()
    conn.close()


def insert_data(date, time, temperature):
    conn = sqlite3.connect('weather.db')
    c = conn.cursor()
    c.execute('INSERT INTO weather_data VALUES (?, ?, ?)', (date, time, temperature))
    conn.commit()
    conn.close()


def get_temperature():
    url = 'https://www.accuweather.com/en/ua/kharkiv/324505/weather-forecast/324505'
    response = requests.get(url)
    soup = BeautifulSoup(response.content, 'html.parser')
    temperature = soup.find('div', class_='temp').text
    temperature = temperature.replace('°', '')
    temperature = float(temperature)
    return temperature


def main():
    create_table()
    while True:
        date = time.strftime('%Y-%m-%d')
        current_time = time.strftime('%H:%M:%S')
        temperature = get_temperature()
        insert_data(date, current_time, temperature)
        time.sleep(1800)


if __name__ == '__main__':
    main()
