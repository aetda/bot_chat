from telebot import TeleBot, types

TOKEN = "ВАШ ТОКЕН"

bot = TeleBot(token=TOKEN)

movies = [
    {"title": "Дивергент", "url": "https://www.kinopoisk.ru/film/582101/?utm_referrer=www.google.com", "genre": "фантастика"},
    {"title": "Голодные игры", "url": "https://www.kinopoisk.ru/film/468581/", "genre": "боевик"},
    {"title": "Время", "url": "https://www.kinopoisk.ru/film/517988/", "genre": "боевик"},
    {"title": "Меняющие реальность", "url": "https://www.kinopoisk.ru/film/450765/", "genre": "фантастика"},
]


@bot.message_handler(commands=['start'])
def start_handler(msg):

    markup_1 = types.InlineKeyboardMarkup()
    kb_1 = types.InlineKeyboardButton(text="По жанру", callback_data="by_genre")
    kb_2 = types.InlineKeyboardButton(text="По названию", callback_data="by_title")
    markup_1.add(kb_1, kb_2)

    bot.send_message(
        chat_id=msg.chat.id,
        text="Привет! Добро пожаловать на наш бот! Выберите фильм:",
        reply_markup=markup_1
    )

    markup = types.ReplyKeyboardMarkup(resize_keyboard=True)
    rkb_1 = types.KeyboardButton(text="По жанру")
    rkb_2 = types.KeyboardButton(text="По названию")
    markup.add(rkb_1, rkb_2)

    bot.send_message(
        chat_id=msg.chat.id,
        text="👍",
        reply_markup=markup
    )

@bot.message_handler(commands=['all_filmes'])
def all_film_show(msg):
    markup = types.InlineKeyboardMarkup(row_width=1)
    kbs = []
    for movie in movies:
        kb_btn = types.InlineKeyboardButton(
            text=movie['title'],
            callback_data=f"film_{movie['title']}"
        )
        kbs.append(kb_btn)
    markup.add(*kbs)

    bot.send_message(
        chat_id=msg.chat.id,
        text="Список фильмов. Выберите одну из фильмов!",
        reply_markup=markup
    )

@bot.message_handler(func=lambda msg: msg.text in ['По жанру', 'По названию'])
def func_hudler(msg):
    films_genre = []
    added_titles = set()
    added_genres = set()
    markup = types.ReplyKeyboardMarkup(resize_keyboard=True)
    if msg.text == 'По жанру':
        for movie in movies:
            if movie['title'] not in added_titles:
                if movie['genre'] not in added_genres:
                    kb_btn = types.KeyboardButton(movie['genre'])
                    films_genre.append(kb_btn)
                    added_genres.add(movie['genre'])
                added_titles.add(movie['title'])
        if films_genre:
            markup.add(*films_genre)
            bot.send_message(chat_id=msg.chat.id, text=f"Список жанров выберите через кнопок!", reply_markup=markup)
        else:
            bot.send_message(chat_id=msg.chat.id, text=f"Не найден!")

    elif msg.text == "По названию":
        films_title = []
        for movie in movies:
            kb_btn = types.KeyboardButton(movie['title'])
            films_title.append(kb_btn)
        markup.add(*films_title)
        bot.send_message(msg.chat.id, text=f"Выберите фильмы:", reply_markup=markup)


@bot.message_handler(func=lambda msg: any(msg.text == movie['genre'] for movie in movies))
def handle_movie_selection(msg):
    films_title = []
    url = []
    markup = types.ReplyKeyboardMarkup(resize_keyboard=True)
    for movie in movies:
        if msg.text == movie['genre']:
            kb_btn = types.KeyboardButton(movie['title'])
            films_title.append(kb_btn)
    markup.add(*films_title)
    bot.send_message(msg.chat.id, text=f"Фильмы жанра: {msg.text}", reply_markup=markup)


@bot.message_handler(func=lambda msg: True)
def filmes_url(msg):
    for movie in movies:
        if msg.text == movie['title']:
            bot.send_message(chat_id=msg.chat.id, text=f"Ссылка на фильм: \n {movie['url']}")


@bot.callback_query_handler()
def callback_handler(callback):
    films_genre = []
    added_titles = set()
    added_genres = set()
    markup = types.InlineKeyboardMarkup()

    if callback.data == 'by_genre':
        for movie in movies:
            if movie['title'] not in added_titles:
                if movie['genre'] not in added_genres:
                    kb_btn = types.InlineKeyboardButton(movie['genre'], callback_data=movie['genre'])
                    films_genre.append(kb_btn)
                    added_genres.add(movie['genre'])
                added_titles.add(movie['title'])
        if films_genre:
            markup.add(*films_genre)
            bot.send_message(chat_id=callback.message.chat.id, text=f"Список жанров", reply_markup=markup)
        else:
            bot.send_message(chat_id=callback.message.chat.id, text=f"Не найден!")

    elif callback.data == 'by_title':
        for movie in movies:
            kb_btn = types.InlineKeyboardButton(
                text=movie['title'],
                url=movie['url'],
            )

            markup.add(kb_btn)
        bot.send_message(
            chat_id=callback.message.chat.id,
            text="Выберите фильм!",
            reply_markup=markup
        )

    elif callback.data in [movie['genre'] for movie in movies]:
        genre = callback.data
        films_by_genre = []
        for movie in movies:
            if movie['genre'] == genre:
                films_by_genre.append(movie)

        if films_by_genre:
            for movie in films_by_genre:
                kb_btn = types.InlineKeyboardButton(movie['title'], url=movie['url'])
                markup.add(kb_btn)
            bot.send_message(callback.message.chat.id, text=f"Фильмы жанра {genre}", reply_markup=markup)
        else:
            bot.send_message(callback.message.chat.id, text=f"Фильмы жанра {genre} не найдены")

    elif callback.data.startswith("film_"):
        title = callback.data[5:]
        for movie in movies:
            if movie['title'] == title:
                bot.send_message(callback.message.chat.id, text=f"Ссылка на фильм: \n {movie['url']}")
                break


bot.infinity_polling()
