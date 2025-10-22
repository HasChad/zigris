# Core Architecture
Start by separating concerns cleanly:
- Game state: The board grid, current piece, next piece, score, level
- Rendering: Drawing to the terminal via libvaxis
- Input handling: Keyboard events
- Game loop: Timing updates and rendering

# Key Things to Handle
The board representation: Use a 2D array (probably 10 wide × 20-22 tall, giving yourself hidden
rows at top for spawning). Store each cell as either empty or a color/piece type.

Piece rotation: This is trickier than it seems. You'll need rotation matrices or hardcoded rotation
states for each tetromino. The Super Rotation System (SRS) has specific kick tables - when a
rotation would collide, it tries several offset positions. You can start simpler, but SRS feels
better.

Timing system: Don't just sleep between frames. Track time deltas and have separate timers for:
- Gravity (piece falling)
- Lock delay (grace period when piece touches ground)
- DAS (delayed auto-shift for held keys)
- Line clear animations

Collision detection: Write a clean function that checks if a piece at position (x,y) with rotation
would overlap the board or boundaries. You'll call this constantly.

# libvaxis-Specific Tips
The library's event loop will be your main loop. You'll be waiting on input events with timeouts to
handle gravity ticks.
Think about your rendering strategy - you probably want to clear and redraw each frame rather than
trying to update only changed cells (simpler, and Tetris is small enough that it won't matter).
Use libvaxis's color capabilities well. Each piece should have distinct colors, and you might want
subtle differences between the active piece and locked pieces.

# Common Gotchas
- Lock delay: Players expect a brief moment when a piece lands before it locks. Without this, the
game feels unforgiving.
- Wall kicks: Test rotation near walls thoroughly. Edge cases will bite you.
- Line clearing: Decide if you'll do instant clear or animate it. If animating, you need to pause
input during the animation.
- Input buffering: Should pressing rotate while a line is clearing queue that input? Up to you, but
think it through.
- Piece generation: Use the "bag" system (shuffle all 7 pieces, distribute, repeat) rather than
pure random. Prevents droughts and floods.
- Start Small
Build incrementally:
- Just draw a static board
- Spawn one piece and make it fall
- Add left/right movement
- Add rotation
- Add collision and locking
- Add line clearing
- Add piece queue, score, etc.

Don't try to build everything at once. Get each piece working before moving on.
The hardest part is usually getting rotation and collision feeling right together.
Test obsessively with pieces in corners and against other pieces.
