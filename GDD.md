# Game Design Document: Leap of Faith (מלך הביצה)

## 1. Summary
- **Game Title:** Leap of Faith OR King of the Swamp
- **Genre:** Digital Board Game / Strategy / Turn-Based Tactics
- **Players:** 2-4 players
- **Duration:** ~45 mins
- **Platform:** PC, Web
- **Target Audience:** Family, casual strategy gamers, 2-4 players

## 2. Game Overview

### 2.1 Concept
Frogs in a swamp compete to be crowned King by being the first to catch three fireflies. Players must plan their movements in advance, leading to chaotic collisions and strategic blunders.

### 2.2 Gameplay Pillars
- **Blind Programming (Hidden Planning):**
  Players must commit to a sequence of up to three moves in total silence. This creates a high-stakes mental game where you are not just navigating the board, but trying to outguess the intentions of your rivals without any communication.
- **Synchronized Chaos (Simultaneous Resolution):**
  The game removes traditional turn-taking; all frogs leap at the same time in three distinct stages. This leads to high-tension moments where perfectly laid plans are instantly derailed by mid-air collisions or disappearing targets.
- **Kinetic Interaction (Chaos Management):**
  The board is a reactive environment where physical contact has consequences. Collisions cancel your remaining momentum and force emergency bumps to adjacent squares, requiring players to be resilient and adapt their strategy on the fly.
- **Tactical Evolution (Abilities & Power-Ups):**
  While the goal is eating fireflies, scavenging for flies grants one-time special jumps (Double, Diagonal, Knight moves). These allow for direct flight paths that bypass the usual movement restrictions and collision risks of the swamp.

## 3. Mechanics & Rules

### 3.1 The Board
- **Grid:** 9x9 coordinate system
- **Starting Positions:** Frogs start at designated corners/edges based on player count, four flies are placed at designated central points, and one firefly is placed at the exact middle.
- **2-player game:** The firefly starts as a larva for 3 rounds.

### 3.2 The Turn Loop (The Round)
Each round consists of three distinct phases:

1. **Planning Phase (Hidden)**
   - Players select up to 3 Jump Cards from their hand (Normal, Special, or Stop)
   - Players arrange them in a Stack (1st, 2nd, 3rd jump)
   - **UI Requirement:** A Ready button for each player
   - **Timer:** Optional turn timer (default 60s). If a player does not ready up, they stand in place.
   - Can’t select illegal moves. The Ready button is press-able only if the moves are legal. Players can’t move outside of the grid.

2. **Jump Loop (Sequential Processing)**
   The engine iterates through the three jump slots provided in the planning phase. Each jump follows this internal logic:

   - **Step A: Target Calculation**
     - Calculate the `(x, y)` target coordinates for every frog currently in the round.
     - **Special Jumps:** If a player used a special card (Double, Diagonal, Knight), the engine treats this as a teleport to the final square; no collisions or eating can occur on any intermediate tiles.

   - **Step B: Conflict Detection**
     - Identify any tiles where `Count(Frogs) > 1` at the end of the jump.
     - Identify any tiles where a frog is landing on a Fly, Firefly, or Poop.

   - **Step C: The Great Leap (Visual Execution)**
     - All frogs animate their movement to their targets simultaneously.

   - **Step D: Collision & Item Resolution**
     - For those not in collision:
       - **Firefly:** +1 point for the player; remove the firefly from the board.
       - **Fly:** Add 1 random Special Jump card to the player’s hand; remove the fly.
       - **Poop:** The frog stops immediately; all future jumps for this player are cancelled, and the poop is removed.
     - If a collision occurred:
       - Takes place after those not in collision.
       - Affected frogs are marked as In Collision.
       - All future jumps for these frogs are deleted/cancelled.
       - If frogs collided on an item (Fly/Firefly), that item is scared away and respawns elsewhere next round.
       - See conflict resolution.

3. **Cleanup/Spawn Phase**
   - Larvae (תולעים) from the previous round become active flies/fireflies.
   - New larvae are spawned randomly on available space.
   - When spawning larvae it should show upon spawning what each larva will become (fly or firefly), and after spawning it remains a larva with no further indication to what it will become (players should remember).
   - **Spawn Rules:** Larvae spawn only on empty tiles (no frogs, flies, fireflies). Flies can spawn on poop.

### 3.3 Conflict Resolution (Collisions)
- **Trigger:** If two or more frogs land on the same tile at the end of a jump
- **Result:**
  - Future planned jumps for the colliding frogs are cancelled
  - Affected players enter a Collision State
  - They must select a single adjacent square to bounce to
  - The bounce happens simultaneously before the next jump sequence continues for others
  - Can be re-triggered if they land again in the same tile
