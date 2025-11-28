# requirements.txt
telegram bot 
import os
import logging
from telegram import Update
from telegram.ext import Application, CommandHandler, MessageHandler, filters, ContextTypes
from dotenv import load_dotenv

# Load environment variables
load_dotenv()

# Enable logging
logging.basicConfig(
    format='%(asctime)s - %(name)s - %(levelname)s - %(message)s',
    level=logging.INFO
)

TOKEN = os.getenv('BOT_TOKEN')

async def start(update: Update, context: ContextTypes.DEFAULT_TYPE):
    """Send welcome message when /start is issued."""
    user = update.effective_user
    await update.message.reply_text(
        f"سلام {user.first_name}! 🤖\n"
        "به ربات امتیازدهی خوش آمدید!\n\n"
        "دستورات:\n"
        "/start - نمایش این پیام\n"
        "/balance - نمایش امتیاز\n"
        "/invite - دریافت کد دعوت"
    )

async def balance(update: Update, context: ContextTypes.DEFAULT_TYPE):
    """Show user balance."""
    await update.message.reply_text("💰 امتیاز شما: 0\n\nبه زودی قابلیت‌ها اضافه میشن!")

async def invite(update: Update, context: ContextTypes.DEFAULT_TYPE):
    """Generate referral link."""
    user_id = update.effective_user.id
    invite_link = f"https://t.me/{(await context.bot.get_me()).username}?start={user_id}"
    await update.message.reply_text(
        f"🔗 لینک دعوت شما:\n`{invite_link}`\n\n"
        "با این لینک دوستاتون رو دعوت کنید!",
        parse_mode='Markdown'
    )

def main():
    """Start the bot."""
    if not TOKEN:
        logging.error("BOT_TOKEN not found in environment variables!")
        return

    # Create application
    application = Application.builder().token(TOKEN).build()

    # Add handlers
    application.add_handler(CommandHandler("start", start))
    application.add_handler(CommandHandler("balance", balance))
    application.add_handler(CommandHandler("invite", invite))

    # Start the Bot
    application.run_polling()
    logging.info("Bot started successfully!")

if __name__ == '__main__':
    main()
