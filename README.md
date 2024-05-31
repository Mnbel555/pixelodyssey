# pixelodyssey
## Introduction
This practical work is delivery document for class of Video Game Development Techniques of the course of Engineering in Digital Game Development, proposed by Professor Daniel Nogueira. The work has several tasks and objectives given by teacher

# The game "[PixelOdyssey](https://github.com/Mnbel555/PIxlOdyssey)"
![character](https://github.com/Mnbel555/pixelodyssey/assets/125232753/74e83f43-766b-4570-a7f1-717f02884061)


## Introduction
Pixel odyssey is a side-scrolling video game  developed using the MonoGame framework in C#; the player kills the enemies and enters new worlds (levels) to find hidden key to find vault for the cure of virus. The objective of the game is to kill enemies to move further into new levels and maintain scores points. Although this is not a complete game just an implementation of a side scrolling game

## Mechanism 
 The game features a player-controlled character navigating through levels while defeating enemies.. If Player interact with enemy, he loses a life.

### Objective
kill enemies to enter new realms.

### Gameplay
- Control Cruis with Arrow keys.
- Press the spacebar to jump and Z to attack, C to slide and X to kick.
- Navigate through levels, defeat enemies.
  
## Instructions 
"To play Pixelodyssey, use the Arrow keys to control Player movement. The objective is to defeat enemies. If Player dont attacks a ghost, then it gets killed. Keep an eye on your score as you spend time to see how well you're doing.

## Organization of Files and Folders
![Screenshot 2024-05-21 224335](https://github.com/Mnbel555/pixelodyssey/assets/125232753/2acbf29b-84a7-4780-a2b7-4d8336a5e87f)


lets go through some of folder and files, `Content`  Content folder are  the  assets  that the  Monogame  framework recognizes  to locate and load images, audios and other files that are not directly part of the game code. all other folders contain sprites of given attribute within content folder.

##  Analysis of Code
In this part, code blocks are described like addition of sprites, sounds, scores,controls of game etc.

 <details>
    <summary>Audio Initialization</summary>
initializing sound
   
```csharp
//character.cs
  public void LoadContent(ContentManager content)
    {
        _slashSoundEffect = content.Load<SoundEffect>("slashing"); // Loading sound effect
    }

```
use of sound during attack

```csharp

if (_isSlashing && _animationManager.CurrentFrame == 0)
{
    _slashSound.Play();
}

```

</details>

<details>
    <summary>Keyboard control</summary>
  
  
Check for action inputs first, all inputs are assigned and conditions checked respectively

```csharp
// Get the current state of the keyboard
var keyboardState = Keyboard.GetState();

// Check if the right arrow key and Z key are pressed
bool isRightPressed = keyboardState.IsKeyDown(Keys.Right);
bool isZPressed = keyboardState.IsKeyDown(Keys.Z);

// Check if the Z key is pressed and no slashing action is already in progress
if (isZPressed && !(_isSlashing || _isRunSlashing))
{
    // Check if the right arrow key is pressed along with Z key
    if (isRightPressed)
    {
        _isRunSlashing = true;
        _animationManager.Play(_animations["RunSlashing"]);
        _velocity.X = _speed; // Move forward while run slashing
    }
    else
    {
        _isSlashing = true;
        _animationManager.Play(_animations["Slashing"]);
    }
}
// Check if the X key is pressed and no kicking action is already in progress
else if (keyboardState.IsKeyDown(Keys.X) && !_isKicking)
{
    _isKicking = true;
    _animationManager.Play(_animations["Kicking"]);
}
// Check if the C key is pressed and no sliding action is already in progress
else if (keyboardState.IsKeyDown(Keys.C) && !_isSliding)
{
    _isSliding = true;
    _animationManager.Play(_animations["Sliding"]);
    _velocity.X = _speed * 4; // Move forward while sliding
}
// Check if only the right arrow key is pressed and no other action is in progress
else if (isRightPressed && !_isJumping && !_isKicking && !_isSliding && !_isSlashing && !_isRunSlashing)
{
    _velocity.X = _speed;
    _animationManager.Play(_animations["Walking"]);
}
// Check if no action is in progress
else if (!_isJumping && !_isKicking && !_isSliding && !_isSlashing && !_isRunSlashing)
{
    _animationManager.Play(_animations["IdleBlinking"]);
}

```

Handle jump logic and other action logics

```csharp
// Check if the space key is pressed and the character is not already jumping
if (keyboardState.IsKeyDown(Keys.Space) && !_isJumping)
{
    _isJumping = true;
    _velocity.Y = _jumpSpeed;
    _animationManager.Play(_animations["JumpStart"]);
}

// Check if the character is currently kicking
if (_isKicking)
{
    // Check if the kicking animation is complete
    if (_animationManager.CurrentAnimation == _animations["Kicking"] && _animationManager.CurrentFrame == _animationManager.CurrentAnimation.FrameCount - 1)
    {
        _isKicking = false;
        _animationManager.Play(_animations["IdleBlinking"]);
    }
}

// Check if the character is currently slashing
if (_isSlashing)
{
    // Check if the slashing animation is complete
    if (_animationManager.CurrentAnimation == _animations["Slashing"] && _animationManager.CurrentFrame == _animationManager.CurrentAnimation.FrameCount - 1)
    {
        _isSlashing = false;
        _animationManager.Play(_animations["IdleBlinking"]);
    }
}

// Check if the character is currently running and slashing
if (_isRunSlashing)
{
    // Check if the running and slashing animation is complete
    if (_animationManager.CurrentAnimation == _animations["RunSlashing"] && _animationManager.CurrentFrame == _animationManager.CurrentAnimation.FrameCount - 1)
    {
        _isRunSlashing = false;
        _animationManager.Play(_animations["IdleBlinking"]);
    }
}

// Check if the character is currently sliding
if (_isSliding)
{
    // Check if the sliding animation is complete
    if (_animationManager.CurrentAnimation == _animations["Sliding"] && _animationManager.CurrentFrame == _animationManager.CurrentAnimation.FrameCount - 1)
    {
        _isSliding = false;
        _animationManager.Play(_animations["IdleBlinking"]);
    }
}

```
</details>

<details>
    <summary>Loading textures</summary>
  
  
 
The LoadContent method initializes the _character object, sets up event handlers for animation completion, and loads necessary content, including the character's slash sound effect.

```csharp
  // Load the background textures
backgroundTextures = new List<Texture2D>();
string[] backgroundFiles = Directory.GetFiles("Content/Background", "*.png");
foreach (var file in backgroundFiles)
{
    // Open the file 
    using (var stream = new FileStream(file, FileMode.Open))
    {
        // Load the texture from the 
        var texture = Texture2D.FromStream(game.GraphicsDevice, stream);
        
        // Add the original texture and its horizontally flipped version to the list
        backgroundTextures.Add(texture);
        backgroundTextures.Add(Texture2DExtensions.FlipHorizontally(texture, game.GraphicsDevice));
    }
}

// Load other textures and assets
heartTexture = game.Content.Load<Texture2D>("lifeline");
pathTexture = game.Content.Load<Texture2D>("hillPath");
slashSoundEffect = game.Content.Load<SoundEffect>("slashing");
trophyTexture = game.Content.Load<Texture2D>("trophy");
_font = game.Content.Load<SpriteFont>("ButtonFont");

```
Handling of backgrounds of levels
```csharp
   // Load the next background texture
        if (_currentLevel <= 2)
        {
            // For the first two levels, use the predefined backgrounds
            if (backgroundTextures.Count >= _currentLevel * 2)
            {
                Texture2D nextBackground = backgroundTextures[(_currentLevel - 1) * 2];
                Texture2D flippedBackground = backgroundTextures[(_currentLevel - 1) * 2 + 1];

                backgroundTextures.Clear();
                backgroundTextures.Add(nextBackground);
                backgroundTextures.Add(flippedBackground);
            }
        }
        else
        {
            // For levels beyond the second, select a random background from the list
            Random random = new Random();
            string[] backgroundFiles = Directory.GetFiles("Content/Background", "*.png");
            int randomIndex = random.Next(0, backgroundFiles.Length);
            using (var stream = new FileStream(backgroundFiles[randomIndex], FileMode.Open))
            {
                Texture2D newBackground = Texture2D.FromStream(game.GraphicsDevice, stream);
                Texture2D flippedBackground = Texture2DExtensions.FlipHorizontally(newBackground, game.GraphicsDevice);

                backgroundTextures.Clear();
                backgroundTextures.Add(newBackground);
                backgroundTextures.Add(flippedBackground);
            }
        }

```
</details>

<details>
    <summary>Scores</summary>
  

                                                                                                                                                  
Here,When the player completes a level or achieves a notable milestone in the game, the game logic calculates the time taken to complete that level or milestone. This time duration is represented as a TimeSpan, Once the TimeSpan representing the score is obtained, it is passed to the AddScore method of the ScoreManager instance. 
                                                                                                                                                  
```csharp


    public ScoreManager()
    {
        // Load high scores from file when ScoreManager is instantiated
        _highScores = LoadScores();
    }

    private List<TimeSpan> LoadScores()
    {
        List<TimeSpan> scores = new List<TimeSpan>();
        // Check if the high scores file exists
        if (File.Exists(_scoreFilePath))
        {
            // Read all lines from the file
            var lines = File.ReadAllLines(_scoreFilePath);
            foreach (var line in lines)
            {
                // Try parsing each line into a TimeSpan (representing a score)
                if (TimeSpan.TryParse(line, out TimeSpan score))
                {
                    scores.Add(score); // Add successfully parsed score to the list
                }
            }
        }
        // Return top 5 high scores in descending order
        return scores.OrderByDescending(x => x).Take(5).ToList();
    }

    // Add a new score to the high scores list
    public void AddScore(TimeSpan score)
    {
        _highScores.Add(score); // Add the new score
        // Keep only the top 5 high scores and order them in descending order
        _highScores = _highScores.OrderByDescending(x => x).Take(5).ToList();
        SaveScores(); // Save the updated high scores to the file
    }

    // Save the high scores list to the file
    private void SaveScores()
    {
        File.WriteAllLines(_scoreFilePath, _highScores.Select(x => x.ToString()));
    }

    // Get the top high scores
    public List<TimeSpan> GetHighScores()
    {
        return _highScores; // Return the list of high scores
    }
}


```

In the Draw method of the PlayState class, the score is displayed on the screen using SpriteBatch.DrawString                                                                                                                                                                                                                                                                
```csharp
string timerText = $"Time: {_timer.Minutes:D2}:{_timer.Seconds:D2}:{_timer.Milliseconds:D3}";
Vector2 timerSize = _font.MeasureString(timerText);
spriteBatch.DrawString(_font, timerText, new Vector2(10, game.GraphicsDevice.Viewport.Height - timerSize.Y - 10), Color.White);


```    
</details>

<details>
    <summary>Enemies Movement and Collision</summary>
It iterates through each enemy and checks if the distance between the character and the enemy is less than 150 units.If the distance check passes and the character's bounding box intersects with the enemy's bounding box, and the character is in one of the attack states (IsSlashing, IsKicking, or IsSliding), it removes the enemy from the list of enemies.
  
```csharp
    private void CheckCollisions()
        {
            // Existing enemy collision checks
            for (int i = _enemies.Count - 1; i >= 0; i--)
            {
                if (Vector2.Distance(_character.Position, _enemies[i].Position) < 150)
                {
                    if (_character.BoundingBox.Intersects(_enemies[i].BoundingBox) && (_character.IsSlashing || _character.IsKicking || _character.IsSliding))
                    {
                        _enemies.RemoveAt(i);
                    }
                }
            }

```
This method updates the enemy's behavior based on the player's position. It determines whether to attack or walk towards the player, flips the enemy sprite accordingly, and triggers events when appropriate.

```csharp
public void Update(GameTime gameTime, Vector2 playerPosition)
{
    // Calculate the direction and distance to the player
    Vector2 direction = playerPosition - _position;
    float distance = direction.Length();

    // Check if the player is within attack range
    if (distance < _attackRange)
    {
        // If not already attacking, start attacking
        if (!_isAttacking)
        {
            _isAttacking = true;
            _hasSlashed = false;
            _animationManager.Play(_animations["Slashing"]);
            System.Diagnostics.Debug.WriteLine("Playing Slashing Animation");
        }
    }
    else
    {
        // If currently attacking and not finished slashing, return
        if (_isAttacking && _animationManager.CurrentAnimation == _animations["Slashing"] && _animationManager.CurrentFrame < _animationManager.CurrentAnimation.FrameCount - 1)
        {
            return;
        }

        // If not within attack range, stop attacking and start walking
        _isAttacking = false;
        _animationManager.Play(_animations["Walking"]);
        System.Diagnostics.Debug.WriteLine("Playing Walking Animation");
        direction.Normalize();
        _position += direction * _speed;
    }

    // Flip the enemy sprite based on the direction of movement
    if (direction.X > 0)
    {
        _animationManager.SpriteEffect = SpriteEffects.None;
    }
    else
    {
        _animationManager.SpriteEffect = SpriteEffects.FlipHorizontally;
    }

    // Adjust the Y-coordinate to keep the enemy on the track and lower it
    _position.Y = playerPosition.Y + _yOffset;

    // Update the position and animation manager
    _animationManager.Position = _position;
    _animationManager.Update(gameTime);

    // Trigger the OnSlashComplete event if the slashing animation is complete and hasn't been triggered yet
    if (_isAttacking && !_hasSlashed && _animationManager.CurrentAnimation == _animations["Slashing"] && _animationManager.CurrentFrame == _animationManager.CurrentAnimation.FrameCount - 1)
    {
        OnSlashComplete?.Invoke();
        _hasSlashed = true;
        _isAttacking = false;
    }
}

```
</details>



