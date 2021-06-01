- ObbotMaster2020 Challenge
  
  1. Front Audience
- Every content has to have a Front Design audience, e.g. Devforum, Team Cookie
  
  2. Front Intro
- Every content must have an Avatar with an intro, e.g. theme, splash, wow factor
  
  3. Front Matter
- Every content must have a front matter, e.g. user interface, client
  
  4. Back Matter
- Every content has to have a back matter, e.g. server
  
  5. Back Outro
- Every content must have an Avatar with an outro, e.g. follow, like, subscribe
  
  6. Back Audience
- Every content has to have a Back Design audience, Model, Plugin
- Day 1 - Making a Projectile
  
  **Front Audience**
- Team Cookie
  
  **Front Matter**
- Player (Todo: or bot) activates projectile
- Projectile starts from player (Todo: or point)
- Projectile tweens linearly to target
- intDamage: damage to humanoid (default: 40)
- dblTweentime: tween time, higher time means slower projectile (default: 2.2 seconds)
- intMaxrange: maximum distance of projectile (default: 150)
  
  **Back Matter**
- Projectile disappears after dblTweentime
- Projectile disappears after hit
- Player hit gets damaged (Todo: or killed)
  
  **Back Audience**
- Model (Todo: or plugin)
- Day 2 - Participants and HUD
  
  **Front Audience**
- Team Cookie
  
  **Front Matter**
- Minimum participants of 2 players
- Wait time begins during non-game time and after minimum Participants reached
- Wait time cancels if number of Participants drops below minimum
- Participants who were killed, or completed, can spectate
- Participant activates spectate in waiting area
- HUD countdown / game time, leaderstats, spectate
  
  **Back Matter**
- Replicated storage table Participant, moved to Leaderboard upon Killed, Success, or Failure.
- Internal table Leaderboard.intTimePlayed, Leaderboard.intRank, Leaderboard.txtStatus = {Passed, Wizard, Failed, Timeout, Disconnected}
- Non-Participants cannot spectate
- Non-Participants waiting area is the same as Participants who can spectate
  
  **Back Audience**
- ParticipantService, Spectate (Roblox Model)
- Day 3 - Participants and Spectator
  
  **Front Audience**
- DevForum
  
  **Front Matter**
- Leaderboard ranks by Participant.boolCompleted==true, and shortest Participant.intTimeplayed first
- Next wizard, then by Participant.boolCompleted==false, and longest Participant.intTimeplayed first
- Participant.boolSpectator=true after killed or completed
- HUD zombie countdown
  
  **Back Matter**
- Participants cannot spectate after game time reached
- Anyone can view leaderboard
- Remove all Participants from internal table
  
  **Back Audience**
- Model, plugin or service
- Day 4 - Timed Challenge
  
  **Front Audience**
- Team Cookie, YouTube gameplay
  
  **Front Matter**
- No maximum participants
- Bot places obby during wait time
- Participants teleported to tower after wait time
- [Todo: Participant randomly chosen as wizard]
  
  **Back Matter**
- Participants leaderboard
- Participants who completed ranks based on time taken
- [Todo: Wizard ranks below Participants who completed before time reached]
- [Todo: Wizard ranks above Participants that are killed or failed to complete before time reached]
- Leaderboard gets updated after time reached
  
  **Back Audience**
- Game, model or plugin
- Day 5 - RoDevs Commission from Discord
  
  **Front Audience**
- Boomkid44#6140
- R$300 Robux and a mention
  
  **Front Matter**
- Display ranking on leaderstats and parts
- Each player gets a point every X minutes while in game, and if you get 5 points you will rank up
- Configurable intervalMins, leaderstatsEnabled
  
  **Back Matter**
- No persistence
- Module script with external function that returns a table of players sorted by points
- Each player has its own timer
  
  **Back Audience**
- Script or showcase
- Day 5 - Daily Challenge
  
  Front Audience
- DevForum
  
  Front Matter
- Local player daily tasks
  
  Back Matter
- Local player can log out / in to continue daily tasks
  
  Back Audience
- Model, or plugin
- Day 6 - Localized Tutorial
  
  Front Audience
- DevForum
  
  Front Matter
- Local player trigger tutorial first time
  
  Back Matter
- Use a combination of Gui and Cutscene
- Tutorial available for subsequent plays, but not cutscene
  
  Back Audience
- Model, or plugin
- Day 7 - Mover and Deliver Parcel
  
  Front Audience
- DevForum
  
  Front Matter
- Docks with conveyor belt (similar to Farmhouse)
- Starter to be given by NPC
  
  Back Matter
- Parcel (tagged to Local player) to be collected from random point and placed inventory
- Equip parcel and click on conveyor belt to deliver
  
  Back Audience
- Model or plugin
- Day 8 - Builder and Chop Wood
  
  Front Audience
- DevForum
  
  Front Matter
- Chop tree similar to Farmhouse
- Starter to be given by NPC
  
  Back Matter
- Chop a random number of trees
  
  Back Audience
- Model or plugin
- Day 9 - VIP, Builder and Mover
  
  Front Audience
- DevForum
  
  Front Matter
- Appears at Local player hotel entrance.
- Starter to be given by Vehicle (non-NPC)
- VIP gives new facility, i.e. swimming pool, etc
- Builder gives new floor, i.e. 3 to 6
- Mover gives upgrade to floow, i.e. banner placed outside windows, on the floor.
  
  Back Matter
- May appear more than once per day, randomly, with higher chance if player has done tasks required by NPC.
- Upgrades improve cashflow
  
  Back Audience
- Model or plugin
- Day 10 - Stock Market
  
  Front Audience
  
  Front Matter
  
  Back Matter
  
  Back Audience
- Day 11 - Neural Net