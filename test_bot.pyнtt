import asyncio
import os
import logging
from dotenv import load_dotenv
from telethon import TelegramClient, events
from telethon.tl.types import MessageMediaDocument, DocumentAttributeVideo

logging.basicConfig(level=logging.INFO)
load_dotenv()

# Загружаем данные из .env
BOT_TOKEN = os.getenv('BOT_TOKEN')
API_ID = int(os.getenv('API_ID'))
API_HASH = os.getenv('API_HASH')

def get_duration_from_message(message):
    """Универсальная функция для получения длительности из любого медиа"""
    # Проверяем голосовые
    if message.voice:
        # В голосовых длительность может быть в разных местах
        if hasattr(message.voice, 'duration'):
            return message.voice.duration
        # Ищем в атрибутах
        if hasattr(message.voice, 'attributes'):
            for attr in message.voice.attributes:
                if hasattr(attr, 'duration'):
                    return attr.duration
    
    # Проверяем видеосообщения (кружочки)
    elif message.video_note:
        # В видео-кружочках длительность может быть в разных местах
        if hasattr(message.video_note, 'duration'):
            return message.video_note.duration
        if hasattr(message.video_note, 'attributes'):
            for attr in message.video_note.attributes:
                if hasattr(attr, 'duration'):
                    return attr.duration
    
    # Проверяем обычные видео
    elif message.video:
        if hasattr(message.video, 'duration'):
            return message.video.duration
    
    return 0  # если не нашли длительность

async def main():
    # Создаем клиента
    client = TelegramClient('bot_session', API_ID, API_HASH)
    
    # Запускаем как бота
    await client.start(bot_token=BOT_TOKEN)
    
    # Проверяем, что запустились
    me = await client.get_me()
    print(f"✅ Бот @{me.username} успешно запущен!")
    
    # Обработчик всех сообщений
    @client.on(events.NewMessage)
    async def handler(event):
        if event.out:  # игнорируем свои сообщения
            return
        
        # Определяем тип сообщения
        message_type = "неизвестный тип"
        duration = 0
        
        if event.message.voice:
            message_type = "голосовое"
            duration = get_duration_from_message(event.message)
            print(f"📢 Голосовое от {event.sender_id}, длительность: {duration} сек")
            await event.reply(f"🎤 Нашел голосовое! Длительность: {duration} сек")
            
        elif event.message.video_note:
            message_type = "видеосообщение (кружочек)"
            duration = get_duration_from_message(event.message)
            print(f"📹 Кружочек от {event.sender_id}, длительность: {duration} сек")
            await event.reply(f"🎥 Нашел видеокружочек! Длительность: {duration} сек")
            
        elif event.message.video:
            message_type = "обычное видео"
            duration = get_duration_from_message(event.message)
            print(f"🎬 Видео от {event.sender_id}, длительность: {duration} сек")
            await event.reply(f"📽 Нашел видео! Длительность: {duration} сек")
            
        elif event.message.text:
            message_type = "текст"
            print(f"📝 Текст от {event.sender_id}: {event.message.text[:50]}...")
            await event.reply(f"Получил текст: {event.message.text[:50]}...")
            
        else:
            print(f"❓ Другой тип сообщения от {event.sender_id}")
            await event.reply("Это не голосовое, не видео и не текст")
    
    print("🤖 Бот слушает сообщения...")
    print("   Поддерживаются: голосовые, видеокружочки, обычные видео, текст")
    await client.run_until_disconnected()

if __name__ == '__main__':
    asyncio.run(main())
