# System-Bot-discord.py

import discord
from discord.ext import commands
from discord import member
from discord.ext.commands import has_permissions, MissingPermissions
import asyncio
import os
import datetime
import json
import random

client = commands.Bot(command_prefix='$')#مكان البريف
client.remove_command("help")

@client.event 
async def on_ready():
    await client.change_presence(status=discord.Status.idle, activity=discord.Game(f"$help , $h | servers: {len(client.guilds)}"))
    print("im online")
    print("----------")
        
#reply
@client.command()
async def hi(ctx):
    await ctx.reply("**hello there**")

@client.command()
async def no(ctx):
  embed = discord.Embed(
  title="Verify Your Self",
  description="React To Get Verifed",
  color =0x607d8b)
  await ctx.send(embed=embed)


@client.command(aliases = ['l'])
async def line(ctx):
    await ctx.send("https://cdn.discordapp.com/attachments/820639873547501589/942489100152352898/Line.gif")

@client.command()

async def mention(ctx):
  await ctx.reply("** My prefix is `$`**")


#ping command
@client.command()
async def ping(ctx):
    latency = round(client.latency * 1000, 1)
    await ctx.reply(f"**pong!: {latency} Ms**")


#clear command
@client.command(aliases = ['cl'])
@commands.has_permissions(manage_messages = True)
async def clear(ctx, amount = 2):
    await ctx.channel.purge(limit = amount+1)
    await ctx.send(f"**{amount} Messages cleared**")
    

#kick
@client.command(aliases = ['k'])
@commands.has_permissions(kick_members = True)
async def kick(ctx, member: discord.Member, *,reason=None):
    if reason == None:
        reason = reason
    await ctx.guild.kick(member)
    guild = ctx.guild
    embed = discord.Embed(
        title = "Kicked",
        description = f"{member.mention} **kicked from the server**",
        colour = discord.Colour.red())
    
    embed.add_field(name = "reason:", value= reason, inline=False)
    await ctx.send(embed=embed)
    await member.send(f"**You were kicked from** {guild.name}")

#ban
@client.command(aliases = ['b'])
@commands.has_permissions(ban_members = True)
async def ban(ctx, user: discord.Member, *,reason=None):
    await member.ban(reason=reason)
    guild = ctx.guild
    embed = discord.Embed(
        title = "Banned",
        description = f"{member.mention} **Banned from the server**",
        colour = discord.Colour.red())
    embed.add_field(name = "reason:", value= reason, inline=False)
    await ctx.send(embed=embed)
    await member.send(f"**You are banned from **{guild.name}")

#unban 
@client.command(aliases = ['ub'])
@commands.has_permissions(ban_members = True)
async def unban(ctx, *, member):
  bannedUsers = await ctx.guild.bans()
  name, discrimator = member.split("#")
  guild = ctx.guild
    

  for ban_entry  in bannedUsers:
      user = ban_entry.user

      if (user.name, user.discriminator) == (name, discrimator):
            await ctx.guild.unban(user)
            await ctx.send(f"{user.mention} **Unbanned from the server**")
            await member.send(f"**You are Unbanned from **{guild.name}")
            
          

#mute
@client.command(aliases = ['mt'])
@commands.has_permissions(manage_messages = True)
async def mute(ctx, member: discord.Member,*,reason=None):
    guild = ctx.guild
    mutedRole = discord.utils.get(guild.roles ,name = "Muted")

    if not mutedRole:
        mutedRole = await guild.create_role(name= "Muted")
        for channel in guild.channels:
            await channel.set_permissions(mutedRole ,speak = False, send_messages=False, add_reaction=False)
        
    await member.add_roles(mutedRole, reason=reason)
    await ctx.send(f"{member.mention}** is muted **")
    await member.send(f"**You got muted in **{guild.name}")

