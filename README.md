//Player.cs
using System;
using UnityEditor.Tilemaps;
using UnityEngine;
using UnityEngine.Windows;
using Input = UnityEngine.Input;

public class Player : MonoBehaviour
{
    [SerializeField]private Animator _animator;
     [SerializeField]private SpriteRenderer spriteRenderer;
    float horizontalInput;
    float inputHorizontal;
    float inputVertical;
    float moveSpeed = 10f;
    Rigidbody2D rb;
    public event Action PlayerDied;

    void Start()
    {
        rb = gameObject.GetComponent<Rigidbody2D>();
        rb = GetComponent<Rigidbody2D>();
    }

    void Update()
    {
        horizontalInput = Input.GetAxis("Horizontal");
    }

    [Obsolete]
    void FixedUpdate()
    {
        rb.velocity = new Vector2(horizontalInput * moveSpeed, rb.velocity.y);
        inputHorizontal = Input.GetAxisRaw("Horizontal");
        inputVertical = Input.GetAxisRaw("Vertical");

        if(inputHorizontal != 0)
        {
            rb.AddForce(new Vector2(inputHorizontal * moveSpeed, 0f));
        }

        if(inputHorizontal > 0)
        {
            gameObject.transform.localScale = new Vector3(1, 1, 1);
        }

        if(inputHorizontal < 0)
        {
            gameObject.transform.localScale = new Vector3(-1, 1, 1);
        }
    }

    private void OnCollisionEnter2D(Collision2D collision)
    {
        if (collision.gameObject.CompareTag("Bomb"))
        {
            PlayerDied.Invoke();
            Destroy(this.gameObject);
        }
    }
   
}

//Music.cs
using UnityEngine;

public class music : MonoBehaviour
{
    [SerializeField] AudioSource musicScource;

    [Header("--------------------------")]
    public AudioClip background;

    private void Start()
    {
        musicScource.clip = background;
        musicScource.Play();
    }
}

//mainmenu.cs
using UnityEngine;
using UnityEngine.SceneManagement;
public class mainmenu : MonoBehaviour
{
   public void PlayerGame()
    {
        SceneManager.LoadSceneAsync("GameScene");
    }
    public void QuitGame()
    {
        Application.Quit();
    }
}

//GameController.cs
using System;
using TMPro;
using UnityEngine;
using UnityEngine.SceneManagement;

public class GameController : MonoBehaviour
{
    [SerializeField] private Player player;
    public Canvas GameOverCanvas;
    public TMP_Text TimerText;

    // ✔ Added Data Structure: Array to store the last 5 time scores
    private float[] timeScores = new float[5];
    private int scoreIndex = 0;

    private void Awake()
    {
        if (player != null)
        {
            player.PlayerDied += WhenPlayerDies;
        }

        if (GameOverCanvas.gameObject.activeSelf)
        {
            GameOverCanvas.gameObject.SetActive(false);
        }
    }

    void WhenPlayerDies()
    {
        GameOverCanvas.gameObject.SetActive(true);

        float finalTime = (float)Math.Round(Time.timeSinceLevelLoad, 2);
        TimerText.text = "You Lasted: " + finalTime;

        // ✔ Store score in the array, wrap around after length
        timeScores[scoreIndex] = finalTime;
        scoreIndex++;
        if (scoreIndex >= timeScores.Length)
            scoreIndex = 0;

        // ✔ Optional: Print time scores for testing
        Debug.Log("=== Time Score History ===");
        for (int i = 0; i < timeScores.Length; i++)
        {
            Debug.Log("Score[" + i + "] = " + timeScores[i]);
        }

        if (player != null)
        {
            player.PlayerDied -= WhenPlayerDies;
        }
    }

    public void RetryClicked()
    {
        SceneManager.LoadScene(SceneManager.GetActiveScene().name);
    }
}

//FallingBombs.cs
using UnityEngine;

public class FallingBomb : MonoBehaviour
{
    private void OnCollisionEnter2D(Collision2D collision)
    {
        Destroy(this.gameObject);
    }
}

//BombFallController.cs
using UnityEngine;

public class NewMonoBehaviourScript : MonoBehaviour
{
    float wait = 0.4f;
    public GameObject fallingBomb;
    void Start()
    {
        InvokeRepeating("Fall", wait, wait);
    }

    void Fall()
    {
        Instantiate(fallingBomb, new Vector3(Random.Range(-10,10), 10, 0), Quaternion.identity);
    }
}
