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