#unmute
@client.command(aliases = ['umt'])
@commands.has_permissions(manage_messages = True)
async def unmute(ctx, member: discord.Member,*,reason=None):
    guild = ctx.guild
    mutedRole = discord.utils.get(ctx.guild.roles ,name = "Muted")

    await member.remove_roles(mutedRole)
    await ctx.send(f"{member.mention}** is unmuted**")
    await member.send(f"**You were unmuted in **{guild.name}")

#avatar command
@client.command(aliases = ['av'])
async def avatar(ctx, *, member: discord.Member=None):
    if not member:
        member = ctx.member.author
        userAvatar = member.avatar_url

    embed = discord.Embed(
        title= "**Avatar**",
        color = member.color,
        Timestamp = ctx.message.created_at
    )
    embed.set_author(name=f"Avatar of {member}")
    embed.set_image(url=member.avatar_url)
    embed.set_footer(text=f"Requested by {ctx.author}", icon_url=ctx.author.avatar_url)

    await ctx.reply(embed=embed)

#lock chat
@client.command(aliases = ['lc'])
@commands.has_permissions(manage_channels=True)
async def lock(ctx):
    await ctx.channel.set_permissions(ctx.guild.default_role, send_messages=False)
    await ctx.reply(ctx.channel.mention + " **Is locked now!**")

#unlock chat
@client.command(aliases = ['ulc'])
@commands.has_permissions(manage_channels=True)
async def unlock(ctx):
    await ctx.channel.set_permissions(ctx.guild.default_role, send_messages=True)
    await ctx.reply(ctx.channel.mention + " **Is Unlocked now!**")

#nick name 
@client.command(pass_context=True,aliases = ["nick"])
async def nickname(ctx, member: discord.Member, nick):
    await member.edit(nick=nick)
    await ctx.reply(f'**Name Changed To {member.mention} ✅**')

#love scale
@client.command()
async def love(ctx,*, message:str):
  a = ctx.author.mention
  love=discord.Embed(description=f"**{a} Loves  {message}  ({random.randint(0,101)}%)**:hearts:")
  await ctx.reply(embed=love, mention_author=False)
#kiss command
@client.command()
async def kiss(ctx,*,k:discord.Member=None):
    if k == None:
        await ctx.reply("**Mention Someone To Kiss**")
    a = ctx.author.mention
    kiss_gifs = ["https://cdn.discordapp.com/attachments/931617398656729209/931645085119021087/anime-kiss1.gif","https://cdn.discordapp.com/attachments/931617398656729209/931645085806915684/kiss-anime2.gif","https://cdn.discordapp.com/attachments/931617398656729209/931645086599614534/kiss-anime3.gif","https://cdn.discordapp.com/attachments/931617398656729209/931645086998069328/koi-to-uso-anime4.gif"]
    random_kiss=random.choice(kiss_gifs)
    kiss_embed = discord.Embed(description=f"{a}** Kissed **{k.mention}.")
    kiss_embed.set_image(url=random_kiss)
    await ctx.reply(embed=kiss_embed)


#hug command
@client.command()
async def hug(ctx,*,k:discord.Member=None):
    if k == None:
        await ctx.reply("**Mention Someone To Hug**")
    a = ctx.author.mention
    hug_gifs = ["https://cdn.discordapp.com/attachments/931671103213023262/931887026490466304/hug1.gif","https://cdn.discordapp.com/attachments/931671103213023262/931887246100021318/horimiya-hug-anime.gif","https://cdn.discordapp.com/attachments/931671103213023262/931887407266140210/hugging-hug.gif"]
    random_kiss=random.choice(hug_gifs)
    hug_embed = discord.Embed(description=f"{a}** Hugged **{k.mention}.")
    hug_embed.set_image(url=random_kiss)
    await ctx.reply(embed=hug_embed,mention_author=False)




#---------------
@client.command()
async def add(ctx):
    embed=discord.Embed(title="**Add the bot**",
     url="https://discord.com/api/oauth2/authorize?client_id=947568678050877462&permissions=1644972474359&scope=bot%20applications.commands",
      color=0x607d8b
      )
    embed.set_author(name=ctx.author.display_name , icon_url=ctx.author.avatar_url)
    await ctx.reply(embed=embed)

