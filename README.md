import discord
from discord import app_commands
from discord.ext import commands
import openai
import os
from dotenv import load_dotenv

# Charger les variables d'environnement
load_dotenv()
DISCORD_TOKEN = os.getenv("DISCORD_TOKEN")
OPENAI_API_KEY = os.getenv("OPENAI_API_KEY")

openai.api_key = OPENAI_API_KEY

# Configuration du bot
intents = discord.Intents.default()
bot = commands.Bot(command_prefix="/", intents=intents)

@bot.event
async def on_ready():
    print(f"{bot.user} est connecté !")
    try:
        synced = await bot.tree.sync()
        print(f"Commandes slash synchronisées : {len(synced)}")
    except Exception as e:
        print(f"Erreur de synchronisation : {e}")

@bot.tree.command(name="imagine", description="Générer une image avec l'aide de ChipsBotNP")
async def imagine(interaction: discord.Interaction, prompt: str):
    await interaction.response.defer()
    try:
        response = openai.Image.create(
            prompt=prompt,
            n=1,
            size="1024x1024"
        )
        image_url = response['data'][0]['url']
        
        await interaction.followup.send(f"Voici ton image : {image_url}")
    except Exception as e:
        await interaction.followup.send(f"Une erreur s'est produite : {e}")

bot.run(DISCORD_TOKEN)