- **Timer:** Like regular turns, but here it will default to 10 secs. Need to figure out how to punish a non-responding player. Easiest way is to select a random direction for them to jump in.
- **Edge Cases:**
  - If two collisions happen simultaneously, all players jump together like a jump in a round.
  - Collision-involved frogs can bounce to a tile occupied by an unrelated frog, making it also part of a collision.

### 3.4 Movement Types
- **Normal Jump:** 1 square in a cardinal direction
- **Double Jump (Special):** 2 squares forward
- **Diagonal (Special):** 1 square diagonally
- **Right/Left Knight Move (Special):** L shape (2 forward, 1 right/left)

### 3.5 Items & Scoring
- **Firefly:** Landing here grants 1 point. First to 3 wins.
- **Fly:** Landing here grants 1 random Special Jump Card.
- **Poop:** Landing on a Poop tile stops the player and cancels remaining jumps, also clears the poop.

### 3.6 Cards & Hand Management
- **Hand Size:** 5 cards max
- **Normal Cards:** 3 cards; always available
- **Special Cards:** 2 cards max; gained from flies, one-time use, then discarded. The deck has 16 cards and is shared across all players: 2 left knights, 2 right knights, 6 diagonal, 6 double jump. We use dynamic RNG with probability, e.g. diagonal with probability `6/16` and so on, keeping track of a deck with dynamic probabilities based on cards drawn and refilling when the deck is empty.
- **Draw Rules:** Landing on a fly grants 1 random special card, immediately added to hand, usable next round.

### 3.7 Edge Cases
- **Empty Stack:** Players may submit fewer than 3 cards; missing slots are treated as Stop.
- **Win Condition:** Since only 1 firefly can be eaten per round, ties are impossible. First player to reach 3 fireflies wins immediately.

### 3.8 Spawn System Details
- **Larva Types:** Spawned larvae type is determined according to the eaten flies. For each eaten fly, a fly larva will spawn for next round; if the firefly is eaten then a firefly larva will spawn.
- **Reveal Rule:** Show the type briefly on spawn, then hide and keep as larva until it matures.
- **Maturation:** Larvae mature at the start of the next Cleanup/Spawn Phase.

### 3.9 Poop Generation
- **Source:** Poop is created when a frog moves from a tile where they ate a fly or firefly.
- **Duration:** Poop remains until a frog lands on it.
- **Flies:** A fly/firefly can spawn on a poop tile;

## 4. UI/UX Design

### 4.1 Game Screens
- **Main Menu:** Play, How to Play, Settings
- **Lobby:** 2-4 player selection (LAN or network), PvP, PvC
- **Game View:** 9x9 Grid, Player Hand (Cards), Scoreboard

### 4.2 Accessibility & Controls
- **Input:** Mouse (drag/drop) and keyboard shortcuts for card placement and rotation
- **Color Safety:** Colorblind-safe icons for flies, fireflies, and larvae types; not color-only
- **Readability:** Scalable UI and clear grid coordinates for planning

### 4.3 User Interactions
- **Drag & Drop:** Move cards into the 1-2-3 planning slots
- **Click to Rotate:** Clicking on a card rotates it by 90 degrees; maybe choose direction by right click/left click
- **Visual Planning:** The board should show the trajectory planned (for the player only)
  - **Ghost Frogs:** When a player drags a card into a slot, show a semi-transparent ghost of the frog at the destination
  - **Trajectory Lines:** Use dotted lines for normal jumps and solid, arced lines for special jumps to emphasize that they bypass the tiles in between
- **Visual Feedback:** Frogs should hop (arc animation) rather than slide
- **Larva Transition:** Visual indicator (e.g., pulsing) when a larva is about to turn into a fly

## 5. Multiplayer & AI
- **Networking Model:** Host-authoritative lockstep; all inputs are collected, validated, then resolved simultaneously.
- **Latency Handling:** Input deadline is the end of the planning timer; late inputs default to standing idle.
- **PvC (AI):** Simple baseline AI that targets nearest fly/firefly and avoids collisions when possible; optional difficulty tiers.

## 6. Art & Audio
- **Art Style:** Stylized 2D cartoon, overview, background animation.
- **Color Palette:** Greens, muddy browns, neon yellows? (fireflies)
- **Sound Effects:** Boing (Jump), Gulp (Eating), Splat (Collision)
- **Dynamic Music:** Theme for each frog, which progresses by number of points the same frog has. More points equals more intensity. Inspired by Wii Tanks.
