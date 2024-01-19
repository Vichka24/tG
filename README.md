import telebot
import random
from telebot import types

# Создаем экземпляр бота
bot = telebot.TeleBot('6883683309:AAFGilqVcrNk89lyFFBkKal9LTjHGK63pN4')

# Список доступных фильмов
movies = []

# Читаем список фильмов из файла
with open('movies.txt', 'r', encoding='utf-8') as file:
    for line in file:
        movies.append(line.strip())

# Функция для выбора случайного фильма
def choose_random_movie():
    return random.choice(movies)

@bot.message_handler(commands=['start'])
def start(message):
    bot.send_message(message.chat.id, 'Привет! Я бот для выбора фильмов. Что тебе интересно?')

@bot.message_handler(func=lambda message: True)
def handle_message(message):
    if message.text == 'Фильм':
        # Выбираем случайный фильм
        random_movie = choose_random_movie()

        # Создаем клавиатуру с кнопками для оценки
        markup = types.InlineKeyboardMarkup(row_width=5)
        for i in range(1, 11):
            button = types.InlineKeyboardButton(str(i), callback_data=str(i))
            markup.add(button)

        bot.send_message(message.chat.id, f'Я рекомендую посмотреть фильм: {random_movie}')
        bot.send_message(message.chat.id, 'Оцените мой выбор:', reply_markup=markup)
    else:
        bot.send_message(message.chat.id, 'Я не понимаю, что ты имеешь в виду.')

# Обработка нажатий на кнопки
@bot.callback_query_handler(func=lambda call: True)
def handle_callback(call):
    if call.data.isdigit() and 1 <= int(call.data) <= 10:
        rating = int(call.data)
        bot.send_message(call.message.chat.id, f'Спасибо за оценку: {rating}!')
    else:
        bot.send_message(call.message.chat.id, 'Неверная оценка. Пожалуйста, выберите число от 1 до 10.')

bot.polling()
