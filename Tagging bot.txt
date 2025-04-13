import asyncio
import random
from telethon import TelegramClient, events
from telethon.tl.functions.messages import GetDialogsRequest
from telethon.tl.types import InputPeerEmpty

api_id = 22443811
api_hash = '923a6f2eaaf49c2e0fbb5b7c4701bbc7'
bot_token = '7643526438:AAGg9Ii4PazKu9sqtmoPa9gS8V-_lUG4POc'

client = TelegramClient('bot', api_id, api_hash).start(bot_token=bot_token)

tagging_active = {}

funny_messages = [
    "किधर चले सब? पार्टी तो अभी शुरू हुई है!",
    "रुको! सबको mention कर रहा हूँ!",
    "चलो भाई, उठ जाओ! Attendance हो रही है!",
    "अरे वाह, इतने सुंदर लोग एक जगह!",
    "किसी ने चाय मंगवाई क्या?",
    "हाज़िरी लगाओ, नहीं तो मीटिंग कैंसिल!",
    "बोट आ गया है सबको परेशान करने!",
    "तुम सबको tag करके मुझे बहुत मज़ा आता है!",
    "इतने बड़े नाम, क्या बात है!",
    "सबको tag कर दिया, अब लोग घर से बाहर नहीं जा सकेंगे!",
    "कहाँ जा रहे हो तुम? Tag कर रहा हूँ!",
    "अब सबको tag karke batao, कौन sabse achha है!",
    "Har tag ek naye adventure ki shuruaat hai!",
    "Tagging ka magic sabko dikhega!",
    "Tagging ke baad sab sabse zyada important लगते हैं!",
    "Tagging made easy, बस एक click और पूरा group active!",
    "Aapko tag karke लगा ki kuch khas kiya hoon!"
]

@client.on(events.NewMessage(pattern='/start'))
async def on_start(event):
    sender = await event.get_sender()
    name = sender.first_name
    welcome_msg = (
        f"नमस्ते {name}!\n\n"
        "मैं एक Tagging बोट हूँ जो एक-एक करके सभी group members को tag करता हूँ "
        "random मजेदार message के साथ!\n\n"
        "Commands:\n"
        "/tagall - सभी को tag करो\n"
        "/cancel - Tagging बंद करो\n\n"
        "Regards,\n"
        "Adi @Adi_11250 और True Love"
    )
    await event.respond(welcome_msg)

@client.on(events.NewMessage(pattern='/tagall'))
async def tag_all(event):
    if not event.is_group:
        await event.reply("यह command केवल groups में काम करती है।")
        return

    if event.chat_id in tagging_active and tagging_active[event.chat_id]:
        await event.reply("Tagging पहले से चालू है। कृपया /cancel भेजें बंद करने के लिए।")
        return

    tagging_active[event.chat_id] = True
    users = await client.get_participants(event.chat_id)

    await event.reply("Tagging शुरू हो गई है...")

    for user in users:
        if not tagging_active.get(event.chat_id):
            await event.respond("Tagging बंद कर दी गई है।")
            break
        if user.bot or user.deleted:
            continue
        name = user.first_name or "user"
        message = random.choice(funny_messages)
        try:
            await client.send_message(event.chat_id, f"{message} [{name}](tg://user?id={user.id})", parse_mode='md')
            await asyncio.sleep(2)  # Flood wait avoid करने के लिए
        except Exception as e:
            print(f"Error tagging {name}: {e}")
            continue

    tagging_active[event.chat_id] = False
    await event.respond("Tagging खत्म हुई।")

@client.on(events.NewMessage(pattern='/cancel'))
async def cancel_tag(event):
    if tagging_active.get(event.chat_id):
        tagging_active[event.chat_id] = False
        await event.reply("Tagging बंद कर दी गई है।")
    else:
        await event.reply("Tagging पहले से बंद है।")

print("Bot is running...")
client.run_until_disconnected()
