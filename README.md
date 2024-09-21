from telegram import InlineKeyboardButton, InlineKeyboardMarkup, Update
from telegram.ext import Updater, CommandHandler, CallbackQueryHandler, MessageHandler, Filters
import time
import threading

# توکن ربات شما
TOKEN = "YOUR_BOT_TOKEN"
CHANNEL_USERNAME = "@YourChannelUsername"  # یوزرنیم کانال شما
MINI_APP_URL = "https://t.me/your_bot_username?start=unique_param"  # لینک مینی اپ شما

# تابعی برای شروع ربات و نمایش دکمه‌های Play و Join channel
def start(update: Update, context):
    keyboard = [
        [InlineKeyboardButton("Play to earn⛏️⛏️", callback_data='play')],
        [InlineKeyboardButton("📣 Join channel 📣", url=f"https://t.me/{CHANNEL_USERNAME}")]
    ]
    reply_markup = InlineKeyboardMarkup(keyboard)
    update.message.reply_text("Welcome! Choose an option:", reply_markup=reply_markup)

# تابع برای مدیریت کلیک بر روی دکمه Play
def button(update: Update, context):
    query = update.callback_query
    query.answer()

    if query.data == "play":
        # هدایت به مینی اپ در تلگرام
        query.edit_message_text(text="Redirecting to mini app...", reply_markup=None)
        context.bot.send_message(chat_id=update.effective_chat.id, text=f"Click here to access the mini app: {MINI_APP_URL}")

# تابعی برای مدیریت بخش friends و عضویت در کانال
def friends(update: Update, context):
    query = update.callback_query
    query.answer()

    # بررسی اینکه آیا کاربر عضو کانال است یا خیر
    member_status = context.bot.get_chat_member(chat_id=CHANNEL_USERNAME, user_id=query.from_user.id).status
    if member_status in ['member', 'administrator', 'creator']:
        # نمایش گزینه Join channel با تیک سبز
        keyboard = [[InlineKeyboardButton("✅ Join channel", callback_data='joined')]]
    else:
        # نمایش گزینه Join channel بدون تیک
        keyboard = [[InlineKeyboardButton("Join channel", url=f"https://t.me/{CHANNEL_USERNAME}")]]
    
    reply_markup = InlineKeyboardMarkup(keyboard)
    query.edit_message_text(text="Invite your friends!", reply_markup=reply_markup)

# تابع اصلی برای شروع ربات
def main():
    updater = Updater(TOKEN, use_context=True)
    dp = updater.dispatcher

    dp.add_handler(CommandHandler("start", start))
    dp.add_handler(CallbackQueryHandler(button, pattern='play'))
    dp.add_handler(CallbackQueryHandler(friends, pattern='friends'))

    updater.start_polling()
    updater.idle()

if name == 'main':
    main()
