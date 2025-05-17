# Ex.No: 10  Implementation of Flappy bird game
### DATE:                                                                            
### REGISTER NUMBER : 212222240094 
### AIM: 
To develop a game flappy bird in Unity 
### Algorithm:
```
Player.cs
1. Initialize SpriteRenderer component.
2. Start sprite animation loop every 0.15 seconds.
3. On enabling the player:
      a. Reset vertical position to 0.
      b. Reset movement direction to zero.
4. Every frame (Update):
      a. If space key or left mouse button pressed, set upward movement direction.
      b. Apply gravity to the vertical direction.
      c. Update player position based on direction.
      d. Tilt the player based on vertical movement speed.
5. Animate player sprite by cycling through sprite frames.
6. On collision with:
      a. Obstacle tag → trigger game over.
      b. Scoring tag → increase score.
```
```
GameManager.cs
1. On awake:
    a. Ensure singleton instance of the game manager.
2. On start:
    a. Pause the game (stop time, disable player controls).
3. Pause() method:
    a. Set game time scale to 0 (freeze game).
    b. Disable player controls.
4. Play() method:
    a. Reset score to zero and update UI.
    b. Hide play button and game over UI.
    c. Enable player controls and start game (set time scale to 1).
    d. Find all existing pipes and destroy them to reset obstacles.
5. GameOver() method:
    a. Show play button and game over UI.
    b. Pause the game.
6. IncreaseScore() method:
      a. Increment score and update UI.
```
```
Parallax.cs
1. On awake:
    a. Get MeshRenderer component of the background.
2. Every frame (Update):
    b. Continuously move the texture offset horizontally to create parallax scrolling effect.
```
```
Pipes.cs
1. On start:
    a. Calculate left boundary off-screen for pipe destruction.
2. Position top pipe upwards by half the gap.
    a. Position bottom pipe downwards by half the gap.
3. Every frame (Update):
    a. Move the pipe leftwards by speed.
    b. If pipe moves beyond left boundary, destroy the pipe object.
```
```
Spawner.cs
1. When enabled:
    a. Start repeatedly invoking the Spawn() method at a fixed spawn rate.
2. When disabled:
    a. Cancel any repeating spawn invocations.
3. Spawn() method:
    a. Instantiate a new pipe prefab at the spawner's position.
    b. Randomly adjust the vertical position of the pipe within min and max height.
```  
### Program:
Player.cs
```
using UnityEngine;

public class Player : MonoBehaviour
{
    public Sprite[] sprites;
    public float strength = 5f;
    public float gravity = -9.81f;
    public float tilt = 5f;

    private SpriteRenderer spriteRenderer;
    private Vector3 direction;
    private int spriteIndex;

    private void Awake()
    {
        spriteRenderer = GetComponent<SpriteRenderer>();
    }

    private void Start()
    {
        InvokeRepeating(nameof(AnimateSprite), 0.15f, 0.15f);
    }

    private void OnEnable()
    {
        Vector3 position = transform.position;
        position.y = 0f;
        transform.position = position;
        direction = Vector3.zero;
    }

    private void Update()
    {
        if (Input.GetKeyDown(KeyCode.Space) || Input.GetMouseButtonDown(0)) {
            direction = Vector3.up * strength;
        }

        // Apply gravity and update the position
        direction.y += gravity * Time.deltaTime;
        transform.position += direction * Time.deltaTime;

        // Tilt the bird based on the direction
        Vector3 rotation = transform.eulerAngles;
        rotation.z = direction.y * tilt;
        transform.eulerAngles = rotation;
    }

    private void AnimateSprite()
    {
        spriteIndex++;

        if (spriteIndex >= sprites.Length) {
            spriteIndex = 0;
        }

        if (spriteIndex < sprites.Length && spriteIndex >= 0) {
            spriteRenderer.sprite = sprites[spriteIndex];
        }
    }

    private void OnTriggerEnter2D(Collider2D other)
    {
        if (other.gameObject.CompareTag("Obstacle")) {
            GameManager.Instance.GameOver();
        } else if (other.gameObject.CompareTag("Scoring")) {
            GameManager.Instance.IncreaseScore();
        }
    }

}
```
GameManager.cs
```
using UnityEngine;
using UnityEngine.UI;

[DefaultExecutionOrder(-1)]
public class GameManager : MonoBehaviour
{
    public static GameManager Instance { get; private set; }

     [SerializeField]private Player player;
     [SerializeField]private Spawner spawner;
     [SerializeField]private Text scoreText;
     [SerializeField]private GameObject playButton;
     [SerializeField]private GameObject gameOver;

    public int score { get; private set; } = 0;

    private void Awake()
    {
        if (Instance != null) {
            DestroyImmediate(gameObject);
        } else {
            Instance = this;
        }
    }

    private void OnDestroy()
    {
        if (Instance == this) {
            Instance = null;
        }
    }

    private void Start()
    {
        Pause();
    }

    public void Pause()
    {
        Time.timeScale = 0f;
        player.enabled = false;
    }

    public void Play()
{
    score = 0;
    scoreText.text = score.ToString();

    playButton.SetActive(false);
    gameOver.SetActive(false);

    Time.timeScale = 1f;
    player.enabled = true;

    Pipes[] pipes = FindObjectsByType<Pipes>(FindObjectsSortMode.None);

    for (int i = 0; i < pipes.Length; i++) {
        Destroy(pipes[i].gameObject);
    }
}

    public void GameOver()
    {
        playButton.SetActive(true);
        gameOver.SetActive(true);

        Pause();
    }

    public void IncreaseScore()
    {
        score++;
        scoreText.text = score.ToString();
    }

}
```
Parallax.cs
```
using UnityEngine;

public class Parallax : MonoBehaviour
{
    public float animationSpeed = 1f;
    private MeshRenderer meshRenderer;

    private void Awake()
    {
        meshRenderer = GetComponent<MeshRenderer>();
    }

    private void Update()
    {
        meshRenderer.material.mainTextureOffset += new Vector2(animationSpeed * Time.deltaTime, 0);
    }

}

```
Pipes.cs
```
using UnityEngine;

public class Pipes : MonoBehaviour
{
    public Transform top;
    public Transform bottom;
    public float speed = 5f;
    public float gap = 3f;

    private float leftEdge;

    private void Start()
    {
        leftEdge = Camera.main.ScreenToWorldPoint(Vector3.zero).x - 1f;
        top.position += Vector3.up * gap / 2;
        bottom.position += Vector3.down * gap / 2;
    }

    private void Update()
    {
        transform.position += speed * Time.deltaTime * Vector3.left;

        if (transform.position.x < leftEdge) {
            Destroy(gameObject);
        }
    }

}
```
Spawner.cs
```
using UnityEngine;

public class Spawner : MonoBehaviour
{
    public GameObject prefab;
    //public Pipes prefab;
    public float spawnRate = 1f;
    public float minHeight = -1f;
    public float maxHeight = 2f;
    public float verticalGap = 3f;

    private void OnEnable()
    {
        InvokeRepeating(nameof(Spawn), spawnRate, spawnRate);
    }

    private void OnDisable()
    {
        CancelInvoke(nameof(Spawn));
    }

    private void Spawn()
    {
        GameObject pipes = Instantiate(prefab, transform.position, Quaternion.identity);
        pipes.transform.position += Vector3.up * Random.Range(minHeight, maxHeight);
    }

}
```
### Output:
![image](https://github.com/user-attachments/assets/11ed6b27-8603-4d05-866d-e182abc50001)
![image](https://github.com/user-attachments/assets/bdcddd97-54a2-473f-832a-0e8456b6a0f7)
![image](https://github.com/user-attachments/assets/383791ab-a488-4f68-b3a0-69c64875c1c7)



### Result:
Thus the game was developed using Unity and it is successfully executed.
