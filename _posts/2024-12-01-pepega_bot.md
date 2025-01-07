---
layout: post
title:  "Pepega Bot"
date:   2024-12-01 19:17:58 -0300
categories: python twitchio flask postgresql
---

## What is this repository about?

This is a simple Twitch bot made using the [twitchio](https://twitchio.dev/en/stable/) library with a diversaty of commands that will be shown below, some even use another python library called [flask](https://flask.palletsprojects.com/en/stable/).

---

## Structure

I organized this project really poorly and I admit that, but since this was just a small scale project to test my abilities it didn't really matter...

![structure](/Midia/pepegabot_structure.png)

1. **pepegabot.py**

    This file is the main file of the bot, the one responsible for handling the core functionality, such as connecting to Twitch channels, managing commands, and responding to events. It initializes the bot using the `twitchio` library, loads commands dynamically from the `commands` directory, and listens for specific messages or commands in chat. Key features include:

    - **Dynamic Command Loading**: Automatically loads Python files from the `commands` directory, skipping excluded files like `__init__.py`.

    - **Event Handling**:

        - `event_ready`: Notifies when the bot is successfully logged in and ready to interact.
        - `event_message`: Processes incoming chat messages, responds to specific keywords (like forsen), and manages reminders for certain messages.
        - `event_command_error`: Handles errors related to invalid or improperly used commands, providing detailed feedback to users.

2. **Command Directory**

    This directory contains the custom commands used by the bot. Each Python file represents a command or set of related commands. These files are dynamically loaded into the bot at runtime, allowing for modular and easily extendable functionality.

    1. `acc_age`

        **Description**: Retrieves and displays the account creation date and age (in years) of the specified user or the command sender.

        ```python
        import time
        from twitchio.ext import commands
        from twitchio.user import User

        @commands.command()
        async def acc_age(ctx: commands.Context, user: User = None):
            if not user:
                user = ctx.author
                
            full_creation_date = user.created_at.strftime('%d/%m/%Y %H:%M:%S')
                
            creation_year = user.created_at.strftime('%Y')
            
            current_year = time.localtime().tm_year
            
            age = current_year - int(creation_year)
            
            await ctx.send(f'{user.name}\'s account was created on {full_creation_date} and is {age} years old.')
        ```

    2. `acc_info`

        **Description**: Displays detailed information about a Twitch user, including account creation date, age, ID, broadcaster type, and profile image.

        ```python
        import time
        from twitchio.ext import commands
        from twitchio.user import User

        @commands.command()
        async def acc_info(ctx: commands.Context, user: User):
            full_creation_date = user.created_at.strftime('%d/%m/%Y %H:%M:%S')
            creation_year = user.created_at.strftime('%Y')
            current_year = time.localtime().tm_year
            age = current_year - int(creation_year)
            
            user_name = user.name
            user_broad_castertype = user.broadcaster_type.name
            user_email = user.email
            user_id = user.id
            user_image = user.profile_image
            
            await ctx.send(f"@{user_name} account is {age} years old, created at {full_creation_date}. Id: {user_id}, type: {user_broad_castertype}, profile image: {user_image}")

        ```

    3. `death`

        **Description**: Increments a death counter stored in the database and displays the updated count in chat.

        ```python
        from db import add_death, show_deaths
        from twitchio.ext import commands

        @commands.command()
        async def death(ctx: commands.Context):
            add_death()
            await ctx.send(f'deaths: {show_deaths()} DISASTER')
        ```

    4. `DinkDonk`

        **Description**: Sends a playful "DinkDonk" message targeting a specific user, repeated a specified number of times (maximum 15).

        ```python
        import asyncio
        from twitchio.ext import commands

        @commands.command()
        async def DinkDonk(ctx: commands.Context, user: str, amount: int):
            """Type a a user name and an amount of times to DinkDonk them"""
            
            if amount > 15:
                await ctx.send(':) Too much tomfoolery ')
            else:
                for _ in range(amount):
                    await ctx.send(f'{user} DinkDonk')
                    await asyncio.sleep(0.5)
        ```

    5. `getgame`

        **Description**: Fetches and displays the current game being played by the streamer hosting the channel.

        ```python
        from twitchio.ext import commands

        @commands.command()
        async def getgame(ctx: commands.Context):
            streamer = ctx.channel.name
            channel_info = await ctx.bot.client.fetch_channel(streamer)
            game_name = channel_info.game_name

            if channel_info and hasattr(channel_info, 'game_name') and game_name:
                await ctx.send(f'@{streamer} is currently playing "{game_name}"')
            else:
                await ctx.send(f'No game found, are you okay @{streamer} monkaS ?')
        ```

    6. `hello`

        **Description**: Greets the user who invoked the command with a personalized hello message.

        ```python
        from twitchio.ext import commands

        @commands.command()
        async def hello(ctx: commands.Context):
            await ctx.send(f'hello @{ctx.author.name}')
        ```

    7. `help`

        **Description**: Lists all available commands by dynamically reading the commands directory and displaying them in chat.

        ```python
        import os
        from twitchio.ext import commands

        @commands.command()
        async def help(ctx: commands.Context, *args):
            command_files = [f[:-3] for f in os.listdir('commands') if f.endswith('.py') and f != '__init__.py']
            command_list = ', '.join(command_files)
            
            await ctx.send(f'Available commands: {command_list}')
        ```

    8. `pfp`

        **Description**: Retrieves and displays the profile image URL of the specified user.

        ```python
        import time
        from twitchio.ext import commands
        from twitchio.user import User

        @commands.command()
        async def pfp(ctx: commands.Context, user: User):
            
            user_image = user.profile_image
            
            await ctx.send(user_image)
        ```

    9. `ping`

        **Description**: Lists the names of all chat participants in the current channel.

        ```python
        from twitchio.ext import commands

        @commands.command()
        async def ping(ctx: commands.Context):
            chatters = ctx.channel.chatters
            chatter_names = [chatter.name for chatter in chatters]
            await ctx.send(', '.join(chatter_names))
        ```

    10. `pyramid`

        **Description**: Creates a pyramid in chat using a specified string (bricks) and height (amount). Builds up and then decreases symmetrically.

        ```python
        from twitchio.ext import commands
        import asyncio

        @commands.command()
        async def pyramid(ctx: commands.Context, bricks: str, amount: int):
            if amount < 1 or amount == 0:
                await ctx.send('You need to provide a number greater than 0 and a non-empty string for bricks')
                return
            print(len(bricks))
            # Increasing part
            for i in range(1, amount + 1):
                await ctx.send(f'{bricks} ' * i)
                await asyncio.sleep(0.5)
            # Decreasing part
            for i in range(amount - 1, 0, -1):
                await ctx.send(f'{bricks} ' * i)
                await asyncio.sleep(0.5)
        ```

    11. `remove_death`

        **Description**: Decrements the death counter stored in the database. Only the user flamingo_lindo can invoke this command.

        ```python
        from db import minus_death, show_deaths
        from twitchio.ext import commands

        @commands.command()
        async def remove_death(ctx: commands.Context):
            sender = ctx.author.name

            if sender != 'flamingo_lindo':
                await ctx.send(f'Only flamingo_lindo can remove deaths')
                return

            minus_death()
            await ctx.send(f'deaths: {show_deaths()} :-(')
        ```

    12. `thumb`

        **Description**: Fetches and displays the thumbnail URL of a live stream for a specified user. If the user is not live, informs the chat.

        ```python
        from twitchio.ext import commands
        from twitchio.user import User

        @commands.command()
        async def thumb(ctx: commands.Context, user: User):

            user_id = user.id

            stream = await ctx.bot.fetch_streams(user_ids=[user_id])
            
            if stream:
                thumb = stream[0].thumbnail_url
                thumb = thumb.replace('{width}', '280').replace('{height}', '160')
                await ctx.send(thumb)
            else:
                await ctx.send(f"{user.name} is not live right now PepeHands")
        ```

3. **Database Connection**

    This module interacts with a PostgreSQL database to manage a death counter for tracking purposes, such as in Twitch streams. Below is a description of its functions:

    - `show_deaths`

    **Purpose**: Fetches and returns the current value of the death counter from the database.  

    **Steps**:  
    - Connects to the database using the credentials (`DB_NAME`, `DB_USER`, `DB_PASS`) loaded from environment variables.  
    - Executes a SQL query to select all rows from the `deaths` table in the `public` schema.  
    - Fetches the first row of the result.  
    - Closes the database connection.  
    - Returns the value of `death_qnt` (assumed to be the second column in the row, `buh[1]`).  

    - `add_death`

    **Purpose**: Increments the death counter by 1 in the database.  

    **Steps**:  
    - Connects to the database.  
    - Executes a SQL query to update the `deaths` table by adding 1 to the `death_qnt` column.  
    - Commits the transaction to save the change.  
    - Closes the database connection.  

    - `minus_death`

    **Purpose**: Decrements the death counter by 1 in the database.  

    **Steps**:  
    - Connects to the database.  
    - Executes a SQL query to update the `deaths` table by subtracting 1 from the `death_qnt` column.  
    - Commits the transaction to save the change.  
    - Closes the database connection.  

    ```python
    import psycopg2
    import os
    from dotenv import load_dotenv

    load_dotenv()

    name = os.getenv("DB_NAME")
    user = os.getenv("DB_USER")
    password = os.getenv("DB_PASS")

    def show_deaths():
        conn = psycopg2.connect(dbname=name, user=user, password=password)

        cur = conn.cursor()

        # get death counter
        cur.execute("SELECT * FROM public.deaths")
        conn.commit()

        buh = cur.fetchone()

        conn.close()

        return buh[1]
        
    def add_death():
        conn = psycopg2.connect(dbname=name, user=user, password=password)

        cur = conn.cursor()

        # add 1 to death counter
        cur.execute("UPDATE public.deaths SET id=1, death_qnt=death_qnt+1")

        conn.commit()

        conn.close()


    def minus_death():
        conn = psycopg2.connect(dbname=name, user=user, password=password)

        cur = conn.cursor()

        # add 1 to death counter
        cur.execute("UPDATE public.deaths SET id=1, death_qnt=death_qnt-1")

        conn.commit()

        conn.close()
    ```

4. **Death Counter Display**

This Flask web application provides a simple interface to display the death counter managed by the database. It uses the `db` module for fetching the death count and dynamically updates the count on the webpage.

---

## How It Works

### Routes
1. **`/` (Root Route)**  
   - **Purpose**: Serves the main webpage where the death counter is displayed.  
   - **Implementation**:  
     - Returns an HTML page with a `div` for displaying the death count.
     - Includes a JavaScript script to periodically fetch the current death count from the `/deaths` route.  
     - The count is updated every 5 seconds using `setInterval`.

2. **`/deaths`**  
   - **Purpose**: Provides the current death count as a JSON object.  
   - **Implementation**:  
     - Calls the `show_deaths()` function from the `db` module to get the death count.
     - Returns the count as a JSON response in the format `{"deaths": <count>}`.

---

### Key Features
- **Dynamic Updates**:  
  The death count is fetched and updated on the webpage every 5 seconds using JavaScript's `fetch()` API.

- **JSON API**:  
  The `/deaths` route serves as an API endpoint to provide the current death count in JSON format, making it easy to integrate with other applications or services.

---

### Usage

1. **Run the App**:  
   Use the following commands to run the app:
   ```bash
   python -m flask --app web_death run

```python
from flask import Flask
from db import show_deaths
# python -m flask --app web_death run
# python -m flask --app  web_death --debug run

app = Flask(__name__)

@app.route("/")
def death_counter():
    return """
    <!DOCTYPE html>
    <html>
    <body>
        <div style="color: white; font-size: 80px; class="death-counter" id="death-counter">Deaths: Loading...</div>
        <script>
            async function fetchDeaths() {
                try {
                    const response = await fetch('/deaths');
                    const data = await response.json();
                    document.getElementById('death-counter').textContent = `Deaths: ${data.deaths}`;
                } catch (error) {
                    console.error('Error fetching deaths:', error);
                }
            }
            // Fetch the deaths every 5 seconds
            setInterval(fetchDeaths, 5000);
            // Fetch immediately on load
            fetchDeaths();
        </script>
    </body>
    </html>
    """

@app.route("/deaths")
def get_deaths():
    deaths = show_deaths()
    return {"deaths": deaths}  # Return as JSON
```