from aiogram import Bot, Dispatcher
from aiogram.filters import Command
import asyncio
import logging

API_TOKEN = '8785757886:AAHc7J6_-m9HEJr4t-wnLs8-M4lHFJO6NN4'

bot = Bot(token=API_TOKEN)
dp = Dispatcher()

@dp.message(Command("start"))
async def test(message):
    await message.answer("✅ Бот живой! Работает.")

async def main():
    logging.basicConfig(level=logging.INFO)
    print("🤖 ТЕСТОВЫЙ БОТ ЗАПУЩЕН")
    await dp.start_polling(bot)

if __name__ == "__main__":
    asyncio.run(main())
