import asyncio
import logging
from aiogram import Bot, Dispatcher, types
from aiogram.filters import Command
from aiogram.types import InlineKeyboardMarkup, InlineKeyboardButton, CallbackQuery

# ================== НАСТРОЙКИ ==================
API_TOKEN = '8785757886:AAHc7J6_-m9HEJr4t-wnLs8-M4lHFJO6NN4'

# ←←← ОБЯЗАТЕЛЬНО ИЗМЕНИ НА СВОЙ КАНАЛ! ←←←
CHANNEL_USERNAME = '@твой_канал'   # Например: @ai_content_pro

# ===============================================

bot = Bot(token=API_TOKEN, parse_mode="HTML")
dp = Dispatcher()


async def check_subscription(user_id: int) -> bool:
    """Проверяет подписку пользователя на канал"""
    try:
        member = await bot.get_chat_member(CHANNEL_USERNAME, user_id)
        return member.status in ["member", "administrator", "creator"]
    except Exception as e:
        logging.error(f"Ошибка при проверке подписки: {e}")
        return False


@dp.message(Command("start"))
async def cmd_start(message: types.Message):
    keyboard = InlineKeyboardMarkup(inline_keyboard=[
        [InlineKeyboardButton(text="🚀 Начать", callback_data="start_action")]
    ])
    
    await message.answer(
        "👋 <b>Добро пожаловать в бота!</b>\n\n"
        "Нажми кнопку ниже, чтобы получить бесплатные материалы по ИИ-контенту.",
        reply_markup=keyboard
    )


@dp.callback_query(lambda c: c.data == "start_action")
async def process_start(callback: CallbackQuery):
    user = callback.from_user
    is_sub = await check_subscription(user.id)

    if is_sub:
        # Подписан
        await callback.message.edit_text(
            f"✅ <b>Спасибо за подписку, {user.first_name}!</b>\n\n"
            "Держи бесплатные гайды по созданию ИИ-контента 🔥\n\n"
            "Какие материалы тебе нужны в первую очередь?"
        )
    else:
        # Не подписан
        keyboard = InlineKeyboardMarkup(inline_keyboard=[
            [InlineKeyboardButton(text="📢 Подписаться на канал", 
                                  url=f"https://t.me/{CHANNEL_USERNAME.strip('@')}")],
            [InlineKeyboardButton(text="✅ Я подписался", callback_data="check_subscription")]
        ])
        
        await callback.message.edit_text(
            f"Привет, <b>{user.first_name}</b>!\n\n"
            "Бесплатный курс уже доступен.\n"
            "Но сначала нужно быть подписанным на канал 👇",
            reply_markup=keyboard
        )
    
    await callback.answer()


@dp.callback_query(lambda c: c.data == "check_subscription")
async def check_subscription_callback(callback: CallbackQuery):
    user = callback.from_user
    is_sub = await check_subscription(user.id)

    if is_sub:
        await callback.message.edit_text(
            f"✅ Отлично, <b>{user.first_name}</b>! Подписка подтверждена.\n\n"
            "Держи бесплатные гайды по созданию ИИ-контента 🔥"
        )
    else:
        await callback.answer("❌ Ты пока не подписан на канал", show_alert=True)
    
    await callback.answer()


async def main():
    logging.basicConfig(level=logging.INFO)
    print("🤖 Бот успешно запущен!")
    await dp.start_polling(bot)


if __name__ == "__main__":
    asyncio.run(main())
