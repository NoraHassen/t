import logging
from telegram import Update
from telegram.ext import ApplicationBuilder, CommandHandler, MessageHandler, filters, ContextTypes
import yfinance as yf
import pandas_ta as ta

logging.basicConfig(level=logging.INFO)

async def start(update: Update, context: ContextTypes.DEFAULT_TYPE):
    await update.message.reply_text("أهلاً بيك! ابعتلي كلمة 'تحليل الذهب' أو صورة للشموع وأنا هحللها لك.")

async def handle_message(update: Update, context: ContextTypes.DEFAULT_TYPE):
    text = update.message.text.lower()
    if "ذهب" in text or "تحليل" in text:
        df = yf.download("GC=F", period="1mo", interval="1h")
        rsi = df.ta.rsi().iloc[-1]
        trend = "صاعد" if rsi > 50 else "هابط"
        await update.message.reply_text(
            f"تحليل الذهب:\n- الاتجاه: {trend}\n- مؤشر RSI: {rsi:.2f}\n- نصيحة: لو هتدخل سكالبينج، تابع كسر المقاومة أو الدعم."
        )
    else:
        await update.message.reply_text("ابعتلي سؤال عن الذهب أو صورة للشموع.")

async def handle_photo(update: Update, context: ContextTypes.DEFAULT_TYPE):
    await update.message.reply_text("استلمت الصورة! جاري تحليلها بالذكاء الاصطناعي (الميزة دي تحت التطوير).")
    # هنا ممكن تضيف كود تحليل الصورة لاحقاً

async def main():
    app = ApplicationBuilder().token("YOUR_BOT_TOKEN").build()
    app.add_handler(CommandHandler("start", start))
    app.add_handler(MessageHandler(filters.TEXT & ~filters.COMMAND, handle_message))
    app.add_handler(MessageHandler(filters.PHOTO, handle_photo))
    await app.run_polling()

if __name__ == "__main__":
    import asyncio
    asyncio.run(main())تليجرام
