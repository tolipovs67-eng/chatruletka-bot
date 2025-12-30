# chatruletka-bot
import telebot

TOKEN = "7997163437:AAExVtNPV6hKELGxR1SQbYfP26Z-M8lNXAk"
bot = telebot.TeleBot(TOKEN)

waiting_users = []
chats = {}

@bot.message_handler(commands=["start"])
def start(m):
    bot.send_message(
        m.chat.id,
        "Salom! 👋\nKeyingisi tugmasini bosing"
    )

@bot.message_handler(func=lambda m: m.text == "Keyingisi")
def find_partner(m):
    uid = m.chat.id

    if uid in chats:
        partner = chats.pop(uid)
        chats.pop(partner, None)
        bot.send_message(partner, "❌ Suhbat tugadi")

    if uid in waiting_users:
        waiting_users.remove(uid)

    if waiting_users:
        partner = waiting_users.pop(0)
        chats[uid] = partner
        chats[partner] = uid
        bot.send_message(uid, "✅ Suhbatdosh topildi")
        bot.send_message(partner, "✅ Suhbatdosh topildi")
    else:
        waiting_users.append(uid)
        bot.send_message(uid, "🔍 Qidirilmoqda...")

@bot.message_handler(content_types=["text"])
def relay(m):
    uid = m.chat.id
    if uid in chats:
        bot.send_message(chats[uid], m.text)

print("Bot ishga tushdi...")
bot.infinity_polling()