@client.command()
async def sup(ctx):
    embed=discord.Embed(title="**Support server**",
     url="https://discord.gg/KKfwYfj78G",
      color=0x607d8b)
    embed.set_author(name=ctx.author.display_name , icon_url=ctx.author.avatar_url)
    await ctx.reply(embed=embed)


  


#roles
@client.command('role',aliases = ['r'])
@commands.has_permissions() 
async def role(ctx, user : discord.Member, *, role : discord.Role):
  if role.position > ctx.author.top_role.position: 
    return await ctx.send("**:x: | You can't give this role!**") 
  if role in user.roles:
      await user.remove_roles(role) 
      await ctx.send(f"**Removed {role} from {user.mention}**")
  else:
      await user.add_roles(role) 
      await ctx.send(f"**Added {role} to {user.mention}**")
@role.error
async def role_error(ctx, error):
  if isinstance(error, MissingPermissions):
    await ctx.send('**:x: | You do not have permission to use this command!**') 

#Hide channel 
@client.command()
@commands.has_permissions(manage_channels=True)
async def hide(ctx, channel: discord.TextChannel = None):
    channel = channel or ctx.channel
    overwrite = channel.overwrites_for(ctx.guild.default_role)
    overwrite.read_messages = False
    await channel.set_permissions(ctx.guild.default_role, overwrite=overwrite)
    await ctx.reply(f'**channel has been Hidden {channel.mention} By {ctx.author.mention}**')

#Show channel
@client.command()
@commands.has_permissions(manage_channels=True)
async def show(ctx, channel: discord.TextChannel = None):
    channel = channel or ctx.channel
    overwrite = channel.overwrites_for(ctx.guild.default_role)
    overwrite.read_messages = True
    await channel.set_permissions(ctx.guild.default_role, overwrite=overwrite)
    await ctx.reply(f'**channel has been shown {channel.mention} By {ctx.author.mention}**')


#dm user 
@client.command()
async def dm(ctx,member:discord.Member,*,content):
    channel = await member.create_dm() 
    await channel.send(content)  


    

#help command
@client.command(aliases = ['h','H'])
async def help(ctx):
    embed = discord.Embed(
        title = "**help Page**",
        description= "**All the available commands**",
        color = 0x607d8b
    )
    embed.set_thumbnail(url=client.user.avatar_url)
    embed.set_author(name=ctx.author.display_name , icon_url=ctx.author.avatar_url)
    embed.add_field(name="**Admins Help Page**" , value="**-------**" , inline=False)
    embed.add_field(name="**● `1` -`Clear messages`**" , value="**`$clear` , `$cl`**\n\nUse **`$help_clear`** to see how the command works ",
     inline=False)
    embed.add_field(name="**● `2` -`Mute member`**" , value="**`$mute` , `$mt`**\n\nUse **`$help_mute`** to see how the command works " ,
     inline=False)
    embed.add_field(name="**● `3` -`Unmute member`**" , value="**`$unmute` , `$umt`**\n\nUse **`$help_unmute`** to see how the command works " ,
     inline=False)
    embed.add_field(name="**● `4` -`Ban member`**" , value="**`$ban` , `$b`**\n\nUse **`$help_ban`** to see how the command works " ,
     inline=False)
    embed.add_field(name="**● `5` -`Unban member`**" , value="**`$unban` , `$ub`**\n\nUse **`$help_unban`** to see how the command works " ,
     inline=False)
    embed.add_field(name="**● `6` -`Kick member`**" , value="**`$kick` , `$k`**\n\nUse **`$help_kick`** to see how the command works " ,
     inline=False)
    embed.add_field(name="**● `7` -`Lock channel`**" , value="**`$lock` , `$lc`**\n\nUse **`$help_lock`** to see how the command works " ,
     inline=False)
    embed.add_field(name="**● `8` -`Unlock channel`**" , value="**`$unlock` , `$ulc`**\n\nUse **`$help_unlock`** to see how the command works " ,
     inline=False)
    embed.add_field(name="**● `9` -`Give/Remove Role`**" , value="**`$role` , `$r`**\n\nUse **`$help_role`** to see how the command works " ,
     inline=False)
    embed.add_field(name="**● `10` -`Hide channel`**" , value="**`$hide`**\n\nUse **`$help_hide`** to see how the command works " ,
     inline=False)
    embed.add_field(name="**● `11` -`Show channel`**" , value="**`$show`**\n\nUse **`$help_show`** to see how the command works " ,
     inline=False)
    embed.add_field(name="**● `12` -`DM a Member`**" , value="**`$dm`**\n\nUse **`$help_dm`** to see how the command works ",
     inline=False)
    k = discord.Embed(
        title = "**Members Help Page**",
        description= "**All the available commands**",
        color = 0x607d8b
    )
    k.set_thumbnail(url=client.user.avatar_url)

    k.add_field(name="**● `1` -`Avatar command`**" , value="**`$avatar` , `$av`**\n\nUse **`$help_avatar`** to see how the command works" ,
     inline=False)   
    k.add_field(name="**● `2` -`Line`**" , value="**`$line` , `$l`**\n\nUse **`$help_line`** to see how the command works " ,
     inline=False)
    k.add_field(name="**● `3` -`Change Nickname`**" , value="**`$nickname` , `$nick`**\n\nUse **`$help_nickname`** to see how the command works " ,
     inline=False)
    k.add_field(name="**● `4` -`Love Scale`**" , value="**`$love`**\n\nUse **`$help_love`** to see how the command works " ,
     inline=False)
    k.add_field(name="**● `5` -`Hug Someone`**" , value="**`$hug`**\n\nUse **`$help_hug`** to see how the command works " 
    , inline=False)
    k.add_field(name="**● `6` -`Kiss Someone`**" , value="**`$kiss`**\n\nUse **`$help_kiss`** to see how the command works " 
    , inline=False)
    j = discord.Embed(
        title = "**About the Bot**",
        description= "**All the available commands**",
        color = 0x607d8b
    )
    j.set_thumbnail(url=client.user.avatar_url)

    j.add_field(name='**● `1` -`Support Server`**', value='**`$support` , `$sup`**\n\nUse **`$help_support`** to see how the command works ',
    inline=False)
    j.add_field(name='**● `2` -`Add the Bot`**', value='**`$add`**\n\nUse **`$help_add`** to see how the command works ',
    inline=False)
    j.add_field(name="**● `3` -`Bot's Ping`**" , value="**`$ping`**\n\nUse **`$help_ping`** to see how the command works " ,
     inline=False) 
    j.set_footer(text="Made By Lucylev.#6070 ")
    await ctx.reply(embed=embed)
    await ctx.send(embed=k)
    await ctx.send(embed=j)

############################################################################################################

@client.command()#mute
async def help_mute(ctx):
    embed = discord.Embed(
        title = "**Mute Help**",
        description= "**$mute `Mention a member`**",
        color = 0x607d8b
    )
    await ctx.reply(embed=embed)

@client.command()#unmute
async def help_unmute(ctx):
    embed = discord.Embed(
        title = "**Unmute Help**",
        description= "**$unmute `Mention a member`**",
        color = 0x607d8b
    )
    await ctx.reply(embed=embed)

@client.command()#clear
async def help_clear(ctx):
    embed = discord.Embed(
        title = "**Clear Help**",
        description= "**$clear `Number of messages`**",
        color = 0x607d8b
    )
    await ctx.reply(embed=embed)

@client.command()#ban
async def help_ban(ctx):
    embed = discord.Embed(
        title = "**Ban Help**",
        description= "**$ban `Mention a member`**",
        color = 0x607d8b
    )
    await ctx.reply(embed=embed)

@client.command()#unban
async def help_unban(ctx):
    embed = discord.Embed(
        title = "**Unban Help**",
        description= "**$unban `name#Tag` `it wont work with the member id`**",
        color = 0x607d8b
    )
    await ctx.reply(embed=embed)

@client.command()#kick
async def help_kick(ctx):
    embed = discord.Embed(
        title = "**Kick Help**",
        description= "**$kick `Mention a member`**",
        color = 0x607d8b
    )
    await ctx.reply(embed=embed)

@client.command()#lock
async def help_lock(ctx):
    embed = discord.Embed(
        title = "**Lock Channel Help**",
        description= "**$lock `Mention The Channel`**",
        color = 0x607d8b
    )
    await ctx.reply(embed=embed)

@client.command()#unlock
async def help_unlock(ctx):
    embed = discord.Embed(
        title = "**Unlock Channel Help**",
        description= "**$unlock `Mention The Channel`**",
        color = 0x607d8b
    )
    await ctx.reply(embed=embed)

@client.command()#roles
async def help_role(ctx):
    embed = discord.Embed(
        title = "**Give/Remove Roles Help**",
        description= "**$role `Mention a member` `Mention The Role`**",
        color = 0x607d8b
    )
    await ctx.reply(embed=embed)

@client.command()#hide
async def help_hide(ctx):
    embed = discord.Embed(
        title = "**Hide Channel Help**",
        description= "**$hide `Mention The Channel`**",
        color = 0x607d8b
    )
    await ctx.reply(embed=embed)

@client.command()#show
async def help_show(ctx):
    embed = discord.Embed(
        title = "**Show Hidden Channel Help**",
        description= "**$show `Mention The Channel`**",
        color = 0x607d8b
    )
    await ctx.reply(embed=embed)

@client.command()#dm
async def help_dm (ctx):
    embed = discord.Embed(
        title = "**Direct Message Help**",
        description= "**$dm `Mention a member` `The Message`**",
        color = 0x607d8b
    )
    await ctx.reply(embed=embed)

@client.command()#avatar
async def help_avatar(ctx):
    embed = discord.Embed(
        title = "**Avatar Help**",
        description= "**$avatar `Mention a member`**",
        color = 0x607d8b
    )
    await ctx.reply(embed=embed)

@client.command()#line
async def help_line(ctx):
    embed = discord.Embed(
        title = "**Line Help**",
        description= "**$line**",
        color = 0x607d8b
    )
    await ctx.reply(embed=embed)

@client.command()#nickname
async def help_nickname(ctx):
    embed = discord.Embed(
        title = "**Nickname Help**",
        description= "**$nickname `Mention a member` `The new nickname`**",
        color = 0x607d8b
    )
    await ctx.reply(embed=embed)

@client.command()#love scale
async def help_love(ctx):
    embed = discord.Embed(
        title = "**Love scale Help**",
        description= "**$love `Mention a member`**",
        color = 0x607d8b
    )
    await ctx.reply(embed=embed)

@client.command()#hug
async def help_hug(ctx):
    embed = discord.Embed(
        title = "**Hug Help**",
        description= "**$hug `Mention a member`**",
        color = 0x607d8b
    )
    await ctx.reply(embed=embed)

@client.command()#kiss
async def help_kiss(ctx):
    embed = discord.Embed(
        title = "**Kiss Help**",
        description= "**$kiss `Mention a member`**",
        color = 0x607d8b
    )
    await ctx.reply(embed=embed)


@client.command()#support
async def help_support(ctx):
    embed = discord.Embed(
        title = "**Support Help**",
        description= "**$support**",
        color = 0x607d8b
    )
    await ctx.reply(embed=embed)

@client.command()#add
async def help_add(ctx):
    embed = discord.Embed(
        title = "**Add Help**",
        description= "**$add**",
        color = 0x607d8b
    )
    await ctx.reply(embed=embed)

@client.command()#ping
async def help_ping(ctx):
    embed = discord.Embed(
        title = "**Ping Help**",
        description= "**$ping**",
        color = 0x607d8b
    )
    await ctx.reply(embed=embed)
###########################################################################################################

client.run(TOKEN)
